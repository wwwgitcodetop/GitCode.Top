# Telegra.phWorkers

这是一个基于 Cloudflare Workers 部署的，使用 Telegra.ph API上传的图床。

GitHub：https://github.com/molikai-work/telegraphWorkers

## 部署
1. 复制本篇教程最后的源代码。
2. 登录到 [Cloudflare](https://dash.cloudflare.com/) 控制台。
3. 选择侧边栏的 [Workers 和 Pages](https://dash.cloudflare.com/?to=/:account/workers-and-pages) ，点击右上角的`创建`按钮。
4. 在`创建应用程序`页面选择`Workers` => `创建 Workers`按钮，设置域名前缀后点击`保存` => `完成`按钮。
5. 编辑刚刚新建的Workers的代码，把在`第一步`复制的源代码粘贴进去，再点击右上角的`部署`按钮，等等成功后可退出。、
6. 访问刚刚设置的地址（如果访问不了设置自己的域名后再试），在HTML页面中上传图片后方可获得图片地址。

## API
`/file`为代理 Telegra.ph 图片的地址。
`/upload`为上传图片的地址。

## 源代码
```
addEventListener('fetch', event => {
    event.respondWith(handleRequest(event.request));
});

async function handleRequest(request) {
    if (request.method === 'OPTIONS') {
        return handleOptionsRequest();
    }

    let targetURL = 'https://telegra.ph';

    const htmlResponse = await fetch('https://molikai-work.github.io/telegraphWorkers/index.html');
    const htmlContent = await htmlResponse.text();

    if (request.url.includes('/file')) {
        const newRequest = new Request(targetURL + new URL(request.url).pathname, request);

        newRequest.headers.delete('Host');
        newRequest.headers.delete('Referer');

        return handleCorsRequest(newRequest);
    } else if (request.url.includes('/upload')) {
        const newRequest = new Request(targetURL + new URL(request.url).pathname, request);

        newRequest.headers.delete('Host');
        newRequest.headers.delete('Referer');

        return handleCorsRequest(newRequest);
    } else {
        return new Response(htmlContent, {
            headers: {
                "Content-Type": "text/html;charset=utf-8",
                'Access-Control-Allow-Origin': '*'
            }
        });
    }
}

async function handleCorsRequest(request) {
    const response = await fetch(request);
    const headers = new Headers(response.headers);
    headers.set('Access-Control-Allow-Origin', '*');
    return new Response(response.body, {
        status: response.status,
        statusText: response.statusText,
        headers: headers
    });
}

function handleOptionsRequest() {
    return new Response(null, {
        headers: {
            'Access-Control-Allow-Origin': '*',
            'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
            'Access-Control-Allow-Headers': 'Content-Type',
            'Access-Control-Max-Age': '86400',
        },
    });
}
```
