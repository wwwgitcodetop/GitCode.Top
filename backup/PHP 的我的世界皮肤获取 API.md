# Minecraft皮肤Api
一个基于 PHP 的我的世界玩家皮肤获取 API。

自动将处理过的头像通过 [https://www.imgtp.com/](https://www.imgtp.com/) 的API上传到图床中。

有限制请求数的功能，请求记录数据默认存储在程序所在目录的`rate_limit_`文件夹。

GitHub：[https://github.com/molikai-work/MinecraftSkinApi](https://github.com/molikai-work/MinecraftSkinApi)

## 使用示例
### 通过API调用，提交 GET 请求。

```url
https://example.com/?name=<id>&avatar=true&avatarsize=64
```

其中`id`填目标玩家的ID，`avatar`等于`true`时才会生成头像，通过`avatarsize`来设置头像的正方形大小。

### 配置
以有详细注释，可根据注释填写。

于代码的第5、6、183行。

## 要求
这段 PHP 代码最低需要 PHP 5.5 的版本，使用了 cURL、JSON 和 GD 扩展。

## 源代码
```php
<?php
// ?name=<id>&avatar=true&avatarsize=64

// 速率限制配置
$rate_limit = 10; // 允许的请求次数
$time_period = 120; // 时间段，单位为秒

// 获取当前时间戳
$current_time = time();

// 获取客户端 IP 地址
$client_ip = $_SERVER['REMOTE_ADDR'];

// 设置存储限制信息的文件夹路径
$folder_name = 'rate_limit_';

// 创建文件夹
if (!file_exists($folder_name)) {
    mkdir($folder_name);
}

// 设置存储限制信息的文件路径
$filename = $folder_name . '/' . 'rate_limit_' . $client_ip . '.txt';

// 如果文件不存在，则创建文件并初始化限制信息
if (!file_exists($filename)) {
    file_put_contents($filename, json_encode(array('requests' => 0, 'last_request_time' => 0)));
}

// 从文件中读取限制信息
$limit_info = json_decode(file_get_contents($filename), true);

// 如果上次请求时间与当前时间超出时间段，则重置请求次数
if ($current_time - $limit_info['last_request_time'] > $time_period) {
    $limit_info['requests'] = 0;
}

// 如果请求次数超出限制，则返回错误信息
if ($limit_info['requests'] >= $rate_limit) {
    header("HTTP/1.1 429 Too Many Requests");
    header('Content-Type: application/json');
    echo json_encode (
        array(
            'code' => 429,
            'msg' => '请求过多，请稍后再试',
            'time' => $current_time
        ));
    exit;
}

// 更新限制信息
$limit_info['requests']++;
$limit_info['last_request_time'] = $current_time;

// 存储更新后的限制信息到文件中
file_put_contents($filename, json_encode($limit_info));

// 初始化 JSON 数据数组
$json_data = array();

if (empty($_GET['name'])) {
    $json_data['code'] = 500;
    $json_data['msg'] = "请提供一个有效的 Minecraft 用户名";
    $json_data['time'] = time();
} else {
    $mojang_uuid = curl_get_https('https://api.mojang.com/users/profiles/minecraft/' . $_GET['name']);
    $de_uuid = json_decode($mojang_uuid, true);

    if (!is_null($de_uuid['id'])) {
        $player_profile = curl_get_https('https://sessionserver.mojang.com/session/minecraft/profile/' . $de_uuid['id']);
        $de_profile = json_decode($player_profile, true);

        $de_textures = json_decode(base64_decode($de_profile['properties'][0]['value']), true);

        // 检查是否需要处理头像
        $include_avatar = isset($_GET['avatar']) ? ($_GET['avatar'] == 'true') : false;

        if ($include_avatar) {
            // 设置头像大小，默认 64px
            $size_avatar = isset($_GET['avatarsize']) ? $_GET['avatarsize'] : 64;

            // 创建头像
            $copyskin = imagecreatetruecolor($size_avatar, $size_avatar);
            $originalskin = imagecreatefromstring(file_get_contents($de_textures['textures']['SKIN']['url']));

            // 图像修改
            if ($copyskin && $originalskin) {
                imagecopyresized($copyskin, $originalskin, 0, 0, 8, 8, $size_avatar, $size_avatar, 8, 8);
                imagecopyresized($copyskin, $originalskin, 0, 0, 40, 8, $size_avatar, $size_avatar, 8, 8);

                // 保存头像
                $avatar_filename = uniqid(rand(), true) . ".png";
                if (imagepng($copyskin, $avatar_filename)) {
                    // 调用接口上传图片
                    $result = upload_image($avatar_filename);

                    if ($result && $result['code'] == 200 && isset($result['data']['url'])) {
                        // $json_data['avatar'] = $result['data']['url']; // 另一种方法
                    } else {
                        $json_data['code'] = 500;
                        $json_data['avatar'] = "头像上传失败";
                        $json_data['time'] = time();
                    }

                    // 删除临时文件
                    unlink($avatar_filename);
                } else {
                    $json_data['code'] = 500;
                    $json_data['avatar'] = "头像保存失败";
                    $json_data['time'] = time();
                }

                // 销毁图像资源
                imagedestroy($copyskin);
                imagedestroy($originalskin);
            } else {
                $json_data['code'] = 500;
                $json_data['avatar'] = "图像资源创建失败";
                $json_data['time'] = time();
            }
        }

        // 构建 JSON 数据
        $json_data['code'] = 200;
        $json_data['msg'] = "皮肤请求成功";
        $json_data['time'] = time();

        $json_data['data'] = array(
            'skin_url' => $de_textures['textures']['SKIN']['url'],
            'cape_url' => !empty($de_textures['textures']['CAPE']['url']) ? $de_textures['textures']['CAPE']['url'] : ''
        );

        $json_data['avatar'] = $result['data'];
    } else {
        $json_data['code'] = 500;
        $json_data['msg'] = "无法通过用户名获取 UUID";
        $json_data['time'] = time();
    }
}

// 输出 JSON 数据
header('Content-Type: application/json');
echo json_encode($json_data);

// 网络请求函数
function curl_get_https($url) {
    $curl = curl_init();
    curl_setopt_array($curl, array(
        CURLOPT_URL => $url,
        CURLOPT_HEADER => 0,
        CURLOPT_RETURNTRANSFER => 1,
        CURLOPT_SSL_VERIFYPEER => true,
        CURLOPT_SSL_VERIFYHOST => 2, // 设置为 2 进行严格的证书检查
    ));

    $response = curl_exec($curl);

    if ($response === false) {
        // 请求失败，输出错误信息
        $error = curl_error($curl);
        curl_close($curl);
        die("Curl 失败: " . $error);
    }

    $httpCode = curl_getinfo($curl, CURLINFO_HTTP_CODE);
    if ($httpCode >= 400) {
        // HTTP 错误码处理
        curl_close($curl);
        die("HTTP 请求失败，代码 {$httpCode}");
    }

    curl_close($curl);
    return $response;
}

// 上传图片函数
function upload_image($filename) {
    // 接口地址
    $api_url = "https://imgtp.com/api/upload";

    // 设置请求头部
    $headers = array(
        "token: " // imgtp.com API 的 token 为空则游客上传
    );

    // 构建POST请求数据
    $post_data = array(
        'image' => new CURLFile(realpath($filename))
    );

    // 初始化CURL
    $curl = curl_init();

    // 设置请求URL
    curl_setopt($curl, CURLOPT_URL, $api_url);

    // 设置POST请求
    curl_setopt($curl, CURLOPT_HTTPHEADER, $headers);
    curl_setopt($curl, CURLOPT_POST, true);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $post_data);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);

    // 执行请求
    $response = curl_exec($curl);

    // 关闭CURL
    curl_close($curl);

    // 解析返回的 JSON 数据
    $result = json_decode($response, true);
    
    // 提取相关信息
    $name = $result['data']['name'];
    $url = $result['data']['url'];
    
    // 构建 JSON 数据
    $result = array(
        'code' => $result['code'],
        'msg' => $result['msg'],
        'time' => $result['time'],
        'data' => array(
            'img_code' => $result['code'],
            'img_msg' => $result['msg'],
            'name' => $name,
            'url' => $url
        )
    );

    return $result;
}
?>
```
