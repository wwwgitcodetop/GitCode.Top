# OCR 识别干扰
通过一个简单的HTML页面，实现了基于JS的文本抗OCR识别的图像生成。

可以设置生成图的干扰参数或着点线干扰参数。

GitHub地址：[https://github.com/molikai-work/OCR-Interference](https://github.com/molikai-work/OCR-Interference)

## 示例
源文本：

> 鉴于对人类家庭所有成员的固有尊严及其平等的和不移的权利的承认，乃是世界自由、正义与和平的基础。任何人不得使为奴隶或奴役，人人生而自由，人人有权享有生命、自由和人身安全。

干扰图像：（失真级别2、波频4、波幅3、随机点数量100、随机线数量20）

`Gmeek-html<img src="https://file.gitcode.top/doce/issues-2/68747470733a2f2f6d6f6c696b61692d776f726b2e6769746875622e696f2f4f43522d496e746572666572656e63652f696d616765322e706e67.png">`

##源代码
可以直接复制到本地运行，新建文件后重命名扩展名为`.html`

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>抗OCR文本图像生成器</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }
        #container {
            max-width: 600px;
            margin: 0 auto;
            background-color: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
            color: #333;
        }
        #controls {
            margin-bottom: 20px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            color: #666;
        }
        input {
            width: 100%;
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }
        button {
            margin-top: 20px;
            background-color: #5d8d5f;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #59815b;
        }
        canvas {
            display: block;
            margin: 0 auto;
            border: 1px solid black;
            margin-top: 20px;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <div id="controls">
        <label for="inputText">反OCR源文本：</label>
        <input type="text" id="inputText" placeholder="输入文本" value="鉴于对人类家庭所有成员的固有尊严及其平等的和不移的权利的承认，乃是世界自由、正义与和平的基础。任何人不得使为奴隶或奴役，人人生而自由，人人有权享有生命、自由和人身安全。">
        
        <label for="maxChars">最大行字数：</label>
        <input type="number" id="maxChars" value="26">

        <label for="margin">边距：</label>
        <input type="number" id="margin" value="10">

        <label for="fontSize">字体大小：</label>
        <input type="number" id="fontSize" value="35">

        <label for="distortionLevel">失真级别：</label>
        <input type="number" id="distortionLevel" value="2">

        <label for="waveFrequency">波频：</label>
        <input type="number" id="waveFrequency" value="4">

        <label for="waveAmplitude">波幅：</label>
        <input type="number" id="waveAmplitude" value="3">

        <label for="pointCount">随机点数量：</label>
        <input type="number" id="pointCount" value="100">

        <label for="lineCount">随机线数量：</label>
        <input type="number" id="lineCount" value="20">

        <button id="generateButton">生成图像</button>
    </div>

    <canvas id="textCanvas"></canvas>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const generateButton = document.getElementById('generateButton');
            generateButton.addEventListener('click', updateCanvas);
            updateCanvas();
        });

        const canvas = document.getElementById('textCanvas');
        const ctx = canvas.getContext('2d');

        function updateCanvas() {
            const text = document.getElementById('inputText').value;
            const maxChars = parseInt(document.getElementById('maxChars').value);
            const margin = parseInt(document.getElementById('margin').value);
            const fontSize = parseInt(document.getElementById('fontSize').value);
            const distortionLevel = parseInt(document.getElementById('distortionLevel').value);
            const waveFrequency = parseInt(document.getElementById('waveFrequency').value);
            const waveAmplitude = parseInt(document.getElementById('waveAmplitude').value);
            const pointCount = parseInt(document.getElementById('pointCount').value);
            const lineCount = parseInt(document.getElementById('lineCount').value);

            drawDistortedText(text, maxChars, margin, fontSize, distortionLevel, waveFrequency, waveAmplitude, pointCount, lineCount);
        }

        function drawDistortedText(text, maxChars, margin, fontSize, distortionLevel, waveFrequency, waveAmplitude, pointCount, lineCount) {
            const lines = wrapText(ctx, text, maxChars, fontSize);
            const lineHeight = fontSize + 10;
            canvas.width = maxChars * (fontSize / 2) + 2 * margin;
            canvas.height = lines.length * lineHeight + 2 * margin;

            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.font = `${fontSize}px Arial`;
            ctx.fillStyle = 'black';

            let y = margin + fontSize;
            for (const line of lines) {
                ctx.fillText(line, margin, y);
                y += lineHeight;
            }

            distortCanvas(ctx, canvas.width, canvas.height, distortionLevel, waveFrequency, waveAmplitude);
            addRandomPoints(ctx, canvas.width, canvas.height, pointCount);
            addRandomLines(ctx, canvas.width, canvas.height, lineCount);
        }

        function wrapText(ctx, text, maxChars, fontSize) {
            const words = text.split('');
            const lines = [];
            let line = '';
            for (const word of words) {
                const testLine = line + word;
                const metrics = ctx.measureText(testLine);
                const testWidth = metrics.width;
                if (testWidth > maxChars * (fontSize / 2) && line.length > 0) {
                    lines.push(line);
                    line = word;
                } else {
                    line = testLine;
                }
            }
            lines.push(line);
            return lines;
        }

        function distortCanvas(ctx, width, height, distortionLevel, waveFrequency, waveAmplitude) {
            const imageData = ctx.getImageData(0, 0, width, height);
            const data = imageData.data;
            const distortedData = new Uint8ClampedArray(data);

            for (let y = 0; y < height; y++) {
                const yDistortion = Math.sin(y / waveFrequency) * waveAmplitude;
                for (let x = 0; x < width; x++) {
                    const xDistortion = Math.sin(x / waveFrequency) * waveAmplitude;
                    const dx = Math.floor(xDistortion + distortionLevel * (Math.random() - 0.5));
                    const dy = Math.floor(yDistortion + distortionLevel * (Math.random() - 0.5));
                    const sx = x + dx;
                    const sy = y + dy;
                    const si = (sx + sy * width) * 4;
                    const di = (x + y * width) * 4;

                    if (sx >= 0 && sx < width && sy >= 0 && sy < height) {
                        distortedData[di] = data[si];
                        distortedData[di + 1] = data[si + 1];
                        distortedData[di + 2] = data[si + 2];
                        distortedData[di + 3] = data[si + 3];
                    }
                }
            }

            ctx.putImageData(new ImageData(distortedData, width, height), 0, 0);
        }

        function addRandomPoints(ctx, width, height, count) {
            for (let i = 0; i < count; i++) {
                const x = Math.random() * width;
                const y = Math.random() * height;
                ctx.fillRect(x, y, 2, 2);
            }
        }

        function addRandomLines(ctx, width, height, count) {
            for (let i = 0; i < count; i++) {
                const x1 = Math.random() * width;
                const y1 = Math.random() * height;
                const x2 = Math.random() * width;
                const y2 = Math.random() * height;
                ctx.beginPath();
                ctx.moveTo(x1, y1);
                ctx.lineTo(x2, y2);
                ctx.stroke();
            }
        }
    </script>
</body>
</html>
```
