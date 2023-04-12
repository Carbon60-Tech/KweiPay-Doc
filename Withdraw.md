# 提币拉取 & 推送管理
> 商户可以在商户后台录入 `提币拉取` 和 `消息推送` API，KWeiPay会定期拉取用户的提币请求，当商户/管理员审核后，商户使用热钱包处理完提现后即可推送提币最终结果。

## 录入API

1. 点击左侧 `财务管理`->`提现管理`->`提现拉取 & 推送管理`
<img width="1948" alt="image" src="https://user-images.githubusercontent.com/20775615/231494251-db24ce46-0860-492d-9742-dd31e2686506.png">

2. 点击右侧 `新增` 按钮
<img width="1954" alt="image" src="https://user-images.githubusercontent.com/20775615/231494884-dd7f40ee-8c36-4672-bd88-384f93c23eff.png">

3. 录入 `拉取接口` 和 `推送接口`
<img width="1950" alt="image" src="https://user-images.githubusercontent.com/20775615/231495222-ae5818c9-a64d-43fa-9697-4ecf2faa6d46.png">

4. 提交
> 一个商户只能录入一条数据。

## API说明
### 提币拉取接口
#### 接口路径
`以商户录入Api为准`

#### 请求方式
`Get`

#### 请求参数
```json
{
    "sign":"3da20594f20fc037d1a4c46452f1d3132399ff121deaa83c13a827991406916f",        // 鉴权签名
    "pulled_at":1234567890,                                                           // 上次拉取时间的时间戳，请将该时间之后创建的提现数据返回
    "timestamp":1234567890                                                            // 接口请求时的时间戳
}
```

#### 签名规则
1. 对需要参与签名的参数按键名进行降序排序。
2. 将需要参与签名的参数按键1=值1&键2=值2...的方式进行链接。
3. 对步骤2得到的字符串进行hash_hmac 256签名，secret为商户后台申请的secret。
4. 将签名后的数据与sign参数进行对比，一致则代表数据未被伪造/篡改。

#### 签名示例
```php
$params = [
  'pulled_at' => 1234567890,
  'timestamp' => 1234567890
];
ksort($params);
echo hash_hmac('sha256', http_build_query($params), `私钥`); //查看商户后台获取私钥
```

### 接口返回要求

当商户录入的接口按以下格式返回时，KWeiPay平台则认为数据推送成功。

```json
{
  "code":0,
  "data": [
            {
                "withdraw_id": 1,                                 // 提现订单编号，唯一
                "merchant_cid": "yeyeyuki",                       // 用户ID，可不传
                "address": "TK5Rh8xpmyvEhCq6LNPqw9hUX4t1ezSZ76",  // 用户提现地址
                "token": "USDT",                                  // 用户提现token，默认值USDT，大写
                "chain": "TRX",                                   // 用户提现公链，默认TRX，大写
                "amount": 200                                     // 用户提现数量
            },
            {
                "withdraw_id": 2,
                "address": "TK5Rh8xpmyvEhCq6LNPqw9hUX4t1ezSZ76",
                "token": "USDT",
                "chain": "TRX",
                "amount": 200
            },
            {
                "withdraw_id": 3,
                "address": "TK5Rh8xpmyvEhCq6LNPqw9hUX4t1ezSZ76",
                "token": "USDT",
                "chain": "TRX",
                "amount": 200
            },
            {
                "withdraw_id" : 4,
                "address" : "0x86eb26cd9f3ccf81d2ae2be7052759b58d3cfdb4",
                "token" : "USDT",
                "chain" : "ETH",
                "amount" : 200
            },
            {
                "withdraw_id" : 5,
                "address" : "0x86eb26cd9f3ccf81d2ae2be7052759b58d3cfdb4",
                "token" : "USDT",
                "chain" : "ETH",
                "amount" : 200
            }
    ]
}
```

### 提币结果推送接口
### 接口路径
`以商户录入Api为准`

### 请求方式
`POST`

### 请求参数

```json
{
    "sign":"3da20594f20fc037d1a4c46452f1d3132399ff121deaa83c13a827991406916f",        // 鉴权签名
    "withdraw_id":1,                                                                  // 提现订单编号
    "hash":"299c1e2ad916eb747ba8a29c1536279b8cbb9543bfc1dfc7d869a4441144a931",        // 交易哈希
    "status":4                                                                        // 交易状态，4:交易成功；5:交易失败
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
  'withdraw_id' => 1,
  'hash'        => '299c1e2ad916eb747ba8a29c1536279b8cbb9543bfc1dfc7d869a4441144a931',
  'status'      => 4,
  'timestamp'   => 1681053844,
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
