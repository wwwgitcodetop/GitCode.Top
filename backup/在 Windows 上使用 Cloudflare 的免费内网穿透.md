内网穿透是一种将本地网络服务暴露给外部互联网用户的技术，无论是为了远程访问家中的服务器，亦或是没有公网 IP 的服务器，内网穿透都是一个非常实用的工具，本文将介绍如何使用 Cloudflare 的免费服务实现内网穿透。

## 准备工作
在开始之前，你需要以下几项准备：

1. 一个 Cloudflare 账户。如果还没有，可以前往 [Cloudflare 官网](https://www.cloudflare.com) 注册。
2. 一个域名，并将其添加到你的 Cloudflare 账户中。
3. 一台能够访问互联网的计算机。

## 进入 Cloudflare Tunnel 控制台
1. 首先，你需要访问 Cloudflare Tunnel 的控制台页面，它不在主控制台页面，而是在[这里](https://one.dash.cloudflare.com/)。
2. 点击上面的网址，然后选择你的账户，进入账户 Tunnel 控制台页面（开启网页翻译可能会引起错误，跟着教程来就好）。
3. 点击左侧边栏的 `Networks` 项，然后再选择 `Tunnels`，然后往下看，点击中间蓝色的 `Add a tunnel` 按钮进入创建隧道页面。

![{0B2F1A44-5A21-4AE1-9F4D-3B80B4B76668}](https://github.com/user-attachments/assets/78575d2d-f5df-4ff6-9337-b7d94843364f)

## 创建隧道
1. 创建隧道的第一个选项不用管，默认 `Cloudflared` 即可，然后点击下一步按钮 `Next`，

![{F3A8DB45-FF5E-4ED7-A1AD-8190B802FD7B}](https://github.com/user-attachments/assets/5b2fc2c4-b42c-4c2a-83dc-b25db7b1f377)

2. 为你的隧道设置一个名字，然后点击按钮 `Save tunnel`。

![{86710FCA-7F23-4741-A2E4-4DC705D306EF}](https://github.com/user-attachments/assets/6d23ca76-0f50-4357-95d3-42c7d6b30e61)

## 安装 Cloudflare Tunnel 客户端

1. 在下一步页面选择你所使用的操作系统或环境，注意子选项的操作系统位数，这里以 Windows 64-bit举例。
![{1D053D4C-3333-4941-B78F-934EA7009C44}](https://github.com/user-attachments/assets/80297e07-2447-4361-878f-73d38388b5e2)

2. 您可看到页面往下一点的 Download 链接，点击，下载程序，然后在下载完毕后运行文件来安装，安装完成后窗口自动消失而且没有提示。

![{F6E70548-D1E6-4E48-9C76-1B48B67531A6}](https://github.com/user-attachments/assets/d1a1e9fe-ddc8-46fc-9a6c-91c7d4abea74)

## 登录
1. 页面中的第 `4` 步给出了快捷登陆的链接，查看并点击代码段右边的复制按钮。

![{7197BC03-9B03-4CF0-80DC-29FA06D8F6F2}](https://github.com/user-attachments/assets/8b9a4298-56ae-44c4-8fbf-f21cb95e6771)

2. 使用管理员权限打开命令提示符并粘贴刚刚复制的代码，回车，完成。

## 继续配置
1. 你可以查看下图的示例，从上到下从左到右分别是，对外的：`子域`、`域名`、`路径`，服务的`协议`、`URL`。

![{FD38585F-B044-4C82-92C0-E66107BC151A}](https://github.com/user-attachments/assets/fc6290aa-4360-464d-8921-f416185ef9c9)

这里的意思是创建的隧道配置到 `r2-w11.gitcode.top` 域名，然后使用 `HTTP` 协议传输服务器上的 `8086` 端口。

使用 Everything 示例。
![{5859A3BD-B55C-4F36-A27C-F63C734A7A30}](https://github.com/user-attachments/assets/fd3b9c75-5c08-4ba2-a8c0-a1d34b1e57f1)

## 完成
如果你在配置过程中遇到任何问题，可以参考 [Cloudflare 官方文档](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps) 获取更多信息。
