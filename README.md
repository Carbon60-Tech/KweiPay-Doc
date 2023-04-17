# KweiPay-Doc

## KweiPay
KweiPay是一套使用加密数字货币支付&结算系统。区别于传统的三方支付方式，KweiPay有以下明显的优势
- 费率更低
- 结算更快。可即时结算，无需 t+ N
- 交易效率更高。用户充值、商户结算等交易，一般1分钟以内即可到账

## 商户接入KWeiPay方式
商户需要先申请一对 `Api Key & Secret`，用于信息加加密，[API KEY使用说明](ApiKeyManagement.md)

KWeiPay提供了4中方式接入，详细请参考[KWeiPay接入说明](KWeiPayCheckoutCount.md)

## 开启充值消息推送

商户在商户后台录入有效的Api接口后，即可开启充值消息推送功能，当专属充值地址有入账，KweiPay的上账程序会检测到此交易，并把此消息推送给商户，商户可通过此地址对应的用户上账。[充值消息管理使用说明](OpenRechargeMessagePush.md)

## 开启提币拉取和消息推送

当用户需要提币的时候，可在网站上填写提币表单申请。商户可在商户管理后台完成审核，并使用KweiPay提供的热钱包完成提币申请；或者通过Api将此申请暴露给KweiPay，由KweiPay管理后台完成此笔提币。[使用说明](Withdraw.md)

## 商户结算

商户可随时在商户管理后台填写申请结算表单，管理后台完成审核之后，会将此币结算申请打到商户指定地址。

## 商户后台演示Demo
