# 开启充值消息推送

商户在管理后台录入Api接口后，即可开启充值消息推送。

## 录入Api

1. 点击左侧`财务管理`->`充值管理`->`充值消息推送管理`。
  <img width="1983" alt="image" src="https://user-images.githubusercontent.com/20775615/230758385-00c7d93b-dea3-4a3c-b987-639aeea1ceee.png">
2. 点击右侧`新增`按钮
  <img width="1983" alt="image" src="https://user-images.githubusercontent.com/20775615/230758412-cae3de00-53f5-45a4-8db1-404dd0a45bbe.png">

## 录入接口信息
录入成功后，KweiPay会以POST方式请求商户录入的接口，将充值消息推送给商户。
  <img width="1983" alt="image" src="https://user-images.githubusercontent.com/20775615/230758676-3625c41e-c50d-4152-a3bb-52ff720ad9d5.png">
  
## 消息推送参数说明
### 接口路径

`以商户录入Api为准`

### 请求方式

`POST`

### 请求参数

```json
{
    "sign":"3da20594f20fc037d1a4c46452f1d3132399ff121deaa83c13a827991406916f",        // 鉴权签名
    "from":"TVZTyrcHW47GPQWm3FeVAo4hD9yFHSun6A",                                      // 付款人
    "to":"TSoRWegvhJG6d7Ftk2HMsWkiNGwYWcLKiV",                                        // 收款人
    "value":200,                                                                      // 充值金额
    "token":"USDT",                                                                   // 充值币种
    "chain":"TRX",                                                                    // 公链
    "hash":"299c1e2ad916eb747ba8a29c1536279b8cbb9543bfc1dfc7d869a4441144a931",        // 交易哈希
    "blockHash":"00000000021f947cc0a0304cae60b6986ae16ee2ebe4eaf1fe0f3266570c3750",   // 区块哈希
    "blockNumber":"35624060",                                                         // 区块编号
    "timestamp":1681024111,                                                           // 时间戳
    "status":1                                                                        // 交易状态，1:交易成功；2:交易失败
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
  'from' => '0xdada22cd461f6fed615a5f78a7a768edbdd5f60b',
  'to' => '0x07a5ff21281c4ec0b653e73847c9d30e9642a1ce',
  'value' => 1314,
  'token' => 'USDT',
  'chain' => 'ETH',
  'hash' => '0xe8518ed0ce5b1a78f414cd0389a718cf86d42f611f9770b4dd01ec29e647deb1',
  'blockHash' => '0xd2d407edba393d3a3b3bc5f6a2531f6d1905efb16f8e98abd48b382de91dbfb3',
  'blockNumber' => '3257040',
  'status' => 1,
  'timestamp' => 1681053844,
];
ksort($params);
echo hash_hmac('sha256', http_build_query($params), '3eff9e5156401d6dabc84f128004ce34597414c637dc43c5acbee793f382ab80');
```

### 接口返回要求

当商户录入的接口按以下格式返回时，KWeiPay平台则认为数据推送成功。

```json
{
  "code":0
}
```
