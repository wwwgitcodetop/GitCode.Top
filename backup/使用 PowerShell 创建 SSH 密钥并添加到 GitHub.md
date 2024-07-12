利用 PowerShell 创建 SSH 密钥并添加到 GitHub，以便使用 SSH 协议拉取仓库与提交。

## 1. 创建 SSH 密钥

首先，右键 `开始` 按钮打开 `PowerShell` （终端）并按照以下步骤操作：

### 1.1 生成 SSH 密钥
直接执行以下命令：

```bash
ssh-keygen -t rsa -b 4096 -C "info@example.com"
```

将 `info@example.com` 替换为您在 GitHub 上注册的邮箱地址，
然后可以一路按回车键，如果没有错误信息，那么密钥就会生成在默认路径 `C:\Users\example\.ssh\id_rsa`，
其中的 `example` 为您的系统用户名。

### 1.2 查看/复制 SSH 密钥
找到您刚刚生成的密钥文件，应该有一个以 `.pub` 扩展名结尾的文件，那是您的 SSH 公钥，
右键，选择使用文本编辑器打开，复制里面的内容，退出。

## 2. 将 SSH 密钥添加到 GitHub

接下来，需要将您刚刚复制的 SSH 公钥添加到您的 GitHub 账户：

1. 登录 GitHub，打开侧边栏，点击 `Settings` （设置）进入设置页面。
2. 选择 `SSH and GPG keys`（SSH 和 GPG 密钥）选项进入设置密钥的界面。
3. 点击 `New SSH key` （新的 SSH 密钥）或 `Add SSH key`（添加 SSH 密钥）。
4. 在 `Title` （标题）输入框中为密钥起个名字，要好记，或者什么都可以。
5. `Key type` （密钥类型）选项不用设置，默认 `Authentication Key` （身份验证密钥）即可。
6. 将复制的 SSH 公钥内容粘贴到 `Key` （密钥）输入框中。
7. 点击 `Add SSH key`。

现在，如果没有提示错误，那么您已经成功将 SSH 密钥添加到了您的 GitHub 账户中。

## 3. 测试 SSH 连接

最后，可以在 `PowerShell` 中通过以下命令测试 SSH 连接是否正常：

```bash
ssh -T git@github.com
```

如果提示：

```
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

在终端中输入 `yes` 然后回车即可，这是问您是否信任 `github.com`。

如果设置正确，您将看到类似以下消息：

```
Hi example! You've successfully authenticated, but GitHub does not provide shell access.
```

> [!TIP]
> 创建 SSH 密钥和复制公钥并导入网站的过程是通用的，理论上也适用于其他网站。

现在，您可以使用 SSH 协议与 GitHub 进行代码管理了，作者亲身经历使用 HTTPS 协议真的不好连接 GitHub 存储库。
