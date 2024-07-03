# 基于 Cloudflare Workers 的网站访问数统计系统
通过 Cloudflare Workers 可实现免费部署与存储，免费计划每日 Workers 请求 10 万次，D1 免费 5GB 存储和 5 百万次读取，足够个人使用。

仓库地址：[https://github.com/molikai-work/Analytics_with](https://github.com/molikai-work/Analytics_with)

## 部署步骤
### 克隆存储库

1. 确保安装 Git 命令，然后执行：
```
git clone https://github.com/molikai-work/Analytics_with.git
```

> [!TIP]
> 如果克隆失败可以手动在 GitHub  Web 页面上下载压缩包，见仓库文件页面右上的 `Code` 按钮的 `Download ZIP`。

2. 在克隆下来的项目文件夹内打开终端，准备执行命令。

### 安装依赖
3. 安装项目必须的依赖，执行一下命令。

```
npm install -g wrangler
npm install hono
```

### 登录 Cloudflare

1. 确保浏览器已登录 Cloudflare，然后执行以下命令，跳转 Cloudflare 网页授权。
```
npx wrangler login
```

### 创建 D1 数据库 "web_analytics_2"

> 数据库名称为 `web_analytics_2` ，与 `package.json` 内保持一致

```
npx wrangler d1 create web_analytics_2
```

成功后显示：
```
✅ Successfully created DB web_analytics

[[d1_databases]]
binding = "DB" # available in your Worker on env.DB
database_name = "web_analytics_2"
database_id = "<unique-ID-for-your-database>"
```

### 配置 Workers 和 D1 数据库绑定

将上个步骤返回的 `unique-ID-for-your-database` 写进 `wrangler.toml` 中

```
name = "analytics-with-2"
main = "src/index.ts"
compatibility_date = "2024-07-03"

[[d1_databases]]
binding = "DB"
database_name = "web_analytics_2"
database_id = "<unique-ID-for-your-database>" # 这里！
```

### 初始化 D1 数据库的表结构

```
npm run initSql
```

### 发布 Workers

```
npm run deploy
```

成功后显示：
```
> analytics_with_cloudflare@0.0.0 deploy
> wrangler deploy

Proxy environment variables detected. We'll use your proxy for fetch requests.
 ⛅️ wrangler 3.18.0
-------------------
Your worker has access to the following bindings:
- D1 Databases:
  - DB: web_analytics (<unique-ID-for-your-database>)
Total Upload: 50.28 KiB / gzip: 12.23 KiB
Uploaded analytics_with_cloudflare (1.29 sec)
Published analytics_with_cloudflare (4.03 sec)
  https://analytics_with_cloudflare.xxxxx.workers.dev
Current Deployment ID: xxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## 如何使用
### 引入脚本
部署完后端之后，使用您的服务地址将请求发送到自己的服务

将本项目中的 [analytics.js](/front/dist/analytics.js) 文件下载后修改内容，
修改 `apiUrl` 的值为您部署的程序地址，例如：“https://analytics.example.com”
注意不要加末尾的 `/`。

`page_pv_id` 和 `page_uv_id` 可根据需求修改为能希望展示数据的标签 id 值。

```
(function() {
    const config = {
        apiUrl: "<your-domain-name>", // API 地址
        page_pv_id: "page_pv", // PV 元素 ID
        page_uv_id: "page_uv", // UV 元素 ID

...
```

### 展示数据
- 加入默认 id 为 `page_pv` 或 `page_uv`的标签，即可显示 `访问人次(pv)` 或 `访问人数(uv)`

例如：

```
本页访问人次:<span id="page_pv"></span>
本页访问人数:<span id="page_uv"></span>
```
