# KweiPay-Doc

## KweiPay
KweiPay是一套使用加密数字货币支付&结算系统。区别于传统的三方支付方式，KweiPay有以下明显的优势
- 费率更低
- 结算更快。可即时结算，无需 t+ N
- 交易效率更高。用户充值、商户结算等交易，一般1分钟以内即可到账

## 商户接入KWeiPay方式
商户需要先申请一对 `Api Key & Secret`，用于信息加加密，[API KEY使用说明](ApiKeyManagement.md)


#### 一：直接访问KweiPay收银台
当用户需要给账户充值的时候，商户可以在网页给客户提供一个加密数字货币支付的选项，客户点击此选项，商户网站根据下述文档中转到一个专属链接页面，此页面为KweiPay收银台。客户在此页面完成充值，当用户充值的币到账之后，KweiPay会通过Api给商户推送一条充值到账消息，商户可在自己的系统给这个客户完成上账。

##### 方式一：自主接入，拼接参数访问收银台（适合有编程能力的商户）

1. 商户后台申请API KEY，[API KEY使用说明](ApiKeyManagement.md)
2. 签名
3. 带签名参数根据规则访问收银台页面

+ 访问示例

http://management.kweipay.com/recharge?sign=3da20594f20fc037d1a4c46452f1d3132399ff121deaa83c13a827991406916f&app_id=e124b881d890ca8ba243b2f09620974cd853267babfd40dca53e0265b9375cf5&merchant_uid=100001&merchant_cid=1&chain=usdt_trc20

+ 页面参数

| 参数名称     | 释义                           | 是否必须 |
| :----------- | ------------------------------ | -------- |
| sign         | 签名                           | 是       |
| app_id       | 商户从后台API管理中获取到的key | 是       |
| merchant_uid | 商户在平台的用户ID             | 是       |
| merchant_cid | 用户所在商户平台的用户ID       | 是       |
| chain        | usdt_trc20/usdt_erc20          | 否       |

+ 签名规则

> 将`app_id`、`merchant_uid`、`merchant_cid`以升序进行连接，签名使用`HMAC SHA256`算法。`app_id`所对应的`secret`作为 `HMAC SHA256` 的密钥，其他所有参数作为`HMAC SHA256`的操作对象，得到的输出即为签名。

+ PHP示例

```php
<?php
  $params = [
      'app_id'         => 'SC2jOccNErhhXwuiuuPstUdk97zndL6p2M4XJqxI0HKFzIFyqYsSPeF74RyOznYA',
      'merchant_uid'   => 100002,
      'merchant_cid'   => 1
  ];
  ksort($params);
  echo hash_hmac('sha256', http_build_query($params), 'ExThHGsiDJT7DTvNfWVKI7zU2tZhFHxnR5BO0Zvf4fEGD1BY6R8dDr4rPqtSm09b');
?>
```

##### 方式二：使用商户后台生成的收银台链接（适合用户数量较少的商户，减少开发成本）

1. 商户后台申请API KEY，[API KEY使用说明](ApiKeyManagement.md)

2. 点击右侧`新增`按钮给用户分配钱包地址（已分配的用户请勿重复分配）

   <img width="1973" alt="image-20230328211729397" src="https://user-images.githubusercontent.com/20775615/228236042-deae0a6c-c8d6-4403-8ae9-c9d927dc1878.png">

3. 填入相应信息后系统会自动分配钱包地址

   <img width="1973" alt="image-20230328211825259" src="https://user-images.githubusercontent.com/20775615/228236092-7318ea86-5fa4-4c74-b3f2-42548bcaeebf.png">

4. 点击左侧`钱包地址管理`列表页`充值链接`的详情按钮获取用户充值链接。

   <img width="1973" alt="image-20230328212059066" src="https://user-images.githubusercontent.com/20775615/228236133-8f813d32-9837-445c-aec2-b6710fea445e.png">


#### 二：通过Api接入（适合有编程能力的商户）

商户也可以通过Api接入KweiPay，客户无需要跳出商户网站，所有交互可通过服务端程序通过Api完成。在 `完成开设商户申请并生成密钥对` 之后，商户可以
请求分配充值地址。

> 需先在商户后台申请API KEY，[API KEY使用说明](ApiKeyManagement.md)

##### 接口路径

