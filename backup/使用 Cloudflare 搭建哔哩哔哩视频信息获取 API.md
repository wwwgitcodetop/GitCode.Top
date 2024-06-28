# bilibiliInfo
这是一个基于 Cloudflare Workers 部署的，获取访客信息的API。

部署后可直接访问使用或者当作一个API，返回JSON格式的信息。

GitHub：[https://github.com/molikai-work/bilibiliInfoApi](https://github.com/molikai-work/bilibiliInfoApi)

## 部署
1. 复制本篇教程最后的源代码。
2. 登录到 [Cloudflare](https://dash.cloudflare.com/) 控制台。
3. 选择侧边栏的 [Workers 和 Pages](https://dash.cloudflare.com/?to=/:account/workers-and-pages) ，点击右上角的创建按钮。
4. 在创建应用程序页面选择Workers => 创建 Workers按钮，设置域名前缀后点击保存 => 完成按钮。
5. 编辑刚刚新建的Workers的代码，把在第一步复制的源代码粘贴进去，再点击右上角的部署按钮，等等成功后可退出。、
6. 访问刚刚设置的地址（如果访问不了设置自己的域名后再试），完成。

## 使用示例
```
https://example.com/?value=<id>
```

其中，`id`填哔哩哔哩视频的ID号。

## 源代码
```
// ?value=BV1rC411L7BH

addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request));
});

async function getAvBvInfo(avBv) {
  const url = `https://www.bilibili.com/video/${avBv}`;

  const response = await fetch(url, {
    headers: {
      'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.9388.240 Safari/537.36 Edge/13.10586',
      'Accept-Language': 'zh,zh-CN;q=0.9,en;q=0.7,en-GB;q=0.6,en-US;q=0.5',
      'Referer': 'https://www.example.com/'
    } 
  });

  if (!response.ok) {
    throw new Error(`Bilibili 请求未能成功: ${response.statusText}`);
  }

  const text = await response.text();
  // 匹配视频标题
  const Title = text.match(/<h1.*?data-title="*?".*?title="*?".*?class="video-title special-text-indent".*?>(.*?)<\/h1>/);
  // 匹配视频作者
  const Author = text.match(/<meta.*?data-vue-meta="*?".*?itemprop="author".*?name="author".*?content="(.*?)".*?>/);
  // 匹配视频作者粉丝数
  const Fans = text.match(/"fans":"(.*?)"/);
  // 匹配视频作者关注数
  const Friend = text.match(/"friend":"(.*?)"/);
  // 匹配视频作者 uid 号
  const Mid = text.match(/"mid":"(.*?)"/);
  // 匹配视频作者简介
  const Sing = text.match(/"sign":"(.*?)"/);
  // 匹配视频播放量信息
  const View = text.match(/<div.*?class="view-text".*?>(.*?)<\/div>/);
  // 匹配视频弹幕数量信息
  const Dm = text.match(/<div.*?class="dm-text".*?>(.*?)<\/div>/);
  // 匹配视频播放量信息
  const Pubdate = text.match(/<div.*?class="pubdate-ip-text".*?>(.*?)<\/div>/);
  // 匹配视频点赞信息
  const Like = text.match(/<span.*?class="video-like-info video-toolbar-item-text">(.*?)<\/span>/);
  // 匹配视频投币信息
  const Coin = text.match(/<span.*?class="video-coin-info video-toolbar-item-text".*?>(.*?)<\/span>/);
  // 匹配视频收藏信息
  const Fav = text.match(/<span.*?class="video-fav-info video-toolbar-item-text".*?>(.*?)<\/span>/);
  // 匹配视频分享信息
  const Share = text.match(/<span.*?class="video-share-info video-toolbar-item-text".*?>(.*?)<\/span>/);
  // 匹配视频简介信息
  const Synopsis = text.match(/<span.*?class="desc-info-text".*?>(.*?)<\/span>/);
  // 匹配视频关键字信息
  const Keywords = text.match(/<meta.*?itemprop="keywords".*?name="keywords".*?content="(.*?)">/);
  // 匹配视频 BV 号
  const Bvid = text.match(/"bvid":"(.*?)"/);
  // 匹配视频封面链接
  const Cover = text.match(/"pic":"(.*?)"/);

  if (Keywords) {
    return {
      title: Title ? Title[1] : null,
      author: Author ? Author[1] : null,
      fans: Fans ? Fans[1] : null,
      friend: Friend ? Friend[1] : null,
      mid: Mid ? Mid[1] : null,
      sing: Sing ? Sing[1] : null,
      view: View ? View[1] : null,
      dm: Dm ? Dm[1] : null,
      pubdate: Pubdate ? Pubdate[1] : null,
      like: Like ? Like[1] : null,
      coin: Coin ? Coin[1] : null,
      fav: Fav ? Fav[1] : null,
      share: Share ? Share[1] : null,
      synopsis: Synopsis ? Synopsis[1] : null,
      keywords: Keywords ? Keywords[1] : null,
      bvid: Bvid ? Bvid[1] : null,
      cover: Cover ? Cover[1] : null
    };
  } else {
    throw new Error('未能找到相应的影片资讯，请核对 AV 或 BV 号是否准确。');
  }  
}

async function handleRequest(request) {
  if (request.method === 'OPTIONS') {
    return new Response(null, {
      headers: {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
        'Access-Control-Allow-Headers': 'Content-Type',
        'Access-Control-Max-Age': '86400',
      },
    })
  }

  try {
    const url = new URL(request.url);
    const avBv = url.searchParams.get('value');

    if (!avBv) {
      throw new Error('请提供 Bilibili 影片的 AV 或 BV 号。');
    }

    // 获取视频信息
    const videoInfo = await getAvBvInfo(avBv);

    // 视频作者
    const Author = videoInfo.author;
    // 视频作者粉丝
    const Fans = videoInfo.fans === "0" ? 0 : videoInfo.fans;
    // 视频作者关注
    const Friend = videoInfo.friend === "0" ? 0 : videoInfo.friend;
    // 视频作者 uid
    const Mid = videoInfo.mid;
    // 视频作者简介
    const Sing = videoInfo.sing === "" ? "-" : videoInfo.sing;
    // 视频标题
    const Title = videoInfo.title;
    // 视频播放量
    const View = videoInfo.view === "0" ? 0 : videoInfo.view;
    // 视频弹幕数
    const Dm = videoInfo.dm === "0" ? 0 : videoInfo.dm;
    // 视频发布时间
    const Pubdate = videoInfo.pubdate;
    // 视频点赞
    const Like = videoInfo.like === "点赞" ? 0 : videoInfo.like;
    // 视频投币
    const Coin = videoInfo.coin === "投币" ? 0 : videoInfo.coin;
    // 视频收藏
    const Fav = videoInfo.fav === "收藏" ? 0 : videoInfo.fav;
    // 视频分享
    const Share = videoInfo.share === "分享" ? 0 : videoInfo.share;
    // 视频简介
    const Synopsis = videoInfo.synopsis === null ? "-" : videoInfo.synopsis;
    // 视频关键字
    const Keywords = videoInfo.keywords;
    // 视频 BV 号
    const Bvid = videoInfo.bvid;
    // 视频封面链接
    const coverUrl = videoInfo.cover;
    let decodedCoverUrl = coverUrl.replace(/\\u002F/g, '/');
    // 如果封面链接是 http:// 开头，则替换成 https://
    if (decodedCoverUrl.startsWith("http://")) {
      decodedCoverUrl = decodedCoverUrl.replace("http://", "https://");
    }

    // 构建 JSON 响应体
    const responseBody = {
      code: 200,
      msg: '成功获得影片资讯',
      time: Date.now(),
      author: {
        name: Author,
        fans: Fans,
        friend: Friend,
        mid: Mid,
        sing: Sing
      },
      data: {
        title: Title,
        view: View,
        dm: Dm,
        pubdate: Pubdate,
        like: Like,
        coin: Coin,
        fav: Fav,
        share: Share,
        synopsis: Synopsis,
        keywords: Keywords,
        bvid: Bvid,
        coverUrl: decodedCoverUrl
      }
    };

    return new Response(JSON.stringify(responseBody), {
      status: 200,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      }
    });
  } catch (error) {
    let code, msg, status;

    // 设置状态码和消息
    if (error instanceof TypeError) {
      code = 400;
      msg = 'Bad Request';
    } else {
      code = 404;
      msg = error.message;
    }

    // 构建错误 JSON 响应
    const responseBody = {
      code,
      msg,
      time: Date.now()
    };

    return new Response(JSON.stringify(responseBody), {
      status,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      },
    });
  }
}
```
