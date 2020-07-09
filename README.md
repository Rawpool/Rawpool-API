# Rawpool API 文档 v1.0

* 首先需要进行OAuth验证来获取属于您账户的oauth_token，然后才能使用我们提供的接口

### /oauth2/token 获取您账户的oauth_token（接口调用凭证）

------------

#### 接口请求说明
```
POST https://api.rawpool.com/oauth2/token
```

#### 请求参数

|  参数   |  参数类型  |  是否必传  |  说明  |
| :------------: | :------------: | :------------: | :------------: |
|  username  |  String  | 是 |  您在矿池的登录用户名或手机号，手机号码前需要加 +86     |
|  password |  String |是 |  您在矿池的登录密码 |
|  grant_type | String  | 是| 固定值 "password"|
|  client_id | String  | 是| 分配给您的 client_id |
|  client_secret | String  | 是| 分配给您的 client_secret | |

#### 返回值说明
* 正常情况下，我们会返回如下json数据包给调用者

```
{
    "access_token": "e058c2582c38d6366624d9e022593770",
    "expires_in": 31536000, //凭证有效期，一年
    "scope": null
}
```

####  当发生错误时，api将返回一个错误码 error_code 和错误信息 error，下同，不再赘述
```
{
	"error":"invalid_request",
	"error_code":10001
}
```



### /pool/getMinerList 获取您的子账号列表

------------

* 用于获取您的账号下所有子账号的详细信息 


#### 接口请求说明
```
POST https://api.rawpool.com/pool/getMinerList
```

#### 请求参数

|  参数   |  参数类型  |  是否必传  |  说明  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType   |  String  | 是 |  币种，需要大写，如：BTC  |
|  oauth_token  |  String |是 |  OAuth 授权返回的 access_token  |

#### 返回值说明
* 正常情况下，我们会返回如下json数据包给调用者

```
{
    "code": "0",
    "message": "ok",
    "data": [
        {
            "minerId": "2775", //子账户id
            "name": "testSubAccount", //子账户名称
            "isOwner": "1",
            "roleList": [ //该子账户的权限、角色信息
                {
                    "id": "1",
                    "name": "owner",
                    "displayNameCn": "拥有",
                    "displayNameEn": "Owner"
                }
            ],
            "endpoint": "",
            "withdrawAddress": "1GtBttDWD5aXXPxJEQCBg3RMdppmPXm4fp",//打款地址
            "withdrawMinAmount": "1000000",//最小打款金额
            "lowEfficiencyHashRate": null,//用户配置的低效矿机算力阈值
            "workerCountAlarmEnabled": "0",
            "hashRateAlarmEnabled": "0",//矿机低算力警告通知是否启用：0-关闭；1-启用
            "expectedWorkerCount": null,
            "expectedHashRate": null,
            "alarmInterval": null, //低算力警告通知发送间隔
            "withdrawOption": "1",
            "withdrawAddressUtime": "0",//打款地址更新时间
            "locked": "0",//是否锁定打款地址
            "lockedUtime": "0",//锁定时间
            "billNotificationEnabled": "0",//是否开启账单通知：1-开启；0-关闭
            "ownerUsername": "songtest",//所属账号
            "rangerCoinType": "BTC"//默认挖矿地址
        },{
			... Same as above
		}
    ]
}
```

### /pool/getEarnStatSummary

------------

* 您可以从Web端查看您的昨日收益金额，也可以调用我们的api获取您的收益信息

#### 接口请求说明

```
POST https://api.rawpool.com/pool/getEarnStatSummary
```

#### 请求参数

|  参数   |  参数类型  |  是否必传  |  说明  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType    |  String  | 是 |  币种，需要大写，如：BTC   |
|  minerId  |  int |是 |  子账号id，在 getMinerList 可以获取 |
|  timezone  | int  | 否| 时区 默认是 0，北京时间请传 8 |
|  oauth_token  | String  | 是| OAuth 授权返回的 access_token | |