`https://api.kweipay.com/api/wallets_address`

##### 访问方式

`POST`

##### 参数说明

| 参数名称 | 类型 | 释义 | 是否必须 |
|:--------------:|:------:|:-----------------------|:--:|
|      sign      | string | 签名 | 是 |
|     app_id     | string | 商户从后台API管理中获取到的key | 是 |
|  merchant_uid  | string | 商户在平台的用户ID | 是 |
|  merchant_cid  | string | 用户所在商户平台的用户ID | 是 |
|     chain      | string | usdt_trc20/usdt_erc20 | 是 |
| qrcode_size | int | 二维码宽度，传递该参数后，接口会返回base64的二维码对象，解码后可直接前端展示。若未传递该参数，则不返回二维码对象。 | 否 |

##### 返回参数

```json
{
   // code < 0 代表请求失败
    "code": 1,
   // 状态
    "message": "success",
   // data
    "data": {
       // 币种 & 公链
        "chain": "USDT_TRC20",
       // 分配的地址
        "address": "TSBjEunKJiTRW8ajh8jh8S7kqEaQW7hnvJ",
       // 商户UID
        "merchant_uid": 100002,
       // 用户CID
        "merchant_cid": 2,
       // base64后的二维码对象，解码后可直接前端展示
        "qrcode": "PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4KPHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZlcnNpb249IjEuMSIgd2lkdGg9IjEyMCIgaGVpZ2h0PSIxMjAiIHZpZXdCb3g9IjAgMCAxMjAgMTIwIj48cmVjdCB4PSIwIiB5PSIwIiB3aWR0aD0iMTIwIiBoZWlnaHQ9IjEyMCIgZmlsbD0iI2ZmZmZmZiIvPjxnIHRyYW5zZm9ybT0ic2NhbGUoNC4xMzgpIj48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgwLDApIj48cGF0aCBmaWxsLXJ1bGU9ImV2ZW5vZGQiIGQ9Ik05IDBMOSAxTDEwIDFMMTAgMkw4IDJMOCAzTDkgM0w5IDRMMTAgNEwxMCA1TDkgNUw5IDZMOCA2TDggOEw0IDhMNCA5TDMgOUwzIDhMMCA4TDAgOUwxIDlMMSAxMEwyIDEwTDIgOUwzIDlMMyAxMUw1IDExTDUgMTJMNiAxMkw2IDEzTDcgMTNMNyAxMkw4IDEyTDggMTNMOSAxM0w5IDExTDEwIDExTDEwIDEwTDEyIDEwTDEyIDhMMTQgOEwxNCAxMEwxMyAxMEwxMyAxMUwxNCAxMUwxNCAxMEwxNiAxMEwxNiAxMUwxNyAxMUwxNyAxMkwxNSAxMkwxNSAxNEwxNCAxNEwxNCAxM0wxMyAxM0wxMyAxMkwxMiAxMkwxMiAxMUwxMSAxMUwxMSAxMkwxMiAxMkwxMiAxM0wxMSAxM0wxMSAxNUwxMiAxNUwxMiAxNkwxMCAxNkwxMCAxN0w4IDE3TDggMTlMNyAxOUw3IDE4TDYgMThMNiAxN0w3IDE3TDcgMTZMOCAxNkw4IDE0TDYgMTRMNiAxNUw3IDE1TDcgMTZMNSAxNkw1IDE0TDQgMTRMNCAxM0wyIDEzTDIgMTFMMCAxMUwwIDEzTDIgMTNMMiAxNEwzIDE0TDMgMTVMMSAxNUwxIDE0TDAgMTRMMCAxNUwxIDE1TDEgMThMMCAxOEwwIDE5TDEgMTlMMSAyMEwwIDIwTDAgMjFMMSAyMUwxIDIwTDMgMjBMMyAxOEw0IDE4TDQgMjFMOCAyMUw4IDI1TDkgMjVMOSAyNkw4IDI2TDggMjlMOSAyOUw5IDI3TDEwIDI3TDEwIDI4TDEyIDI4TDEyIDI5TDEzIDI5TDEzIDI4TDEyIDI4TDEyIDI3TDEwIDI3TDEwIDI1TDkgMjVMOSAyMkwxMCAyMkwxMCAyM0wxMiAyM0wxMiAyNEwxMSAyNEwxMSAyNkwxMiAyNkwxMiAyNEwxNCAyNEwxNCAyNkwxMyAyNkwxMyAyN0wxNCAyN0wxNCAyNkwxNSAyNkwxNSAyNUwxNiAyNUwxNiAyNkwxNyAyNkwxNyAyN0wxNiAyN0wxNiAyOEwxNyAyOEwxNyAyOUwyMSAyOUwyMSAyOEwyNSAyOEwyNSAyN0wyNiAyN0wyNiAyNkwyOCAyNkwyOCAyN0wyNyAyN0wyNyAyOEwyNiAyOEwyNiAyOUwyOSAyOUwyOSAyOEwyOCAyOEwyOCAyN0wyOSAyN0wyOSAyMkwyNyAyMkwyNyAyMUwyOSAyMUwyOSAyMEwyOCAyMEwyOCAxOUwyOSAxOUwyOSAxOEwyMyAxOEwyMyAxN0wyNiAxN0wyNiAxNkwyMiAxNkwyMiAxNUwyMSAxNUwyMSAxMkwyMCAxMkwyMCAxMUwyMiAxMUwyMiAxNEwyMyAxNEwyMyAxM0wyNSAxM0wyNSAxMkwyNCAxMkwyNCAxMUwyMyAxMUwyMyAxMEwyMCAxMEwyMCAxMUwxOSAxMUwxOSA5TDIwIDlMMjAgN0wyMSA3TDIxIDZMMjAgNkwyMCA1TDE5IDVMMTkgNEwyMSA0TDIxIDBMMTkgMEwxOSAxTDE4IDFMMTggMkwxOSAyTDE5IDNMMTggM0wxOCA0TDE3IDRMMTcgNUwxOSA1TDE5IDZMMTggNkwxOCA3TDE3IDdMMTcgNkwxNiA2TDE2IDhMMTUgOEwxNSA2TDE0IDZMMTQgNUwxNSA1TDE1IDRMMTYgNEwxNiAzTDE3IDNMMTcgMEwxNiAwTDE2IDFMMTQgMUwxNCAwTDEyIDBMMTIgMUwxMCAxTDEwIDBaTTEzIDFMMTMgMkwxNCAyTDE0IDFaTTExIDJMMTEgM0wxMiAzTDEyIDRMMTEgNEwxMSA1TDEwIDVMMTAgNkw5IDZMOSA3TDEwIDdMMTAgNkwxMSA2TDExIDhMMTIgOEwxMiA2TDEzIDZMMTMgN0wxNCA3TDE0IDZMMTMgNkwxMyA1TDE0IDVMMTQgNEwxMyA0TDEzIDNMMTIgM0wxMiAyWk0xNSAyTDE1IDNMMTYgM0wxNiAyWk0xOSA2TDE5IDdMMjAgN0wyMCA2Wk05IDhMOSAxMEwxMCAxMEwxMCA4Wk0xOCA4TDE4IDlMMTkgOUwxOSA4Wk0yMSA4TDIxIDlMMjMgOUwyMyA4Wk0yNiA4TDI2IDlMMjUgOUwyNSAxMEwyNyAxMEwyNyAxMkwyNiAxMkwyNiAxM0wyNyAxM0wyNyAxNEwyNCAxNEwyNCAxNUwyOCAxNUwyOCAxN0wyOSAxN0wyOSAxNEwyOCAxNEwyOCAxM0wyOSAxM0wyOSA5TDI3IDlMMjcgOFpNNCA5TDQgMTBMNSAxMEw1IDlaTTYgOUw2IDEwTDcgMTBMNyAxMUw2IDExTDYgMTJMNyAxMkw3IDExTDggMTFMOCAxMEw3IDEwTDcgOVpNMTYgOUwxNiAxMEwxNyAxMEwxNyAxMUwxOCAxMUwxOCAxMEwxNyAxMEwxNyA5Wk0xNyAxMkwxNyAxM0wxOCAxM0wxOCAxNEwxNyAxNEwxNyAxNUwxOSAxNUwxOSAxNkwxOCAxNkwxOCAxOEwxNyAxOEwxNyAxOUwxOCAxOUwxOCAxOEwxOSAxOEwxOSAxOUwyMCAxOUwyMCAyMEwyMyAyMEwyMyAxOUwyMiAxOUwyMiAxOEwyMSAxOEwyMSAxOUwyMCAxOUwyMCAxN0wxOSAxN0wxOSAxNkwyMSAxNkwyMSAxNUwyMCAxNUwyMCAxM0wxOSAxM0wxOSAxMlpNMjcgMTJMMjcgMTNMMjggMTNMMjggMTJaTTEzIDE0TDEzIDE1TDE0IDE1TDE0IDE0Wk0xNSAxNEwxNSAxOEwxNCAxOEwxNCAxNkwxMiAxNkwxMiAxOEwxMCAxOEwxMCAxOUw5IDE5TDkgMjBMOCAyMEw4IDIxTDEwIDIxTDEwIDIyTDExIDIyTDExIDIxTDEyIDIxTDEyIDIwTDEzIDIwTDEzIDIxTDE0IDIxTDE0IDIyTDEzIDIyTDEzIDIzTDE0IDIzTDE0IDIyTDE1IDIyTDE1IDIzTDE2IDIzTDE2IDI0TDE3IDI0TDE3IDI1TDIwIDI1TDIwIDI2TDE4IDI2TDE4IDI3TDIzIDI3TDIzIDI1TDIwIDI1TDIwIDI0TDE3IDI0TDE3IDIzTDE4IDIzTDE4IDIyTDE3IDIyTDE3IDIzTDE2IDIzTDE2IDIxTDE1IDIxTDE1IDIwTDE2IDIwTDE2IDE3TDE3IDE3TDE3IDE2TDE2IDE2TDE2IDE0Wk0zIDE1TDMgMTdMMiAxN0wyIDE4TDEgMThMMSAxOUwyIDE5TDIgMThMMyAxOEwzIDE3TDQgMTdMNCAxOEw1IDE4TDUgMTlMNiAxOUw2IDIwTDcgMjBMNyAxOUw2IDE5TDYgMThMNSAxOEw1IDE3TDQgMTdMNCAxNVpNMTMgMThMMTMgMTlMMTQgMTlMMTQgMThaTTExIDE5TDExIDIwTDEyIDIwTDEyIDE5Wk0yNSAxOUwyNSAyNUwyNiAyNUwyNiAyNEwyNyAyNEwyNyAyNUwyOCAyNUwyOCAyNEwyNyAyNEwyNyAyMkwyNiAyMkwyNiAyMUwyNyAyMUwyNyAxOVpNMTcgMjBMMTcgMjFMMTkgMjFMMTkgMjJMMjAgMjJMMjAgMjFMMTkgMjFMMTkgMjBaTTIxIDIxTDIxIDI0TDI0IDI0TDI0IDIxWk0yMiAyMkwyMiAyM0wyMyAyM0wyMyAyMlpNMCAwTDAgN0w3IDdMNyAwWk0xIDFMMSA2TDYgNkw2IDFaTTIgMkwyIDVMNSA1TDUgMlpNMjIgMEwyMiA3TDI5IDdMMjkgMFpNMjMgMUwyMyA2TDI4IDZMMjggMVpNMjQgMkwyNCA1TDI3IDVMMjcgMlpNMCAyMkwwIDI5TDcgMjlMNyAyMlpNMSAyM0wxIDI4TDYgMjhMNiAyM1pNMiAyNEwyIDI3TDUgMjdMNSAyNFoiIGZpbGw9IiMwMDAwMDAiLz48L2c+PC9nPjwvc3ZnPgo="
    }
}
```

#### 三：通过`SDK`接入

```
开发ing，敬请期待~
```

## 开启充值消息推送

商户在商户后台录入有效的Api接口后，即可开启充值消息推送功能，当专属充值地址有入账，KweiPay的上账程序会检测到此交易，并把此消息推送给商户，商户可通过此地址对应的用户上账。[充值消息管理使用说明](OpenRechargeMessagePush.md)

## 开启提币拉取和消息推送

当用户需要提币的时候，可在网站上填写提币表单申请。商户可在商户管理后台完成审核，并使用KweiPay提供的热钱包完成提币申请；或者通过Api将此申请暴露给KweiPay，由KweiPay管理后台完成此笔提币。

## 商户结算

商户可随时在商户管理后台填写申请结算表单，管理后台完成审核之后，会将此币结算申请打到商户指定地址。

## 商户后台演示Demo
