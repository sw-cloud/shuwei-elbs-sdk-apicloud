<p style="color: #ccc; margin-bottom: 30px;">来自于：数位传媒科技有限公司</p>

<div class="outline">

[initLocateSDK](#a1)

[startService](#a2)

[stopService](#a3)

[startLocate](#a4)

[startCycleLocate](#a5)

[stopCycleLocate](#a6)

[submitCollectData](#a7)

[queryCollectData](#a8)

[deleteCollectData](#a9)

</div>

# **概述**

**数位传媒科技有限公司简介**

[数位传媒科技有限公司](http://www.szshuwei.com/)是专注的城市数字化建设者，基于机器视觉技术和多信号指纹定位技术等创新人工智能技术，实现室内物理空间的数字化构建和感知，并为移动互联网、AIoT、智慧城市建设等领域提供服务。

**场景识别功能服务**

- 定位功能：区别于高德、百度等产品的室外定位能力，我们主要是精准的室内定位能力，定位到的位置信息包括用户所在的城市、行政区域、街道、商圈、楼宇、楼层、店铺及经纬度等信息。
- 采集功能：客户可以自己采集室内的位置信息并上传到数位云服务器，之后就可以在采集过的区域进行定位了，定位返回的数据取决于采集时上传的数据。
- 采集查询功能：客户可以查询采集过的位置信息。
- 采集删除功能：客户可以删除采集过的位置信息。

**场景识别模块概述**

本模块封装了数位传媒科技场景识别Android SDK的核心功能，集成了主动定位、周期定位、采集、采集查询、采集删除等功能。客户接入后，可以快速实现室内精准位置定位的能力。**（目前只支持Android平台）**

**工作原理**

模块通过上传用户的设备数据和手机获取到的当前位置的信号信息（主要是必传的WiFi信号，同时也包括基站、蓝牙、光强、地磁、方向等非必传信号）到服务器来定位；

周期定位运行过程中会通过定时器设定每隔一定周期在设备权限允许的前提下扫描设备的WiFi列表，并通过与最近一次的扫描结果进行比较，上一次信号变化超过设定的阈值，调用定位接口进行定位；

应用列表、设备信息等其他的上报数据通过设定周期，定期在定位接口中同时上报数据到服务器。

**模块使用攻略**

使用之前须从[数位云平台](https://cloud.papakaka.com/)申请开发者账号并创建应用，获取 appid 和 appkey。

**使用此模块之前须先配置  [config.xml](https://docs.apicloud.com/Dev-Guide/app-config-manual) 文件，配置完成之后，需提交代码并通过云端编译生效，配置方法如下：**

- 参数：com.shuwei.location.APP_ID、com.shuwei.location.APP_KEY
- 配置示例：

```xml
    <permission name="readPhoneState" />
    <permission name="location" />
    <permission name="internet" />
    <meta-data name="com.shuwei.location.APP_ID" value="您应用的 appid" />
    <meta-data name="com.shuwei.location.APP_KEY" value="您应用的 appkey" />
```

- 字段说明：

  **com.shuwei.location.APP_ID**：（必须配置）从数位云开发平台获取的 appid。
  **com.shuwei.location.APP_KEY**：（必须配置）从数位云开发平台获取的 appkey。
  
- 权限说明：

  该模块需要读取手机状态权限、定位权限及网络权限

## [实例widget下载地址](https://github.com/sw-cloud/shuwei-elbs-sdk-apicloud)


## **模块接口**

<div id="a1"></div>

# **initLocateSDK**

初始化场景识别模块。

initLocateSDK({params})

## params

debug：

- 类型：布尔型
- 描述：（可选项）是否开启debug模式，如果打开，则会输出模块内部的运行日志。
- 默认值：false

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.initLocateSDK({
    debug: true
});
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a2"></div>

# **startService**

启动场景识别服务，场景识别模块中的所有功能，都依赖于场景识别服务的启动。

startService(callback(ret))

## callback(ret)

ret：

- 类型：JSON 对象
- 内部字段：

```js
{
    status: true      //布尔类型；true||false；当前服务是否启动成功
}
```

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.startService(function(ret) {
    if (ret.status) {
        alert("服务启动成功");
    } else {
        alert("服务启动失败");
    }
});
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a3"></div>

# **stopService**

停止场景识别服务，当不再需要使用场景识别模块中的功能时，可以停止场景识别服务以释放服务资源。

stopService()

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.stopService();
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a4"></div>

# **startLocate**

调用主动定位接口，获取精准的室内位置信息。主动定位是每调用一次只获取一次定位结果。

startLocate({params}, callback(ret, err))

## params

ext：

- 类型：字符串
- 描述：（可选项）扩展字段，有些应用场景或者业务下，集成方需要在场景识别的时候加上自己的标识或者数据，因此我们开放了一个扩展参数字段。扩展参数可以根据客户的要求来配置是否在对应的回调接口中返回。如果配置为返回，则会原样返回；如果配置为不返回，则不会返回该字段。
- **特别注意：扩展参数请尽量勿传入过长的字符串，以减少无谓的的带宽流量消耗，目前暂时没有对此扩展参数长度进行限制，但是不保证后期前/后端不会进行的截取操作，如有任何问题，请务必和我们进行沟通协商。**

passThrough：

- 类型：字符串
- 描述：（可选项）透传字段。透传字段和扩展字段的使用类似，不过透传字段是不需要通过后台配置是否返回的，而是一定会在对应的回调接口中原样返回的。主要用来区分不同触发请求的。
- **特别注意：透传参数请尽量勿传入过长的字符串，以减少无谓的的带宽流量消耗，目前暂时没有对此透传参数长度进行限制，但是不保证后期前/后端不会进行截取操作，如有任何问题，请务必和我们进行沟通协商。**


## callback(ret, err)

ret：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 0,      		 			//数字类型；错误码
    msg: 'OK',      				//字符串类型；错误信息说明
    timestamp: 1586585334775, 		//数字类型；时间戳
    poiId: '36d53dc0c473d38b', 		//字符串类型；POI的 id
    name: 'zara',					//字符串类型；POI名称
	floorName: '1',					//字符串类型；楼层
	buliding: '海岸城购物中心',		//字符串类型；楼宇
	area: '海岸城购物中心',			//字符串类型；商圈
	address: '文心四路14',		      //字符串类型；详细地址
	street: '文心四路14',			  //字符串类型；街道
	region: '南山区',				  //字符串类型；行政区
	city: '深圳市',				  //字符串类型；城市
	brand: 'zara',					//字符串类型；品牌
	areaCategory: '购物中心;商场',    //字符串类型；商圈分类
	areaAttribute: "档次:高档',      //字符串类型；商圈属性
	poiCategory: '购物;服装",        //字符串类型；POI分类
	poiAttribute: '风格:潮流时尚|品牌来源:欧美',    //字符串类型；POI属性
	coordinateSystem: 'GCJ02',     //字符串类型；经纬度坐标系类型：BD09-百度坐标系  GCJ02-火星坐标系  WGS84-地球坐标系
	longitude: 113.936074,		   //数字类型；经度
	latitude: 22.51703,            //数字类型；纬度
    ext: 'ext22',				   //字符串类型；定位时上传的扩展字段
	passThrough: 'pt22',		   //字符串类型；透传字段
    collectExt: '{\"name\": \"zhangsan\", 	\"age\": 18 }',     //字符串类型；采集时上传的透传字段
    requestSign: '439d8190a0ac977306ae436859bd4a9b25a64ada'		//字符串类型；请求签名
}
```
err：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 201001,      		 		//数字类型；错误码
    msg: '定位无结果'      			 //字符串类型；错误信息说明
}
```

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.startLocate({
    ext: 'ext11',
    passThrough: 'pt11'
}, function(ret, err) {
    if (ret) {
        alert(JSON.stringify(ret))
    } else {
        alert(JSON.stringify(err));
    }
});
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a5"></div>

# **startCycleLocate**

调用周期定位接口，获取精准的室内位置信息。周期定位调用一次之后，会按照一定的时间频率返回定位结果，时间频率可以在数位云开发平台上设置，默认是5分钟返回一次定位结果。

startCycleLocate({params}, callback(ret, err))

## params

ext：

- 类型：字符串
- 描述：（可选项）扩展字段，有些应用场景或者业务下，集成方需要在场景识别的时候加上自己的标识或者数据，因此我们开放了一个扩展参数字段。扩展参数可以根据客户的要求来配置是否在对应的回调接口中返回。如果配置为返回，则会原样返回；如果配置为不返回，则不会返回该字段。
- **特别注意：扩展参数请尽量勿传入过长的字符串，以减少无谓的的带宽流量消耗，目前暂时没有对此扩展参数长度进行限制，但是不保证后期前/后端不会进行的截取操作，如有任何问题，请务必和我们进行沟通协商。**

passThrough：

- 类型：字符串
- 描述：（可选项）透传字段。透传字段和扩展字段的使用类似，不过透传字段是不需要通过后台配置是否返回的，而是一定会在对应的回调接口中原样返回的。主要用来区分不同触发请求的。
- **特别注意：透传参数请尽量勿传入过长的字符串，以减少无谓的的带宽流量消耗，目前暂时没有对此透传参数长度进行限制，但是不保证后期前/后端不会进行截取操作，如有任何问题，请务必和我们进行沟通协商。**


## callback(ret, err)

ret：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 0,      		 			//数字类型；错误码
    msg: 'OK',      				//字符串类型；错误信息说明
    timestamp: 1586585334775, 		//数字类型；时间戳
    poiId: '36d53dc0c473d38b', 		//字符串类型；POI的 id
    name: 'zara',					//字符串类型；POI名称
	floorName: '1',					//字符串类型；楼层
	buliding: '海岸城购物中心',		//字符串类型；楼宇
	area: '海岸城购物中心',			//字符串类型；商圈
	address: '文心四路14',		      //字符串类型；详细地址
	street: '文心四路14',			  //字符串类型；街道
	region: '南山区',				  //字符串类型；行政区
	city: '深圳市',				  //字符串类型；城市
	brand: 'zara',					//字符串类型；品牌
	areaCategory: '购物中心;商场',    //字符串类型；商圈分类
	areaAttribute: "档次:高档',      //字符串类型；商圈属性
	poiCategory: '购物;服装",        //字符串类型；POI分类
	poiAttribute: '风格:潮流时尚|品牌来源:欧美',    //字符串类型；POI属性
	coordinateSystem: 'GCJ02',     //字符串类型；经纬度坐标系类型：BD09-百度坐标系  GCJ02-火星坐标系  WGS84-地球坐标系
	longitude: 113.936074,		   //数字类型；经度
	latitude: 22.51703,            //数字类型；纬度
    ext: 'ext22',				   //字符串类型；定位时上传的扩展字段
	passThrough: 'pt22',		   //字符串类型；透传字段
    collectExt: '{\"name\": \"zhangsan\", 	\"age\": 18 }',     //字符串类型；采集时上传的透传字段
    requestSign: '439d8190a0ac977306ae436859bd4a9b25a64ada'		//字符串类型；请求签名
}
```
err：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 201001,      		 		//数字类型；错误码
    msg: '定位无结果'      			 //字符串类型；错误信息说明
}
```

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.startCycleLocate({
    ext: 'ext22',
    passThrough: 'pt22'
}, function(ret, err) {
    if (ret) {
        alert(JSON.stringify(ret))
    } else {
        alert(JSON.stringify(err));
    }
});
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a6"></div>

# **stopCycleLocate**

停止周期定位，不再按照设置的时间频率返回定位结果。

stopCycleLocate()

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.stopCycleLocate();
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a7"></div>

# **submitCollectData**

采集位置数据，并会将对应的位置信息和信号数据上传到数位云服务器。

submitCollectData({params}, callback(ret, err))

## params

name：

- 类型：字符串
- 描述：（必选项）采集点的名称或者店铺的名称

floorName：

- 类型：字符串
- 描述：（可选项）采集点的所在的楼层名称

building：

- 类型：字符串
- 描述：（可选项）采集点的所在的楼宇名称

area：

- 类型：字符串
- 描述：（可选项）采集点的所在的商圈名称

address：

- 类型：字符串
- 描述：（可选项）采集点的所在的详细地址

street：

- 类型：字符串
- 描述：（可选项）采集点的所在的街道

region：

- 类型：字符串
- 描述：（可选项）采集点的所在的行政区

area：

- 类型：字符串
- 描述：（可选项）采集点的所在的商圈名称

city：

- 类型：字符串
- 描述：（可选项）采集点的所在的城市

province：

- 类型：字符串
- 描述：（可选项）采集点的所在的省份

collectExt：

- 类型：字符串
- 描述：（可选项）扩展字段，有些应用场景或者业务下，集成方需要在采集位置点的时候加上自己的标识或者数据，因此我们开放了一个扩展参数字段。该扩展字段必须是json格式的字符串。

passThrough：

- 类型：字符串
- 描述：（可选项）透传字段。透传字段和扩展字段的使用类似，不过透传字段是不需要通过后台配置是否返回的，而是一定会在对应的回调接口中原样返回的。主要用来区分不同触发请求的。

poiId：

- 类型：字符串
- 描述：（可选项）采集点的 id。主要用于“单店多点”采集功能，即在同一个店铺的多个位置进行采集，如：采集同一个店铺的门口、收银台、试衣间、卫生间等多个点位。一个店铺对应一个 poiId，一个采集点对应一个 collectPoint，poiId和collectPoint是一对多的关系。

**说明：每个店铺第一次采集时不用传入 poiId，当第一个采集点采集成功后，采集回调结果中会返回一个新生成的 poiId 字段的值，以后采集的点，如果想关联到同一个 poiId 下，只需要在采集的时候带上 poiId 的值即可。如果第二次上传了 poiId ，同时也更改了 name 字段的值，则定位的结果以更改后的为准。**

collectorId：

- 类型：字符串
- 描述：（可选项）采集者的 id。通过设置这个字段，可以指明该条采集数据是由哪个采集者上传的，之后在查询或删除对应的采集信息时，也需要带上同样的采集者id字段。如果没有传递这个字段，则默认使用设备的唯一标识作为采集者id字段。建议开发者传递用户id或其他能唯一识别用户的字符串到这个字段以便进行用户账号对于采集数据的权限控制。

**特别注意：请根据商务合同中确定的需要采集的数据进行传递。省、市、区、街道、楼宇、楼层不得超过64个字符长度，商圈、name字段不得超过128个字符长度，自定义扩展字段必须是json格式的字符串且不得超过255个字符长度，自定义透传字段可以是任意字符串且不得超过255个字符长度，采集者id字段是任意字符串且不得超过255个字符长度。**


## callback(ret, err)

ret：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 0,      		 			//数字类型；错误码
    msg: 'OK',      				//字符串类型；错误信息说明
    timestamp: 1586585334775, 		//数字类型；时间戳
    poiId: 'zw7ex2vpj1vj43oq',		//字符串类型；POI的 id
    passThrough: 'pt33',			//字符串类型；透传字段
    requestSign: '439d8190a0ac977306ae436859bd4a9b25a64ada',	//字符串类型；请求签名
    collectPoints: [				//数组类型
        timestamp: 1586585334775,	//数字类型；时间戳
        cpCode: 'w9m6ldojrq38450x'	//字符串类型；采集点的 id
        longitude: 113.936074,		//数字类型；经度
	    latitude: 22.51703          //数字类型；纬度
    ]
}
```
err：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 206002,      		 		//数字类型；错误码
    msg: 'poi字段太长'      	 	 //字符串类型；错误信息说明
}
```

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.submitCollectData({
    name: '数位科技',				  //字符串类型；POI的名称
    floorName: '29',			    //字符串类型；楼层
    building: '彩讯科创中心',         //字符串类型；楼宇
    area: '海岸城购物中心',			//字符串类型；商圈
	address: '文心四路14',		      //字符串类型；详细地址
	street: '文心四路14',			  //字符串类型；街道
	region: '南山区',				  //字符串类型；行政区
	city: '深圳市',				  //字符串类型；城市
    provice: '广东省',				  //字符串类型；省份
    collectExt: '{\"name\": \"zhangsan\", 	\"age\": 18 }',		//Json字符串类型；采集扩展字段
    passThrough: 'pt33',			//字符串类型；透传字段
    poiId: '36d53dc0c473d38b',		//字符串类型；POI的 id
    collectorId: '2ien3ddidj3jd33'	//字符串类型；采集者的 id
}, function(ret, err) {
    if (ret) {
        alert(JSON.stringify(ret))
    } else {
        alert(JSON.stringify(err));
    }
});
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a8"></div>

# **queryCollectData**

采集位置数据，并会将对应的位置信息和信号数据上传到数位云服务器。

queryCollectData({params}, callback(ret, err))

## params

pageOffset：

- 类型：数字类型
- 描述：（可选项）当前页，即查询第几页的数据
- 默认值：1

pageSize：

- 类型：数字类型
- 描述：（可选项）每一页的数量
- 默认值：10

passThrough：

- 类型：字符串
- 描述：（可选项）透传字段。透传字段和扩展字段的使用类似，不过透传字段是不需要通过后台配置是否返回的，而是一定会在对应的回调接口中原样返回的。主要用来区分不同触发请求的。

collectorId：

- 类型：字符串
- 描述：（可选项）采集者的 id。通过设置这个字段，可以指明该条采集数据是由哪个采集者上传的，之后在查询或删除对应的采集信息时，也需要带上同样的采集者id字段。如果没有传递这个字段，则默认使用设备的唯一标识作为采集者id字段。建议开发者传递用户id或其他能唯一识别用户的字符串到这个字段以便进行用户账号对于采集数据的权限控制。


## callback(ret, err)

ret：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 0,      		 			 //数字类型；错误码
    msg: 'OK',      				 //字符串类型；错误信息说明
    timestamp: 1586585334775, 		 //数字类型；时间戳
    recordsCount: 1,				 //数字类型；总的采集数
    passThrough: 'pt55',				
    requestSign: '43d97df3049ba0440a35da31091de154e43fecba',	//字符串类型；请求签名
    poiPoints: [					 //数组类型；POI点的集合
        timestamp: 1586585334775,	 //数字类型；时间戳
        poiId: 'zw7ex2vpj1vj43oq'	 //字符串类型；POI的 id
        name: '数位科技',			   //字符串类型；POI的名称
        floorName: '29',			 //字符串类型；楼层
        building: '彩讯科创中心',      //字符串类型；楼宇
        area: '海岸城购物中心',		//字符串类型；商圈
        address: '文心四路14',		  //字符串类型；详细地址
        street: '文心四路14',		  //字符串类型；街道
        region: '南山区',			   //字符串类型；行政区
        city: '深圳市',			   //字符串类型；城市
        provice: '广东省',			   //字符串类型；省份
        collectExt: '{\"name\": \"zhangsan\", 	\"age\": 18 }',		//Json字符串类型；采集扩展字段
        collectPoints: [				//数组类型；采集点的集合
            timestamp: 1586585334775, 	//数字类型；时间戳
            cpCode: '0kv6vynzvj1x6mrg', //字符串类型；采集点的 id	
            longitude: 113.936074,		//数字类型；经度
            latitude: 22.51703          //数字类型；纬度
    	]
    ]
}
```
err：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 206010,      		 		//数字类型；错误码
    msg: '当前页应该大于0'      		//字符串类型；错误信息说明
}
```

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.queryCollectData({
    pageOffset: 1,					//数字类型；当前页
    pageSize: 10,					//数字类型；每一页的数量
    passThrough: 'pt55',			//字符串类型；透传字段
    collectorId: '2ien3ddidj3jd33'	//字符串类型；采集者的 id
}, function(ret, err) {
    if (ret) {
        alert(JSON.stringify(ret))
    } else {
        alert(JSON.stringify(err));
    }
});
```

## 可用性

Android系统

可提供的1.0.0及更高版本

<div id="a9"></div>

# **deleteCollectData**

采集位置数据，并会将对应的位置信息和信号数据上传到数位云服务器。

deleteCollectData({params}, callback(ret, err))

## params

poiIds：

- 类型：字符串数组类型
- 描述：（可选项）需要删除的兴趣点集合，对应采集查询结果中的 poiPoints 的 poiId

cpCodes：

- 类型：字符串数组类型
- 描述：（可选项）需要删除的采集点集合，对应采集查询中的 collectPoints 的 cpCode

passThrough：

- 类型：字符串
- 描述：（可选项）透传字段。透传字段和扩展字段的使用类似，不过透传字段是不需要通过后台配置是否返回的，而是一定会在对应的回调接口中原样返回的。主要用来区分不同触发请求的。

collectorId：

- 类型：字符串
- 描述：（可选项）采集者的 id。通过设置这个字段，可以指明该条采集数据是由哪个采集者上传的，之后在查询或删除对应的采集信息时，也需要带上同样的采集者id字段。如果没有传递这个字段，则默认使用设备的唯一标识作为采集者id字段。建议开发者传递用户id或其他能唯一识别用户的字符串到这个字段以便进行用户账号对于采集数据的权限控制。


## callback(ret, err)

ret：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 0,      		 			//数字类型；错误码
    msg: 'OK',      				//字符串类型；错误信息说明
    timestamp: 1586585334775, 		//数字类型；时间戳
    passThrough: 'pt66',			//字符串类型；透传字段
    requestSign: '43d97df3049ba0440a35da31091de154e43fecba'  //字符串类型；请求签名
}
```
err：

- 类型：JSON 对象
- 内部字段：

```js
{
    code: 206009,      		 		//数字类型；错误码
    msg: 'POI不存在或已经删除'        //字符串类型；错误信息说明
}
```

## 示例代码

```js
var swLocate = api.require("swLocate");
swLocate.deleteCollectData({
    poiIds: [								//字符串数组类型；poiId的集合
        "mpned2k5yqq062vq",					//字符串类型；POI的 id
        'zo160732gp3r4pjn'
    ],
    cpCodes: [								//字符串数组类型；cpCode的集合
        "w9m6ldojrq38450x",					//字符串类型；采集点的 id
        'w9m6ldojrq83750x'
    ],
    passThrough: 'pt66',					//字符串类型；透传字段
    collectorId: '2ien3ddidj3jd33'			//字符串类型；采集者的 id
}, function(ret, err) {
    endTime = (new Date()).getTime();
    if (ret) {
        alert(JSON.stringify(ret))
    } else {
        alert(JSON.stringify(err));
    }
    alert("采集查询耗时：" + (endTime - startTime) + "ms");
});
```

## 可用性

Android系统

可提供的1.0.0及更高版本



# **Android Q的适配**

## 设备唯一标示IMEI的获取

在Android Q上将无法获取设备的IMEI 信息，我们已经对此获取容错处理，在Android Q手机上不会报错。

## MAC地址的获取

在Android Q上获取到的MAC地址将是随机的，导致无法通过MAC追踪用户，目前对这个接口做了容错处理，不会报错，对于获取到的MAC是随机的，暂时没有处理。

## 定位权限的变化

在Android Q上，如果应用想要在后台获取定位权限，需要申请ACCESS_BACKGROUND_LOCATION 权限。对应APP应用，如果应用的 targetSdkVersion=Q，必须动态申请 ACCESS_BACKGROUND_LOCATION 权限；如果应用的 targetSdkVersion<Q，并且申请了 ACCESS_FINE_LOCATION 和 ACCESS_COARSE_LOCATION 权限，系统会自动授予 ACCESS_BACKGROUND_LOCATION 权限。对于希望在后台获取定位功能的客户，请适配 ACCESS_BACKGROUND_LOCATION 的权限判断。

## 无线信号的获取

在Android Q上想要扫描WiFi、蓝牙及使用 WLAN API 都必须拥有 ACCESS_FINE_LOCATION 权限才行。所以请客户的应用在针对Android Q做适配的时候，务必申请到 ACCESS_FINE_LOCATION 权限，否则会影响定位功能的结果。

##  外部存储设备权限的变化

每个应用将只能访问自己应用创建的文件，且替换并取消了原来的 READ_EXTERNAL_STORAGE 和 WRITE_EXTERNAL_STORAGE 权限，改而使用 READ_MEDIA_IMAGES, READ_MEDIA_VIDEO, READ_MEDIA_AUDIO 等权限，由于我们SDK中并不需要读取外部存储设备的权限，所以对于我们 SDK 的影响不大。

## 非Android SDK接口限制

Android Q对于通过反射获取系统中隐藏接口功能的API做了限制，目前我们SDK中只会通过反射获取应用的Activity任务栈中Activity的个数，这个API接口目前在Android Q限制的灰名单中，暂时没有影响。

## WiFi信号扫描限制

从Android P开始，系统对于WIFI信号的扫描做了限制，前台应用 2 分钟只能获取4次WiFi，所有后台应用30分钟内一共只能获取到1次WiFi信号，所以请客户控制好主动定位的频率。

**特别注意：**

**我们SDK已经针对Android Q做了适配，关于SDK中需要使用到的权限，需要客户的应用自己申请。**



# 采集和定位结果码说明

| retCode  | msg                                               | remark                            |
| -------- | ------------------------------------------------- | --------------------------------- |
| 0        | OK                                                | 成功                              |
| 100001   | System error                                      | 服务器错误                        |
| 100002   | Environment error                                 | 环境错误                          |
| 100003   | Miss required parameter                           | 缺少必传参数                      |
| 100004   | Parameter value invalid                           | 参数格式错误                      |
| 100005   | Sign error                                        | 签名错误                          |
| 100006   | Package name error                                | 包名错误                          |
| 100007   | Request data is null                              | 请求数据为空                      |
| 100008   | Lack authorization                                | 限制访问。缺少头部授权信息        |
| 100009   | Restricted access.App TPS reach limit             | 限制访问。应用每秒事务数达到上限  |
| 100010   | Restricted access.Transaction reach daily limit   | 限制访问。达到每天事务数配额上限  |
| 100011   | Restricted access.TPS reach limit                 | 限制访问。Server tps 达到上限     |
| 10002001 | Illegal oid.Fake oid                              | 疑似虚假设备号                    |
| 10002002 | Illegal oid.Oid request frequency is too high     | 高频请求                          |
| 201001   | No result                                         | 定位失败                          |
| 203004   | AppId invalid                                     | AppId无效                         |
| 203005   | App is disabled                                   | App不可用                         |
| 203006   | App type error                                    | App类型错误                       |
| 206001   | Lack of required POI fields                       | 缺少必须的poi字段                 |
| 206002   | POI field too long                                | poi字段太长                       |
| 206003   | Containing sensitive words                        | 包含敏感词                        |
| 206004   | Collect is turned off                             | UGC采集未开启                     |
| 206005   | Collect reach daily limit                         | UGC采集每日配额达到上限           |
| 206006   | PoiCode value invalid                             | poiCode无效                       |
| 206007   | PoiCode value invalid                             | poiCode无效                       |
| 206008   | PoiCode and collector do not match                | poiCode和collectId不匹配          |
| 206009   | Data does not exist or has been deleted           | POI不存在或已经删除               |
| 206010   | The value of CP should be greater than 0          | 当前页应该大于0                   |
| 206011   | The value of PS should be greater than 0          | 每页页大小应该大于0               |
| 206012   | The value of province containing sensitive words  | 省包含敏感词                      |
| 206013   | The value of city containing sensitive words      | 城市包含敏感词                    |
| 206014   | The value of region containing sensitive words    | 区域包含敏感词                    |
| 206015   | The value of street containing sensitive words    | 街道包含敏感词                    |
| 206016   | The value of address containing sensitive words   | 地址包含敏感词                    |
| 206017   | The value of area containing sensitive words      | 商圈包含敏感词                    |
| 206018   | The value of building containing sensitive words  | 楼宇包含敏感词                    |
| 206019   | The value of floorName containing sensitive words | 楼层名称包含敏感词                |
| 206020   | The value of poi name containing sensitive words  | POI名称包含敏感词                 |
| 206021   | The value of ext containing sensitive words       | 扩展字段包含敏感词                |
| 206023   | The value of ext must be Json                     | 扩展字段必须是Json格式的          |
| 300001   | Prepare launch resources error                    | SDK准备请求资源失败               |
| 300002   | The location response parse error                 | SDK解析定位结果数据失败           |
| 300004   | No http message                                   | SDK检测定位结果中没有msg信息      |
| 300005   | No available network                              | SDK检测没有可用的网络             |
| 300006   | Data verify failed                                | SDK检测定位请求数据不符合要求     |
| 300007   | Inner error                                       | SDK内部执行流程出现异常           |
| 300008   | Http header is error                              | SDK检测Http请求头错误             |
| 300009   | Sign error                                        | SDK检测签名错误                   |
| 300010   | Http request timeout                              | SDK请求定位超时                   |
| 300011   | Http other error                                  | SDK 请求http 出错                 |
| 300015   | the collect response parse error                  | SDK检测采集请求数据不符合要求     |
| 300016   | the query request json error                      | SDK检测采集查询请求数据不符合要求 |
| 300017   | the query request other error                     | SDK检测采集查询请求数据其他错误   |
| 300018   | the query response parse error                    | SDK检测采集查询响应数据解析失败   |
| 300019   | the delete request json error                     | SDK检测采集删除请求数据不符合要求 |
| 300020   | the delete request other error                    | SDK检测采集删除请求数据其他错误   |
| 300021   | the delete response parse error                   | SDK检测采集删除响应数据解析失败   |













