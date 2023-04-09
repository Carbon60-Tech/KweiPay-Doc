# KweiPay-Doc

### KweiPay
KweiPay是一套使用加密数字货币支付&结算系统。区别于传统的三方支付方式，KweiPay有以下明显的优势
- 费率更低
- 结算更快。可即时结算，无需 t+ N
- 交易效率更高。用户充值、商户结算等交易，一般1分钟以内即可到账

### 商户接入方式
商户需要先申请一对 `Api Key & Secret`，用于信息加加密，[API KEY使用说明](ApiKeyManagement.md)


##### 1、KweiPay收银台
当用户需要给账户充值的时候，商户可以在网页给客户提供一个加密数字货币支付的选项，客户点击此选项，商户网站根据下述文档中转到一个专属链接页面，此页面为KweiPay收银台。客户在此页面完成充值，当用户充值的币到账之后，KweiPay会通过Api给商户推送一条充值到账消息，商户可在自己的系统给这个客户完成上账。


```
这里补充通过收银台接入的文档
```

##### 2、Api
商户也可以通过Api接入KweiPay, 客户无需要跳出商户网站，所有交互可通过服务端程序通过Api完成。在 完成开设商户申请并生成密钥对 之后，商户可以
1、请求分配充值地址。KweiPay会给商户分配一批充值地址，商户将此充值地址导入到本地数据库，给不同用户分配专属充值地地址。
```
[{
	"walletName": "merchant name",
	"index": 0,
	"token": 0,
	"tokenName": "USDT_TRC20",
	"address": "TP95XiwrkU1VkL43TuwkPivHiDT3wjc3nj"
}, {
	"walletName": "merchant name",
	"index": 1,
	"token": 0,
	"tokenName": "USDT_TRC20",
	"address": "TFbrbxKfLMbCc3E1sXYugLEcF1No8a6YpW"
}, {
	"walletName": "merchant name",
	"index": 2,
	"token": 0,
	"tokenName": "USDT_TRC20",
	"address": "TVbRcdVJogzwY12s1u3SPEqZBWWCVuBNWQ"
}, {
	"walletName": "merchant name",
	"index": 3,
	"token": 0,
	"tokenName": "USDT_TRC20",
	"address": "TS6cvrY66RT2UqWyuMQNujJJvgwoFuNw8B"
},
.......
]
```

2、充值。当专属充值地址有入账，KweiPay的上账程序会检测到此交易，并把此消息推送给商户，商户可通过此地址对应的用户上账。
```
这里补充充值推送的api文档
```

3、提币。当用户需要提币的时候，可在网站上填写提币表单申请。商户可在商户管理后台完成审核，并使用KweiPay提供的热钱包完成提币申请；或者通过Api将此申请暴露给KweiPay，由KweiPay管理后台完成此笔提币。
```
这里补充管理后台获取提币申请的的Api接入文档
```

4、结算。商户可随时在商户管理后台填写申请结算表单，管理后台完成审核之后，会将此币结算申请打到商户指定地址

##### 3、SDK
```
开发ing
```

### 商户后台演示Demo