#### 返回值说明

* 正常情况下，我们会返回如下json数据包给调用者

```
{
    "code": "0",
    "message": "ok",
    "data": {
        "earn24h": "832313",
        "earnLastD": "832313", //昨日收益，上同
        "earnCurrentD": "291449", //今日收益
        "earnEstCurrentD": "799352", //今日预计收益
        "earnWhole": "737061838", //累计支付收益
        "earnUnpaid": "2682975" //未支付收益
    }
}
```

### /pool/getWorkerList

------------

* 获取矿机列表

#### 接口请求说明

```
POST https://api.rawpool.com/pool/getWorkerList
```

#### 请求参数

|  参数   |  参数类型  |  是否必传  |  说明  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType    |  String  | 是 |  币种，需要大写，如：BTC   |
|  minerId  |  int |是 |  子账号id，在 getMinerList 可以获取 |
|  workerGroupId  | int  | 是 | 矿机组 传固定值 -1 获取全部分组的矿机 |
|  hashRateTimeQuantum  | String  | 是 | 算力统计时间段，可选值： 1m - 1分钟； 5m - 5分钟； 15m - 15分钟； 1h - 1小时； 1d - 1天 |
|  start    |  int  | 是 |  分页起点（建议： 0）   |
|  size    |  int  | 是 |  分页大小（建议： 500）   |
|  order[]    |  StringArray  | 是 |  排序，可选值：workerName - 矿机名；revisedHashRate - 算力； <br> lastShareTimelastShareTime - 最近提交时间； rejectRate - 拒绝率； <br>以上所有属性后缀加以上所有属性后缀加 desc 可倒序 |
|  pageDisabled     |  int  |否 |  不分页：需要不分页的话传 1   |
|  oauth_token  | String  | 是| OAuth 授权返回的 access_token | |

#### 返回值说明

* 正常情况下，我们会返回如下json数据包给调用者

```
{
    "code": "0",
    "message": "ok",
    "data": {
        {
            "workerId": "49759979635", //矿机id
            "coinType": "BTC", //币种
            "workerFullName": "songtestBTC.168X113X8", //带子账号名称的矿机全名
            "minerId": "2791", //所属子账号id
            "workerName": "168X113X8", //矿机名称
            "workerGroupId": "0", //矿机组
            "lastShareTime": "1594269182", //最后提交时间
            "revisedHashRate15m": "48014997186622", //15分钟平均算力
            "revisedHashRate1h": "51974999231588", // 1小时平均算力
            "revisedHashRate1d": "48657625481275", //1天平均算力
            "rejectRate": "0.0000", //拒绝率,小数制
            "ctime": "1587162061",  //创建时间
            "utime": "1594269182", //更新时间
            "visibility": "0",
            "favorite": "0",
            "revisedHashRate": "48014997186622", //算力
            "efficiencyLevel": "1"
        },
		...同上
    }
}
```


### /pool/getWorkerCount

------------

* 获取矿机数量（包含无效矿机）

#### 接口请求说明

```
POST https://api.rawpool.com/pool/getWorkerCount
```

#### 请求参数

|  参数   |  参数类型  |  是否必传  |  说明  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType    |  String  | 是 |  币种，需要大写，如：BTC   |
|  minerId  |  int |是 |  子账号id，在 getMinerList 可以获取 |
|  workerGroupId  | int  | 是 | 矿机组 传固定值 -1 获取全部分组的矿机 |
|  hashRateTimeQuantum  | String  | 是 | 算力统计时间段，可选值： 1m - 1分钟； 5m - 5分钟； 15m - 15分钟； 1h - 1小时； 1d - 1天 |
|  oauth_token  | String  | 是| OAuth 授权返回的 access_token | |

#### 返回值说明

* 正常情况下，我们会返回如下json数据包给调用者

```
{
    "code": "0",
    "message": "ok",
    "data": "20" //矿机数量
}
```


## 注意: 所有接口都是采用https协议, form-data 请求
