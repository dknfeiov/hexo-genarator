# 前端数据预览API v1.0.0


### 1.1 通信协议

> WEBSOCKET 协议


### 1.2 请求报文结构

> 请求参数
参数名称		 |类型	    |出现要求	 |描述  
:----			|:---		|:------	|:---	
cmd				|string		|required			|请求指令 （load、view）
msg				|object		|required			|指令执行所需参数，详见下表

> msg 参数详细
参数名称		|类型	    |出现要求	        |描述  
:----			|:---		|:------	        |:---	
dataSetId	    |string		|load 指令			|数据集ID
key				|string		|view 指令			|视图条件：  过滤关键字, 匹配所有字段
sortColumn	    |string		|view 指令			|视图条件：  排序字段名
sortType	    |string		|view 指令			|视图条件：  排序类型 ,（asc 升序 、 desc 降序 ），默认不排序
start	        |string		|view 指令			|视图条件：  起始行序号 => 从1开始
end	            |string		|view 指令			|视图条件：  结束行序号

> 排序规则

    1.  数值型数据默认根据数值，
    2.  字符串、日期格式等默认根据Unicode编码排序

> 请求报文示例

```javascript

    // load 指定（切换）数据集
    {   
        "cmd" : "load",
        "msg" : {
            dataSetId: 10001,    
        }
    }


    // view 视图条件 request
    {
        "cmd" : "view",
        "msg" : {
            key: '湖北',    
            sortName: 'column1',
            sortType: 'asc', 
            start: 1,
            end: 20
        }
    }

```



### 1.3 响应报文结构

> 响应参数参数
参数名称		 |类型	     |出现要求	  |描述  
:----			|:---		|:------	|:---	
cmd				|string		|required			|响应指令    viewState
msg				|object		|required			|指令执行所需参数，详见下表

> msg 参数
参数名称		        |类型	     |出现要求	              |描述  
:----			        |:---		|:------	            |:---	
list	                |string		|viewState 指令			|满足视图条件 的数据列表（并根据start、end限定）
viewTotal				|string		|viewState 指令			|满足视图条件 数据总数（全量）
columnsDistribute	    |string		|viewState 指令			|视图数据 字段值区间分布

```javascript


    // viewState 根据视图条件返回响应数据 response
    {
        "cmd" : "viewState",
        "msg" : {
            list:[
                [1,2],
                [4,5]
            ],
            viewTotal: 203,
            columnsDistribute: [
                {
                    name:'column1',
                    data: {
                        values: [1, 3, 6],
                        numbers: [120, 450, 780]    // single count
                    },
                },
                {
                    name:'column2',
                    data: {
                        values: ['1~20', '20~40', '40~60'],
                        numbers: [78, 342, 1110]    // range count
                    },
                },
                {
                    name:'column3',
                    data: {
                        values: ['China', 'Canada', 'America'],
                        numbers: ['30%', '40%', '30%']  // string percent
                    }
                }
            ]
        }
    }


```