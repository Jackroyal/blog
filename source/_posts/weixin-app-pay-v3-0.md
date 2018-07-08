title: 微信支付app支付3.0接口开发
date: 2015-10-25 21:30:03
tags:
- 微信支付
- api
categories:
- php
---
最近在做微信支付,因为前段时间做了微信的公众账号支付,我以为是一个东西,结果发现不是,我真是醉了,竟然是独立的两套东西.
<!-- more -->
![微信支付架构图](https://ww4.sinaimg.cn/large/692869a3gw1exdqakso33j20ny0dptbi.jpg)
整个微信支付,分为三大平台,公众平台(就是公众账号那个),开发平台(主要针对app这块),商户平台(所有微信支付的结算,最终在这里).三个平台的账号都不同,而且必须不同,不然不让你注册.
其中,需要用户注册的是公众平台和开放平台,当你审核通过以后,就会给你分配一个对应的商户号.也就是说,你一个公司,申请一个公众号和一个开放平台账号,分别给你一个商户号,你就一共有两个商户号.这两个商户号不同,我之前就是拿着公众账号对应的商户号(客户给错了),去做app支付,一直说不对应.
下面进入正文,看看怎么开发
# 准备工作
首先你需要注册[开放平台](https://open.weixin.qq.com/)账号,然后添加一个app应用(里面包含你的appid和appkey),并且进行认证,然后就会收到一封邮件,里面包含了你分到的商户号.

# 1 流程图
接下来,我们需要搞清楚微信app支付开发的流程,如下图所示.
![微信app支付流程图](https://ww1.sinaimg.cn/large/692869a3gw1exgxrb07bfj20ou0sy453.jpg)
其中,红色的部分是需要我们做的,具体分下来,客户端要做的事就比较少,生成预支付订单,返回签名的package这些都是服务端做的(开发客户端真爽),当然从另外一个角度来说,这些事情也确实应该放在服务器端来做,因为涉及到一些key和密钥之类的东西,放在客户端app中不安全,如果app被反编译就暴露了这些信息.
```
商户系统和微信支付系统主要交互说明：
步骤1：用户在商户APP中选择商品，提交订单，选择微信支付。
步骤2：商户后台收到用户支付单，调用微信支付统一下单接口。参见【统一下单API】。
步骤3：统一下单接口返回正常的prepay_id，再按签名规范重新生成签名后，将数据传输给APP。参与签名的字段名为appId，partnerId，prepayId，nonceStr，timeStamp，package。注意：package的值格式为Sign=WXPay
步骤4：商户APP调起微信支付。api参见本章节【app端开发步骤说明】
步骤5：商户后台接收支付通知。api参见【支付结果通知API】
步骤6：商户后台查询支付结果。，api参见【查询订单API】
```
服务端要做的,就是步骤1235,客户端做步骤4就行了,步骤6看自己的需求,我们没有做.
首先,生成商户服务器订单,这个自不必说,只有生成订单,才有订单号,才能做后面的工作.
我们重点看下步骤2和步骤3

# 2  调用统一下单接口
首先,我们需要看一下[统一下单接口](https://pay.weixin.qq.com/wiki/doc/api/app.php?chapter=9_1)文档,里面包含了请求的地址和要传的参数,顾名思义,那些必填字段是必须要填写的,这是我的请求参数列表`out_trade_no`,`body`,`total_fee`,`time_start`,`time_expire`,`spbill_create_ip`,`notify_url`,`trade_type`还有一个签名`sign`,这就是所有的请求字段.
## 签名
签名的思路是,把所有得除`sign`字段以外的字段,按照参数名ASCII码从小到大排序,使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA。在stringA最后拼接上key得到stringSignTemp字符串，并对stringSignTemp进行MD5运算，再将得到的字符串所有字符转换为大写，得到sign值signValue。[官方文档](https://pay.weixin.qq.com/wiki/doc/api/app.php?chapter=4_3).

签名可能有点蛋疼,你可以看[这个](https://pay.weixin.qq.com/wiki/doc/api/app.php?chapter=4_3)文档.微信也提供了签名的[在线调试工具](https://pay.weixin.qq.com/wiki/tools/signverify/),你把参数填进去,看看签名拿到的值是否和你签名的结果一样.

## 调用unifiedOrder
由于统一下单接口,所有的支付方式都会调用,包括公众账号的几种支付方式,而且官方没有app支付的sdk代码,所以我们直接用公众号支付的js sdk代码.
sdk里面已经封装好了对统一下单接口的调用,包括签名,所以我们只需要调用这个就好了.

## 整理参数
调用统一下单接口后,会返回很多数据,我们还是调用js sdk里面的处理函数,因为返回的是xml的内容,有些数据我们不要,(下面的结果是js api的,结果跟app除了trade_type不同,其他都是一样的)
```
<xml>
   <return_code><![CDATA[SUCCESS]]></return_code>
   <return_msg><![CDATA[OK]]></return_msg>
   <appid><![CDATA[wx2421b1c4370ec43b]]></appid>
   <mch_id><![CDATA[10000100]]></mch_id>
   <nonce_str><![CDATA[IITRi8Iabbblz1Jc]]></nonce_str>
   <sign><![CDATA[7921E432F65EB8ED0CE9755F0E86D72F]]></sign>
   <result_code><![CDATA[SUCCESS]]></result_code>
   <prepay_id><![CDATA[wx201411101639507cbf6ffd8b0779950874]]></prepay_id>
   <trade_type><![CDATA[JSAPI]]></trade_type>
</xml>
```
上面的参数中,我们需要appid(就是你配置中的那个appid),prepay_id(预支付id,之前都是为了它),partnerid(就是你配置的商户号mch_id),其他的就没啥用了,我们接下来要给客户端返回一个数据包,全部的数据如下
```
{"appid":"wx8c965dd8b4794241","noncestr":"oxh4g98rfgbmugwbmxfg72ay6qpvieos","package":"Sign=WXpay","partnerid":"1277670101","prepayid":"wx2015102014523449175fc2fd0939076028","timestamp":"1445323951","sign":"7F84997FDW40F6F15DD1C28A9E313122"}
```
`noncestr`是重新生成的,`package`是固定写法,里面的内容必须写`"Sign=WXpay"`,`timestamp`也是重新生成的,`sign`是重新签名后的结果.
然后把数据返回给客户端就行了,客户端调起支付.

# 3  支付回调
和支付宝原理一样,不过微信返回的数据不是标准的`post`,所以你没法通过`$_POST['out_trade_no']`这样来获取数据.
所以,我的做法还是调用js sdk里面的回调方法,把那个回调类继承了一下,我们只需要重写`NotifyProcess`函数就行了,在这里面加入自己的逻辑,比如判断订单是否存在,订单是否已经处理过之类的.
至此,大流程已经走通了.
# 4  优化
在调试过程中,我发现同一个订单号不能重复的去获取预支付的prepay_id,所以,我们在整理参数那一步后,需要将返回参数存到数据库,下次申请支付的时候,先去数据库查一下,有的话,就不用给微信服务器请求了


打完收工.

# 参考文献
1 [微信官方sdk下载](https://pay.weixin.qq.com/wiki/doc/api/app.php?chapter=11_1)
