# whoisXmlApi.com

这是一个基于 Cloudflare Workers 部署的，获取域名信息的 API

部署后可直接访问使用或者当作一个API，返回JSON、XML格式的信息。

你需要先在 whoisxmlapi.com 申请 API 密钥，然后填入代码中的对应位置`const apikey = ""`，在代码的第70行。

GitHub：[https://github.com/molikai-work/whoisXmlApi.com](https://github.com/molikai-work/whoisXmlApi.com)

## 部署
1. 复制本篇教程最后的源代码。
2. 登录到 [Cloudflare](https://dash.cloudflare.com/) 控制台。
3. 选择侧边栏的 [Workers 和 Pages](https://dash.cloudflare.com/?to=/:account/workers-and-pages) ，点击右上角的创建按钮。
4. 在创建应用程序页面选择Workers => 创建 Workers按钮，设置域名前缀后点击保存 => 完成按钮。
5. 编辑刚刚新建的Workers的代码，把在第一步复制的源代码粘贴进去，再点击右上角的部署按钮，等等成功后可退出。、
6. 访问刚刚设置的地址（如果访问不了设置自己的域名后再试），完成。

## 使用示例
```
https://example.com/?domain=<domain>&format=<format>
```

其中，domain填要查询的目标裸域名，format可选填返回为`json`或`xml`格式。

## 源代码
```
// ?domain=cloudflare.com&format=json

addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  if (request.method === 'OPTIONS') {
    return new Response(null, {
      headers: {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
        'Access-Control-Allow-Headers': '*',
        'Access-Control-Max-Age': '86400',
      },
    })
  }

  try {
    const url = new URL(request.url)

    const domain = url.searchParams.get('domain')

    const format = url.searchParams.get('format') || 'json';

    const domainRegex = /^[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
    if (!domain.match(domainRegex)) {
      return new Response(JSON.stringify({
        code: 400,
        msg: '提供的域名参数无效。',
        timestamp: Date.now()
      }), {
        status: 400,
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*'
        }
      })
    }


    if (!domain) {
      return new Response(JSON.stringify({
        code: 400,
        msg: '请使用 domain 查询参数提供域名。',
        timestamp: Date.now()
      }), {
        status: 400,
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*'
        }
      })
    }

    if (format !== 'json' && format !== 'xml') {
      return new Response(JSON.stringify({
        code: 400,
        msg: '指定的格式无效。仅允许 json 和 xml。',
        timestamp: Date.now()
      }), {
        status: 400,
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*'
        }
      })
    }

    const apikey = "" // 在 whoisxmlapi.com 申請 API 密钥

    const api_url = `https://www.whoisxmlapi.com/whoisserver/WhoisService?apiKey=${apikey}&domainName=${domain}&outputFormat=${format}`

    const response = await fetch(api_url)

    if (!response.ok) {
      return new Response(JSON.stringify({
        code: 400,
        msg: '无法获取此域名的 WHOIS 信息。',
        timestamp: Date.now()
      }), {
        status: 400,
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*'
        }
      })
    }

    let responseData;
    if (format === 'json') {
      responseData = await response.json();
    } else if (format === 'xml') {
      responseData = await response.text();
    }

    if (format === 'json') {
      return new Response(JSON.stringify(responseData, null, 2), {
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*'
        }
      })
    } else if (format === 'xml') {
      return new Response(responseData, {
        headers: {
          'Content-Type': 'application/xml',
          'Access-Control-Allow-Origin': '*'
        }
      })
    }
  } catch (error) {
    return new Response(JSON.stringify({
      code: 500,
      msg: `Error: ${error.message}`,
      timestamp: Date.now()
    }), {
      status: 500,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      }
    })
  }
}
```
