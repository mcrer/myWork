### 业务架构

![image text](https://www.plantuml.com/plantuml/png/XPJFJjj04CRFsQVO5oYNtYg4g6Wb3g22o0rnCFQIM79iQgzfeoeIAL082NyG1LGeeRH1eH8A82eAfIqFqwnZtgApNfbCm8X38dlcjz_EVBkUNezhBlTp5h7O1AVSeQwPoN9gc2xJkUdOX13TSdo3Xes_eZPFDOyQRChK6IKJhfPZr7GuVKT8Ihsr_go9kJEnj18s6fAb6OspDre-GbAwavjqR6PpckuRwdZlJwSx0uF3wNH782YlYzEDjZ9xovbjU21RkDqz-78XhcU2pqjYSLyokkCojVDeg_GzEDmNrN9O_nPKLyTaccTDEpFe6Bx59DByLMvUdOlQR6lrGvHdKx1nlsOR5dIRedGqs3W9bXf1fTYyd1ELpGYQPAvdUb2B7ec4zLcny4dytWZMbwEy2-elxOnfCrnfw-DXiF1JN9w9bPEumYWKBDV5mZb4nw9OVGzYodgKGKBDYrAmUQbQ1Y7iMDYeXSV5czAFO1TvykgvpxF0nSti7OJ7NwDAdtBZXSQrOP-v-H7coaD6tFBXpNHHoM1woFUoCOpAKel4wNk96i2f9kEQjq558L7DAKcCQnwZkRptsf9hndqFBFEyGHa0fD1Uhkp0or7FADUwceNpRDzOMMvVJ7RicfN_RR1VlYQNfO2ug9Jy9OZJhguUhFKubj3cpMxrdb5nQdexR1m7MmVYUal5aGKucMm4593pRPFd1vmCDkJXXli6uW8HmopDuwO-mZHNpnALIzR7pMArnrTqRY9N5Desj7RMuAF0DYGSQSu1NSTLvw3MmqyQhRQctNARU4mzWykv6EvN_aqNcnTbU21nflj93nuO2IJE0et71z3XoGnyuK2BdIey4vm568qHW6z7Z3h0Ep2QAP8jrM5tF7S85WCSVnHO_0w7RjnzbE299U67o4ummJJfLL2PRrxL24aOEGa5juwEr9Z3SJMui3FolYIC9Io0PnaX1TBBRCFFMVy1)

```plantuml

@startuml

left to right direction


cloud 设备 as device 
frame iot {

	frame 网关协议 as  gateway {

		component TCP
		component HTTP
		component MQTT
		component 拓展协议 as extends

	}

	frame 核心服务 as core {

		[物模型解析] as thingModule
		[事件处理] as eventHandle 
	  [数据持久化] as persistence
		[规则引擎] as ruleEngine
	}

	frame 用户平台 {

		[用户控制台]
		[租户设备控制台]
	}

	frame 三方服务 {
		component 认证鉴权服务 as OAuth
		[数据查询] as 3tdDataQueryService
		[数据推送服务] as 3tdDataPushService
	}

	frame 数据层 as data {

		frame 业务数据 {
			database mysql as businessMysql
		}

		frame 设备数据 {
			component 设备数据组件 as deviceDataComponent
			database hbase as deviceHbase
			database mysql as deviceMysql
			
			deviceDataComponent ..> deviceHbase
			deviceDataComponent ..> deviceMysql
		}

		frame 非业务数据 {
			frame 通讯日志 {
				component 通讯日志数据组件 as deviceCommunityLogComponent
				database mysql as deviceCLMysql
				database elasticSearch

				deviceCommunityLogComponent ..> deviceCLMysql
				deviceCommunityLogComponent ..> elasticSearch
			}
			frame 监控日志 {
				database hbase as monitorHbase
			}
		}
	}

device <..> gateway : 上行、下行
gateway <-> thingModule : 上行、下行
thingModule <-> eventHandle : 调用、回调
eventHandle -> persistence : 持久化数据
eventHandle -> ruleEngine : 执行规则引擎
persistence -> 设备数据 : 持久化
persistence -> 非业务数据 : 持久化

用户平台 -> 业务数据 : 持久化
用户平台 -> thingModule : 下行指令

3tdDataQueryService -> 设备数据 : 第三方设备数据查询
eventHandle ..> 3tdDataPushService : 第三方数据推送

}


@enduml

```
================================================================
