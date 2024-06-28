# ipInfoApi
这是一个基于 Cloudflare Workers 部署的，获取访客信息的API。

部署后可直接访问使用或者当作一个API，返回JSON格式的信息。

GitHub：[https://github.com/molikai-work/ipInfoApi](https://github.com/molikai-work/ipInfoApi)

## 部署
1. 复制本篇教程最后的源代码。
2. 登录到 [Cloudflare](https://dash.cloudflare.com/) 控制台。
3. 选择侧边栏的 [Workers 和 Pages](https://dash.cloudflare.com/?to=/:account/workers-and-pages) ，点击右上角的创建按钮。
4. 在创建应用程序页面选择Workers => 创建 Workers按钮，设置域名前缀后点击保存 => 完成按钮。
5. 编辑刚刚新建的Workers的代码，把在第一步复制的源代码粘贴进去，再点击右上角的部署按钮，等等成功后可退出。、
6. 访问刚刚设置的地址（如果访问不了设置自己的域名后再试），完成。

## 示例
```json
{
    "code": 200,
    "msg": "OK",
    "time": 1714457816445,
    "data": {
        "ip": "203.**.**.94",
        "rayId": "87c55b******203d",
        "pseudoIPv4": null,
        "connectingIPv6": null,
        "country": "***",
        "region": "***",
        "city": "***",
        "colo": null,
        "latitude": "***",
        "longitude": "***",
        "service": "***",
        "lang": "zh;q=0.9,en;q=0.8,en-US;q=0.7,en-GB;q=0.6",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 Edg/124.0.0.0"
    }
}
```

### 源代码
```
const handleOptionsRequest = () => {
  return new Response(null, {
    headers: {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
      "Access-Control-Allow-Headers": "*",
      "Access-Control-Max-Age": "86400"
    }
  });
};

const handleInfoRequest = (request) => {
  try {
    const { cf } = request;
    const ip = request.headers.get("x-real-ip");
    const rayId = request.headers.get("CF-ray");
    const pseudoIPv4 = request.headers.get("CF-Pseudo-IPv4");
    const connectingIPv6 = request.headers.get("CF-Connecting-IPv6");
    const lang = request.headers.get("Accept-Language");
    const userAgent = request.headers.get("User-Agent");

    const info = {
      country: cf.country || null,
      region: cf.region || null,
      city: cf.city || null,
      colo: cf.colo || null,
      latitude: cf.latitude || null,
      longitude: cf.longitude || null,
      service: cf.asOrganization || null
    };

    const responseData = {
      code: 200,
      msg: "OK",
      time: Date.now(),
      data:{
        ip,
        rayId,
        pseudoIPv4,
        connectingIPv6,
        ...info,
        lang,
        userAgent
      }
    };

    return new Response(JSON.stringify(responseData), {
      headers: {
        "Access-Control-Allow-Origin": "*",
        "Content-Type": "application/json;charset=utf-8"
      }
    });
  } catch (error) {
    console.error("An error occurred:", error);
    const errorMessage = {
      code: 500,
      msg: "Internal Server Error",
      time: Date.now()
    };

    return new Response(JSON.stringify(errorMessage), { 
      status: 500, 
      headers: { 
        'Access-Control-Allow-Origin': '*', 
        'Content-Type': 'application/json;charset=utf-8' 
      } 
    });
  }
};

const fetchHandler = (request) => {
  if (request.method === "OPTIONS") {
    return handleOptionsRequest();
  }
  return handleInfoRequest(request);
};

export default { fetch: fetchHandler };
```
