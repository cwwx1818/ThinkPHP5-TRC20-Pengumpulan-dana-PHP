
# 基于ThinkPHP5版本TRC20-资金归集解决方案

源码地址：[源码下载地址](https://www.kancloud.cn/bishengzhu/trc20trxusdt)  

购买账号注册：[点击注册](https://www.kancloud.cn/invite?code=5e902a37a6852)

看云官方网站的文档作者是我：通过QQ找到本人购买源码可以八折优惠，源码会定期优化升级

基于ThinkPHP5版本TRC20-资金归集解决方案，当你有很多钱包（已知地址跟私钥）有USDT，你想把它们全部归集到一个指定的钱包，你就需要用到资金归集功能，自己开发，永远比第三方可靠，本文档仅在SDK上支持，源码无加密，可放心使用。


# 基于ThinkPHP5版本TRC20-资金归集解决方案



>[warning] ## 如果这个功能你一天能自己搞定，就不需要买文档。如果你搞不定，就果断下单，联系我QQ，我会抽空协助（不包括tp环境部署，仅限这个项目相关问题解答，提前看好功能是否满足您的要求再买）


<span style="color:red;font-size:25px">    （这不是系统，是解决方案，涉及到的知识点都会讲到，自己开发的才是最放心的不是吗？一定要看到最底部，</span> <span style="color:green;font-size:30px">有彩蛋</span> <span style="color:red;font-size:25px">    ）</span> 

>[info] 温馨提示：当前文档主要是介绍资金归集的一个方案这个在很多场景都能应用到。
> #### 应用场景
> - 当你有很多钱包（已知地址跟私钥）有USDT，你想把它们全部归集到一个指定的钱包，你就需要用到资金归集功能。
> - 资金归集需要的一个手续费账号（已知地址跟私钥），账号里面有足够的TRX,用于给钱包分配对应数额的手续费。
> - 需要一个收U的账号，只需要地址即可


>[success] #### 答疑
>- 问：为啥要那么多钱包呢？
>- 答：因为有一种场景是你注册一个平台，平台给你分配一个钱包，这个钱包是平台生成的，你向里面转账，平台会监控到账情况 ，到账了就会给你在平台的账号余额里面充值对应数额的平台币。平台的这个钱包里面只有你冲的U，没有手续费，如果需要转出到指定账户，人工来做很麻烦，而且一旦泄露私钥，就没法收拾了。行业内因为私钥被归集的钱包场景太多了，很多老板都不敢用第三方平台，于是 就自己找开发，这不，案例就来了嘛。
>- 问：私钥真的不会被泄露吗？
>- 答：代码无加密，你自己开发的，底层肯定要过一遍的。如果你用三方的成品归集系统我就不敢给你打包票是否安全。
>- 问：市面上成熟的资金归集系统不香吗？
>- 答：香不香老板说了算，私钥泄露了，就不香了。按照老板说的，你看不到代码的资金归集系统都不香




*****
- 当前SDK目前支持波场的 TRX 和 TRC20 中常用生成地址，转账，余额查询，离线签名等功能。
- 一套写法兼容 TRON 网络中 TRX 货币和 TRC 系列所有通证
- 接口方法可可灵活增减


```
/*基础配置*/
$this->config = [
    'contract_address' => 'TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t',// USDT TRC20
    'decimals' => 6, /*精度*/
];
```



>[warning] 可以加我QQ 1113249273，对于目前的应用当中的查询余额，转账，查账等几个功能可以做技术支持。之外的功能不在技术支持范围内，请悉知。
QQ 群：925283872 ，问题回答正确才能入群，PHP基本知识。


# 代码以及返回参数摘要
### 零、TRX转账回参，即手续费从总的账号转到没有手续费待归集清零U的账户。只有有了TRX，你这U才能归集到大BOSS的钱包可不是。result为true的时候才算是成功了，但是这还不算，<span style="color:red;font-size:30px">    因为转TRX有延迟</span>，1-2分钟不等，快的时候可以30秒。所以转U把稳操作是放到队列三分钟后操作。可以给对应的钱包标记一个转TRX的起始时间，这样就能快速筛出来哪些钱包已经达到了转出U的条件，这还不算，转之前还得查一下U跟TRX是否真的OK，反正U都是全部转出，就看TRX够不够，不够再安排TRX转账以及更新TRX的起始时间字段。业务逻辑不过多叙述，我会专门啰嗦一段话去说这个事情。就放在这个markdown最底部，愿意看就去看。
```
{
    "result":true,
    "txid":"72aafccbb9ba4595a3c2d5ec8fd292f6121cde1c424895148b74145dda8574fa",
    "visible":false,
    "txID":"72aafccbb9ba4595a3c2d5ec8fd292f6121cde1c424895148b74145dda8574fa",
    "raw_data":{
        "contract":[
            {
                "parameter":{
                    "value":{
                        "amount":1000000,
                        "owner_address":"41bfb14932a54572576b7afce2362aaa61b73fe52c",
                        "to_address":"41ad9f1cae0fdef98446b7aae9502ff3626c96732d"
                    },
                    "type_url":"type.googleapis.com\/protocol.TransferContract"
                },
                "type":"TransferContract"
            }
        ],
        "ref_block_bytes":"69bb",
        "ref_block_hash":"2ac135d43ba624e8",
        "expiration":1649777049000,
        "timestamp":1649776992092
    },
    "raw_data_hex":"0a0269bb22082ac135d43ba624e840a8fbfff281305a67080112630a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412320a1541bfb14932a54572576b7afce2362aaa61b73fe52c121541ad9f1cae0fdef98446b7aae9502ff3626c96732d18c0843d70dcbefcf28130",
    "signature":[
        "51810c5ee788ed5572f3fedb20630e0a04c8debfbaa7a9ed2c5e3185ce5f8a5f40999dc58557d0ae0e1a60a20e5d9774f51337bfdf03623d88e2d8ca5613eaf201"
    ]
}
```





### 一、生成地址 generateAddress() 回参
~~~
{
    "privateKey": "0xe2ad74294c273467027f80*********6cc2e9a9cd4214ef3418b818d48e66",
    "address": "TLowZwvVHCQSKH8Pjwgo67TPe2dea7grWa",
    "hexAddress": "4176e8c1d6e77d1ce87a0b242366c26f550556b689"
}
~~~

### 二、交易转账 transfer($from,$to,$acount)  回参 

交易转账回参不能判断是否交易成功，还需要根据回参的 `txID` 参数去调用另一个API来实现，继续往下看
~~~
{
   "code": 200,
   "data": {
      "signature": [],
      "txID": "eb150f82dde7eec67bfa84a433397769d6beccac781e01b62fd5dc76515153dc",
      "raw_data": {
         "contract": [
            {
               "parameter": {
                  "value": {
                     "data": "a9059cbb00032200000000000000004144967f55976c06c4fb55b2e310843c25105ba78d00000000000000000000000000000000000000000000000000000000000f4243",
                     "owner_address": "4144967f222206c4fb55b2e310843c25105ba78d",
                     "contract_address": "41a614f803b22280986a42c78ec9c7f77e6ded13c"
                  },
                  "type_url": "type.googleapis.com/protocol.TriggerSmartContract"
               },
               "type": "TriggerSmartContract"
            }
         ],
         "ref_block_bytes": "cb77",
         "ref_block_hash": "904b72d42bb7d1b8",
         "expiration": 1649065326000,
         "fee_limit": 100000000,
         "timestamp": 1649065268704
      },
      "contractRet": "PACKING"
   }
}
~~~

*****
### 三、根据交易哈希查询信息 transactionReceipt($txID)  
回参的contractRet参数为SUCCESS为成功，别的均为不成功。虽然上面的交易api也有这个回参，但是并不会告诉你成功与否，你还得单独调用一次这个接口才能知道结果。
~~~
{
   "code": 1,
   "data": {
      "signature": [],
      "txID": "fad174a7b0fcbd10c2e1c9a***974505f7af6609b8e080956dc2111223e6d",
      "raw_data": {
         "contract": [
            {
               "parameter": {
                  "value": {
                     "data": "a9059cbb00000000000000000004144967f55976c06c4fb55b2e310843c25105ba78d0000000000000000000000000000000000000000000000000000000077359400",
                     "owner_address": "4144967f55976c06c4fb52e3143c25105ba78d",
                     "contract_address": "41a614f803b6fd780986c79c7f77e6ded13c"
                  },
                  "type_url": "type.googleapis.com/protocol.TriggerSmartContract"
               },
               "type": "TriggerSmartContract"
            }
         ],
         "ref_block_bytes": "dfef",
         "ref_block_hash": "a0e7bd2508835fe",
         "expiration": 16488814348000,
         "fee_limit": 100000000,
         "timestamp": 1648884290694
      },
      "contractRet": "SUCCESS"
   }
}
~~~



# 综上，这里说下大体思路以及数据库设计
*****
一、在配置文件加六个配置，如下所示。
1. 用于供应TRX手续费的钱包私钥，这个钱包不需要U，只需要有TRX即可
2. 用于供应TRX的钱包地址（可自动获取，写到配置少一步api请求（调用通过私钥获取地址的api））
3. apikey （不填写也可以，但是怕量大会影响调用上限）
4. api地址，目前是：https://api.trongrid.io
5. 接收U的老板钱包address
6. trx转账归集时间间隔（秒）
*****
二、思路：
等于你一个发手续费的钱包，一个收U的钱包。其余的钱包就是转U用的，手续费不够就走那个手续费钱包拿手续费，再不够就等待。后台有个地方显示手续费钱包的TRX余额，这样就能行程一个闭环，手续费不够就喊老板向里面加就可以了，这个就可以写一个[定时任务](https://www.kancloud.cn/manual/thinkphp5/235129)处理。

*****
三、数据库设计（用于归集的钱包地址的数据库设计）
```

CREATE TABLE `fa_address`
(
    `id`          int(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',
    `address`     varchar(60) COLLATE utf8mb4_unicode_ci  DEFAULT '' COMMENT 'address地址',
    `private_key` varchar(120) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT '私钥',
    `trx`         decimal(16, 6) NOT NULL                 DEFAULT '0.000000' COMMENT 'TRX',
    `usdt`        decimal(16, 6) NOT NULL                 DEFAULT '0.000000' COMMENT 'USDT',
    `txID`        varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '归集订单hash',
    `switch`      tinyint(2) unsigned NOT NULL DEFAULT 0 COMMENT '是否需要归集,0已归集，1待归集',
    `trxtime`     int(10) DEFAULT NULL COMMENT 'trx转入时间',
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='归集管理';
```
字段解释：
1. address ：需要转出U的钱包地址
2. private_key ：要转出U的钱包私钥
3. trx ：当前钱包里面的trx余额
4. usdt ：当前钱包里面的usdt余额
5. txID：最近一次进行归集的订单hash，用于查账用的
6. switch：每当有新的U进来的时候，就会把这个0变成1，归集定时任务脚本就会扫描到
7. trxtime：每次从TRX钱包里面向待归集钱包转入trx的时候，这个字段就会更新，上面的配置六你设定180秒以后才会进行资金归集，这样定时脚本就扫描当前时间跟trxtime大于180秒且 switch为1的钱包进行归集操作，操作完成以后修改switch为0。



>[info]  API 源码对应目录地址 `application/api/controller/Index.php` ，接口文档如下所示
*****


#  **转账接口**


##### 请求URL
- ` /api/index/transfer `
  
##### 请求方式
- POST/GET 

##### 参数

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|toaddress |是  |string |接收方地址   |
|amount |是  |float | 转账金额（最低为1，精度为6）|

##### 返回示例 

```
{
   "code": 1,
   "data": {
      "signature": [],
      "txID": "eb150f82dde7eec67bfa84a433397769d6beccac781e01b62fd5dc76515153dc",
      "raw_data": {
         "contract": [
            {
               "parameter": {
                  "value": {
                     "data": "a9059cbb00032200000000000000004144967f55976c06c4fb55b2e310843c25105ba78d00000000000000000000000000000000000000000000000000000000000f4243",
                     "owner_address": "4144967f222206c4fb55b2e310843c25105ba78d",
                     "contract_address": "41a614f803b22280986a42c78ec9c7f77e6ded13c"
                  },
                  "type_url": "type.googleapis.com/protocol.TriggerSmartContract"
               },
               "type": "TriggerSmartContract"
            }
         ],
         "ref_block_bytes": "cb77",
         "ref_block_hash": "904b72d42bb7d1b8",
         "expiration": 1649065326000,
         "fee_limit": 100000000,
         "timestamp": 1649065268704
      },
      "contractRet": "PACKING"
   }
}
```

##### 返回参数说明 

|参数名|类型|说明|
|:-----  |:-----|-----                           |
|txID |string   |交易哈希值（用于查询转账交易）  |

##### 备注 

- txID 用来查询订单信息从而确定是否交易成功
- 可以查询余额后再转账，否则余额不够转死都转不成功。查询余额的方法返回的是一个负点数金额。


```
/**
 * 获取余额  完整代码可以下载项目源码查看
 * $address 地址对象,通过私钥可以用获取
 * 这里查询的是转账用户的余额
 */
private function getBalance($address)
{
    ...
    /*私钥地址*/
    $balance = $TRC20->balance($address);
    return $balance;
}
```

```
/**
 * 根据私钥获取地址 完整代码可以下载项目源码查看
 * $privateKey 私钥
 */
private function getAddress()
{
    ...
    /*私钥地址*/
    $privateKeyToAddress = $TRC20->privateKeyToAddress($this->privateKey);
    return $privateKeyToAddress;
}
```

*****





#  **查单接口**



##### 请求URL
- ` /api/index/transData `
  
##### 请求方式
- POST/GET 

##### 参数

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|txID |是  |string |交易哈希值   |

##### 返回示例 

```
{
   "code": 1,
   "data": {
      "signature": [],
      "txID": "fad174a7b0fcbd10c2e1c9a***974505f7af6609b8e080956dc2111223e6d",
      "raw_data": {
         "contract": [
            {
               "parameter": {
                  "value": {
                     "data": "a9059cbb00000000000000000004144967f55976c06c4fb55b2e310843c25105ba78d0000000000000000000000000000000000000000000000000000000077359400",
                     "owner_address": "4144967f55976c06c4fb52e3143c25105ba78d",
                     "contract_address": "41a614f803b6fd780986c79c7f77e6ded13c"
                  },
                  "type_url": "type.googleapis.com/protocol.TriggerSmartContract"
               },
               "type": "TriggerSmartContract"
            }
         ],
         "ref_block_bytes": "dfef",
         "ref_block_hash": "a0e7bd2508835fe",
         "expiration": 16488814348000,
         "fee_limit": 100000000,
         "timestamp": 1648884290694
      },
      "contractRet": "SUCCESS"
   }
}
```

##### 返回参数说明 

|参数名|类型|说明|
|:-----  |:-----|-----                           |
|contractRet |string   |交易结果：SUCCESS 代表成功，否则为失败）  |




#### 案例使用方法
>[info] 在当前的项目中开发，可以看下面这个配置调整，如果需要将当前代码作为接口开放，可以看下一章节
*****
### 生成地址，转账，余额查询，离线签名
1.  `application/index/controller/Trc20.php` 里面的 `$this->privateKey` 私钥改成您自己的私钥
2. `application/index/controller/Trc20.php`里面封装的几个常用方法稍微看下就明白了。实在不明白，就部署好代码，使用接口测试，接口文件地址： `api/controller/Index.php`

*****
### 资金归集：` api/controller/Trx.php`
修改下面的配置即可测试相关的资金归集操作 ，`decimals` 为精度，目前设置为6
```

public function __construct(Request $request = null)
{
    parent::__construct($request);
    $this->uri = 'https://api.trongrid.io'; /*API地址*/
    $this->outTxrPrivateKey = '3850eea23a0566fe0e5a*******763f79e98883ae35'; /*转出TRX账号私钥地址*/
    $this->outTxrAddress = 'TTSnSjvfuiTxdC7EeHHRKTChB9qtYvd43M'; /*转出TRX账号Address地址*/
    $this->getUsdtAddress = 'TMhHi3oq5b61nCGP2AECMVhhd8rRQ1vUuh'; /*接收USDT账号Address地址*/
    /*基础配置*/
    $this->config = [
        'contract_address' => 'TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t',// USDT TRC20
        'decimals' => 6,
    ];
    $this->TRX = 10.0;   /*单次转入的手续费*/
}

```



>[info] 这个是资金归集的相关业务代码，具体应用场景需要进行相关的调整

项目中的文件位置 `api/controller/Fundcollection.php`

```
<?php
/**
 * Date: 2022/4/20
 * Time: 23:22
 * 资金归集
 */

namespace app\api\controller;

use app\api\model\Address;
use think\Controller;

class Fundcollection extends Controller
{



    /*
    CREATE TABLE `fa_address`
    (
        `id`          int(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',
        `address`     varchar(60) COLLATE utf8mb4_unicode_ci  DEFAULT '' COMMENT 'address地址',
        `private_key` varchar(120) COLLATE utf8mb4_unicode_ci DEFAULT '' COMMENT '私钥',
        `trx`         decimal(16, 6) NOT NULL                 DEFAULT '0.000000' COMMENT 'TRX',
        `usdt`        decimal(16, 6) NOT NULL                 DEFAULT '0.000000' COMMENT 'USDT',
        `txID`        varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '归集订单hash',
        `switch`      tinyint(2) unsigned NOT NULL DEFAULT 0 COMMENT '是否需要归集,0已归集，1待归集',
        `trxtime`     int(10) DEFAULT NULL COMMENT 'trx转入时间',
        PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='归集管理';
    */


    /*TODO 此归集案例是根据文档里面的数据表进行的业务操作，在这里新建一个模型来Address处理*/
    /**
     * @return void
     * @value $trxlimit 归集钱包最低TRX余额，这个根据实际情况而定
     * api/model/Address.php  对应上表的模型
     * 如果钱包很多，下面是每次取50个处理
     * 归集转账最低金额为1U，少于1 就不需要进行归集处理了
     */
    public function dotmd(){
        $time = (int)bcsub(time(), 180); /*先拿到180秒之前的时间*/
        $list = Address::where(['switch' => ['eq', 1], 'trxtime' => ['lt', $time]])->limit(50)->order('trxtime desc')->select();
        $trxlimit=10;
        if ($list){
            foreach ($list as $k=>$v){
                $trx = new Trx();
                $trxBalance = $trx->balanceTRX($v['address'], $v['private_key']);
                $usdtBalance = $trx->balanceUSDT($v['private_key']);

                if ($usdtBalance < 1) {
                    /*U为0就直接归类为已归集*/
                    $v->switch = 0;
                    $v->trx = $trxBalance;   /*trx 更新为剩余的*/
                    $v->usdt = $usdtBalance;    /*归集成功这个数据的usdt改为查询的U值*/
                    $v->save();
                } else {
                    /*U大于0的时候不管三七二十一就直接继续归集
                    * 1.查看TRX是否够用
                    * 不够用就转，并且更新时间，够用就直接发起归集
                    */
                    if ($trxBalance < $trxlimit) {
//                    /*trx不够*/
                        if ($usdtBalance > 1) {
                            /*转TRX账去了，并且更新相关数据*/
                            $trx->sendTrx($v['address'], $v['private_key']);
                            $v->trxtime = time();
                            $v->switch = 1;
                            $v->usdt = $usdtBalance;
                            $v->save();
                        } else {
                            /*更新为已归集*/
                            $v->switch = 0;
                            $v->trx = $trxBalance;
                            $v->usdt = $usdtBalance;
                            $v->save();
                        }
                    } else {
                        /*资金归集*/
                        $ret = $trx->transferUsdt($v['private_key']);  /*资金归集*/
                        $v->txID = $ret->txID;
                        $v->isout = 2;   /*是否归集:1=未归集，2=待确认,3=已归集*/
                        $v->save();
                    }
                }
            }
        }
    }

    /**
     * @return void
     * 监测钱包情况,定时查看小于1的U的钱包，看下是否有新的钱进入了，有并且大于1U余额，就更新这个钱包为待归集，由上面的定时脚本去处理
     */
    public function checkAddress(){
        $list = Address::where(['usdt' => ['lt', 1]])->limit(500)->order('trxtime desc')->select();
        if ($list){
            foreach ($list as $k=>$v){
                $trx = new Trx();
                $trxBalance = $trx->balanceTRX($v['address'], $v['private_key']);
                $usdtBalance = $trx->balanceUSDT($v['private_key']);
                if ($usdtBalance>1){
                    $v->switch = 1;
                    $v->trx = $trxBalance;
                    $v->usdt = $usdtBalance;
                    $v->save();
                }
            }
        }
    }
}
```




>[danger] # 收集错误情况(一定要重视这些问题)：
1. `地址是正确的为啥报：Contract validate error : account does not exist`     已排查的是手续费TRX不够导致的，[点击查看相关解答](https://learnblockchain.cn/question/2291)
2. 接口是否需要使用apikey？答：可用可不用，最后一个章有详细解答。最好用一下，可以有效避免被限流的情况
