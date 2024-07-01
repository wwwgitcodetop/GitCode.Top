# 几何像素头像生成
通过一个简单的HTML页面，实现了通过字符串来生成简单的几何像素头像。

可以设置生成字符串与对称选项。

GitHub地址：[https://github.com/molikai-work/geometricAvatarGeneration](https://github.com/molikai-work/geometricAvatarGeneration)

## 示例
生成字符串：`123456`
生成竖对称：`false`

`Gmeek-html<img src="https://file.gitcode.top/doce/issues-14/344553269-6ac93975-780b-406a-86df-c757ee868056.png">`

## 源代码
可以直接复制到本地运行，新建文件后重命名扩展名为`.html`

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>字符串头像生成器</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
    <style>
        #avatar {
            border: 1px solid #ddd;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="mt-5">头像生成器</h1>
        <div class="form-group">
            <label for="inputString">输入任意字符串：</label>
            <input type="text" class="form-control" id="inputString" placeholder="随便输入些什么东西...">
        </div>
        <div class="form-group">
            <div class="form-check">
                <input class="form-check-input" type="checkbox" id="symmetric">
                <label class="form-check-label" for="symmetric">
                    生成竖对称
                </label>
            </div>
        </div>
        <button id="generateAvatar" class="btn btn-primary mx-2">生成头像</button>
        <canvas id="avatar" width="420" height="420"></canvas>
    </div>

    <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <script>
        $(document).ready(function () {
            const urlParams = new URLSearchParams(window.location.search);
            const idParam = urlParams.get('id');
            const symmetryParam = urlParams.get('symmetry');

            if (idParam) {
                $('#inputString').val(idParam);
            }
            if (symmetryParam !== null) {
                $('#symmetric').prop('checked', symmetryParam === 'true');
            }

            $('#generateAvatar').click(function () {
                var inputString = $('#inputString').val();
                var isSymmetric = $('#symmetric').is(':checked');
                generateAvatar(inputString, isSymmetric);
            });

            if (idParam) {
                var inputString = $('#inputString').val();
                var isSymmetric = $('#symmetric').is(':checked');
                generateAvatar(inputString, isSymmetric);
            }

            function generateAvatar(inputString, isSymmetric) {
                var canvas = document.getElementById('avatar');
                var ctx = canvas.getContext('2d');
                ctx.clearRect(0, 0, canvas.width, canvas.height);

                var size = 70;
                var cols = 6;
                var rows = 6;

                function getHashCode(str) {
                    var hash = 0;
                    for (var i = 0; i < str.length; i++) {
                        var char = str.charCodeAt(i);
                        hash = ((hash << 5) - hash) + char;
                        hash |= 0;
                    }
                    return hash;
                }

                var hashCode = getHashCode(inputString);

                var colors = ['#3498db', '#1abc9c', '#e74c3c', '#f1c40f', '#2ecc71', '#9b59b6', '#7f8c8d'];
                var bgColor = '#f5f7f8';
                var blockColor = colors[Math.abs(hashCode) % colors.length];

                for (var col = 0; col < (isSymmetric ? Math.ceil(cols / 2) : cols); col++) {
                    for (var row = 0; row < rows; row++) {
                        var x = col * size;
                        var y = row * size;

                        var color = (hashCode >> (col * row)) & 1 ? blockColor : bgColor;
                        ctx.fillStyle = color;
                        ctx.fillRect(x, y, size, size);
                        if (isSymmetric) {
                            ctx.fillRect(canvas.width - x - size, y, size, size);
                        }
                    }
                }
            }
        });
    </script>
</body>
</html>
```
