# D1-Workers
这是一个基于 Cloudflare Workers 部署的，快捷连接 D1 数据库的程序，可当作 API 使用。

部署后可直接访问使用或者当作一个API，返回JSON格式的信息。

GitHub：[https://github.com/molikai-work/d1-workers](https://github.com/molikai-work/d1-workers)

## 部署
1. 复制本篇教程最后的源代码。
2. 登录到 [Cloudflare](https://dash.cloudflare.com/) 控制台。
3. 选择侧边栏的 [Workers 和 Pages](https://dash.cloudflare.com/?to=/:account/workers-and-pages) ，点击右上角的创建按钮。
4. 在创建应用程序页面选择Workers => 创建 Workers按钮，设置域名前缀后点击保存 => 完成按钮。
5. 编辑刚刚新建的Workers的代码，把在第一步复制的源代码粘贴进去，再点击右上角的部署按钮，等等成功后可退出。
6. 在`设置` => `函数`页面绑定`D1`数据库，变量名称填``
7. 访问刚刚设置的地址（如果访问不了设置自己的域名后再试），成功。

## 使用示例

```
https://example.com/?table=<table>&fuzzy=<fuzzy>&column=<column>&search=<search>
```

其中的`table`为必填的要查询的表的名称，
`fuzzy`为是否启用模糊搜索，可选值为`true`、`false`，
`column`为搜索的目标项，
`search`为要搜索的内容。

## 源代码
```
// ?table=111&fuzzy=true&column=id&search=1

const src_default = {
  async fetch(request, env) {
    try {
      const url = new URL(request.url);
      const params = new URLSearchParams(url.search);

      if (request.method !== 'GET') {
        return new Response("Method Not Allowed", { status: 405 });
      }

      const table = params.get('table');
      if (!table) {
        return new Response("The table parameter for 'table' is missing.", { status: 400 });
      }
      if (table !== "111" && table !== "222") {
        return new Response("Invalid table name.", { status: 403 });
      }

      const fuzzy = params.get('fuzzy');
      if (fuzzy && fuzzy !== "true" && fuzzy !== "false") {
        return new Response("Invalid fuzzy name.", { status: 403 });
      }

      const columnName = params.get('column');
      if (columnName && columnName !== "id" && columnName !== "ces" && columnName !== "......") {
        return new Response("Invalid table name.", { status: 403 });
      }

      const searchTerm = params.get('search');
      if (!searchTerm || !/^[\u4e00-\u9fa5a-zA-Z0-9-.]+$/.test(searchTerm)) {
        return new Response("Invalid search name.", { status: 403 });
      }

      const column = "id AS id,123 AS 123"

      try {
        let stmt;
        let results;

        if (fuzzy === "true" && columnName && searchTerm) {
          stmt = await env.DB.prepare(`SELECT ${column} FROM ${table} WHERE ${columnName} LIKE '%${searchTerm}%'`);
        } else if (columnName && searchTerm) {
          stmt = await env.DB.prepare(`SELECT ${column} FROM ${table} WHERE ${columnName} LIKE ${searchTerm}`);
        } else {
          stmt = await env.DB.prepare(`SELECT ${column} FROM ${table}`);
        }

        results = await stmt.all();

        return new Response(JSON.stringify(results), { status: 200, headers: { 'Content-Type': 'application/json' }});
      } catch (error) {
        console.error(`Error performing database operation:`, error);
        return new Response("An error occurred while processing the request.", { status: 500 });
      }

    } catch (error) {
      console.error("Error processing request:", error);
      return new Response("An error occurred while processing the request.", { status: 500 });
    }
  }
};

export default src_default;
```
