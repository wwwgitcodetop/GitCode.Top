# vercelTelegramRobot
在 Vercel 上简单部署调用 TelegramAPI 的 API，这是一个简单的示例，请参考。

此项目不储存和设置 API 密钥，靠接收的 POST 请求数据工作，仅是一个简单的部署于 Vercel 的 API 项目示例。

（好像没什么用，为什么不直接用 Telegram 官方的 API 呢？）

GitHub：[https://github.com/wwwgitcodetop/vercelTelegramRobot](https://github.com/wwwgitcodetop/vercelTelegramRobot)

## 准备
1. 首先需要在 Telegram 上向 https://t.me/BotFather 发送信息通过一定步骤来申请一个 Telegram 机器人，获得 `HTTP API` 保存。

2. 然后获取你接受信息的用户 ID ，可以使用 https://t.me/userinfobot 快速获得用户 ID。

（请注意，这个机器人似乎并不是官方的，我不是在推荐使用这个机器人）

## 部署
很简单的，你可以分叉这个[存储库](https://github.com/wwwgitcodetop/vercelTelegramRobot)，然后在 Vercel 上部署新项目，选择本存储库，等待部署完成后即可。

或者快捷点击下面的按钮来一键部署。

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/import/project?template=https://github.com/wwwgitcodetop/vercelTelegramRobot)

## API 使用
可以使用 Vercel 默认分配的地址使用，向这个地址发送 POST 请求，内容为 JSON 格式，

例如：
```json
{
    apiToken = "xxxxx:xxxxxxxxxxxxxxx"
    chatId = "xxxxxx"
    message = "Hello, World!"
} 
```

其中的 `apiToken` 为你在准备第一步获得的机器人密钥 `HTTP API`，
`chatId` 为你获取的用户 ID，
`message` 为要让机器人发送的信息。
