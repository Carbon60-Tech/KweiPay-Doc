# 余额和余额变动消息推送
## 余额展示
1. 登陆商户后台
2. 点击左侧 `财务管理`->`余额管理`->`查看余额`
<img width="1962" alt="image" src="https://user-images.githubusercontent.com/20775615/232422769-599273ca-fccb-4de1-be84-7392d4571256.png">

## 余额变动消息推送

> 当商户有充值和提现时，KWeiPay会根据商户录入的API来推送动账后的余额。

### 开启消息推送
1. 登陆商户后台
2. 点击左侧 `财务管理`->`余额管理`->`余额消息推送管理`
<img width="1958" alt="image" src="https://user-images.githubusercontent.com/20775615/232423448-40dfccb4-857f-4f66-9e6d-503d73b7e886.png">
3. 点击右侧 `新增` 按钮
<img width="1954" alt="image" src="https://user-images.githubusercontent.com/20775615/232423596-1db595dc-856c-480d-8f53-5a4d687e1168.png">
4. 录入API
<img width="1954" alt="image" src="https://user-images.githubusercontent.com/20775615/232423792-44d0ced9-7e0a-4985-a7e7-b9782b7d5e93.png">

#### 消息推送参数说明
### 接口路径

`以商户录入Api为准`

### 请求方式

`POST`

### 请求参数

```json
{
    "sign":"3da20594f20fc037d1a4c46452f1d3132399ff121deaa83c13a827991406916f",        // 鉴权签名
    "chain":"TRX",                                                                    // 主链
    "token":"USDT",                                                                   // 币种
    "balance":200,                                                                    // 余额
    "timestamp":1234567890                                                            // 时间戳
}
```

### 签名规则

1. 对需要参与签名的参数按键名进行降序排序。
2. 将需要参与签名的参数按键1=值1&键2=值2...的方式进行链接。
3. 对步骤2得到的字符串进行hash_hmac 256签名，secret为商户后台申请的secret。
4. 将签名后的数据与sign参数进行对比，一致则代表数据未被伪造/篡改。

#### 签名示例
```php
$params = [
  'chain'     => 'TRX',
  'token'     => 'USDT',
  'balance'   => 200,
  'timestamp' => 1681053844,
];
ksort($params);
echo hash_hmac('sha256', http_build_query($params), '私钥'); //查看商户后台获取私钥
```

### 接口返回要求

当商户录入的接口按以下格式返回时，KWeiPay平台则认为数据推送成功。

```json
{
  "code":0
}
```
