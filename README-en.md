# Rawpool API Document v1.0

*  You need to perform OAuth verification to obtain the oauth_token belonging to your account, then you can use the interface we provided.

### /oauth2/token get your account oauth_token

------------

#### API Request Format
```
POST https://api.rawpool.com/oauth2/token
```

#### Parameters

|  param   |  type  |  Required  |  Description  |
| :------------: | :------------: | :------------: | :------------: |
|  username  |  String  |  YES  |  Your login user name of Rawpool or you phone number with country code (For example, the country code for China is +86)|
|  password |  String | YES  |  Your login password of  |
|  grant_type | String  |  YES | Fixed value  "password"|
|  client_id | String  |  YES | Assigns you client_id |
|  client_secret | String  |  YES | Assigns you client_secret | |

#### Response Description
* Under normal circumstances ,Rawpool will return the following JSON packet to the caller.

```
{
    "access_token": "e058c2582c38d6366624d9e022593770",
    "expires_in": 31536000, //Validity of certificate，one year
    "scope": null
}
```

#### When an error occurs, Rawpool will return an not 0 error_code and related information.
```
{
	"error":"invalid_request",
	"error_code":10001
}
```



### /pool/getMinerList Obtained your sub account list

------------

* Used to obtain detailed information of all sub-accounts under your account


#### API Request Format
```
POST https://api.rawpool.com/pool/getMinerList
```

#### Parameters

|  param   |  type  |  Required  |  Description  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType   |  String  |  YES  |  The coin type needs to be capitalized：BTC  |
|  oauth_token  |  String |  YES  |  access_token  |

#### Response Description
* Under normal circumstances ,Rawpool will return the following JSON packet to the caller.

```
{
    "code": "0",
    "message": "ok",
    "data": [
        {
            "minerId": "2775", //Sub account id
            "name": "testSubAccount", //sub account name
            "isOwner": "1",
            "roleList": [ //The permissions and role information of the sub account
                {
                    "id": "1",
                    "name": "owner",
                    "displayNameEn": "Owner"
                }
            ],
            "endpoint": "",
            "withdrawAddress": "1GtBttDWD5aXXPxJEQCBg3RMdppmPXm4fp",//Payment address
            "withdrawMinAmount": "1000000",//The Minimum payment threshold
            "lowEfficiencyHashRate": null,//The low-efficiency miners’ threshold value configured by the user
            "workerCountAlarmEnabled": "0",
            "hashRateAlarmEnabled": "0",//Enable alert notification of ineffective miners  ：0-off；1-on
            "expectedWorkerCount": null,
            "expectedHashRate": null,
            "alarmInterval": null, //The alert notifications interval of ineffective miners.
            "withdrawOption": "1",
            "withdrawAddressUtime": "0",//Update time of payment address.
            "locked": "0",//To lock the payment address：0-Yes；1-no
            "lockedUtime": "0",//The locked time
            "billNotificationEnabled": "0",//bill notification ：1-Activated；0-Deactivated
            "ownerUsername": "songtest",//Sub account under each account
            "rangerCoinType": "BTC"//The default mining address
        },{
			... Same as above
		}
    ]
}
```

### /pool/getEarnStatSummary

------------

* You can view your yesterday's earnings from the Website, or you can call our API to get your earnings information.

#### API Request Format

```
POST https://api.rawpool.com/pool/getEarnStatSummary
```

#### Parameters

|  param   |  type  |  Required  |  Description  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType    |  String  |  YES  |  The coin type needs to be capitalized：BTC   |
|  minerId  |  int | YES  |  The sub account id can be obtained at getMinerList |
|  timezone  | int  |  NO | The default time zone is 0 |
|  oauth_token  | String  |  YES | access_token | |

#### Response Description

* Under normal circumstances ,Rawpool will return the following JSON packet to the caller.

```
{
    "code": "0",
    "message": "ok",
    "data": {
        "earn24h": "832313",
        "earnLastD": "832313", //Yesterday’s earnings, same as above
        "earnCurrentD": "291449", //Today’s earnings
        "earnEstCurrentD": "799352", //Today’s estimated earnings
        "earnWhole": "737061838", //Total payouts
        "earnUnpaid": "2682975" //Unpaid income
    }
}
```

### /pool/getWorkerList

------------

* Get worker list

#### API Request Format

```
POST https://api.rawpool.com/pool/getWorkerList
```

#### Parameters

|  param   |  type  |  Required  |  Description  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType    |  String  |  YES  |  The coin type needs to be capitalized：BTC   |
|  minerId  |  int | YES  |  The sub account id can be obtained at getMinerList  |
|  workerGroupId  | int  |  YES  | Fixed value -1 to get all worker |
|  hashRateTimeQuantum  | String  |  YES  | 算力统计时间段，可选值： 1m - 1分钟； 5m - 5分钟； 15m - 15分钟； 1h - 1小时； 1d - 1天 |
|  start    |  int  |  YES  |  Paging start（default： 0）   |
|  size    |  int  |  YES  |  Paging size（default： 500）   |
|  order[]    |  StringArray  |  YES  |  排序，可选值：workerName - 矿机名；revisedHashRate - 算力； <br> lastShareTimelastShareTime - 最近提交时间； rejectRate - 拒绝率； <br> 以上所有属性后缀加以上所有属性后缀加 desc 可倒序 |
|  pageDisabled     |  int  | NO  |  需要不分页的话，这一项传 1   |
|  oauth_token  | String  |  YES | access_token | |

#### Response Description

* Under normal circumstances ,Rawpool will return the following JSON packet to the caller.

```
{
    "code": "0",
    "message": "ok",
    "data": {
        {
            "workerId": "49759979635",
            "coinType": "BTC",
            "workerFullName": "songtestBTC.168X113X8", //Full name of worker with sub account name
            "minerId": "2791",
            "workerName": "168X113X8",
            "workerGroupId": "0",
            "lastShareTime": "1594269182",
            "revisedHashRate15m": "48014997186622", // 15 minutes average Hashrate
            "revisedHashRate1h": "51974999231588", // an hour average Hashrate
            "revisedHashRate1d": "48657625481275", // one day average Hashrate
            "rejectRate": "0.0000",
            "ctime": "1587162061",  //Creat time
            "utime": "1594269182", //Update time
            "visibility": "0",
            "favorite": "0",
            "revisedHashRate": "48014997186622", // Hashrate
            "efficiencyLevel": "1"
        },
		...同上
    }
}
```


### /pool/getWorkerCount

------------

* Get worker count

#### API Request Format

```
POST https://api.rawpool.com/pool/getWorkerCount
```

#### Parameters

|  param   |  type  |  Required  |  Description  |
| :------------: | :------------: | :------------: | :------------: |
|  coinType    |  String  |  YES  |  The coin type needs to be capitalized：BTC   |
|  minerId  |  int | YES  |  The sub account id can be obtained at getMinerList  |
|  workerGroupId  | int  |  YES  | Fixed value -1 to get all worker |
|  hashRateTimeQuantum  | String  |  YES  | 算力统计时间段，可选值： 1m - 1分钟； 5m - 5分钟； 15m - 15分钟； 1h - 1小时； 1d - 1天 |
|  oauth_token  | String  |  YES | access_token | |

#### Response Description

* Under normal circumstances ,Rawpool will return the following JSON packet to the caller.

```
{
    "code": "0",
    "message": "ok",
    "data": "20" //number of worker
}
```


## note: All interfaces are using https protocol, form-data request
