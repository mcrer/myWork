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
### 应用架构
#### 核心组件
● 通用服务组件
TODO
● 业务服务组件
TODO
● 通讯协议组件
TODO
#### 两种部署架构
● 微服务架构（16G内存）
● standalong架构（10G内存）
图 TODO
#### 微服务架构
![image](https://www.plantuml.com/plantuml/png/XPJVJZf75CUlV_iKa_HQWqgf6aC4rFQYBJPmRxOmu0ROfRjBZJ4csegY5PLQbMZLQaYqQLnDLQfIyM7A_jctw2mxCiDcG2wCS3xVSywSxyoPL5HULWkvB9U5AHMe4f25zBmAae8C4weWYHod2ceM0hsf6R-NzQqBuwnkdArnd98Hn3mlypcGGdyW04lSfp2VoAHbgI0c8r9MaYScx4R5QZxRfx_gBmV18FXiT2Zic_eSWPASX78FQDW_F1q8SptJcNzlOdBwYt1WoiTCHo2VtnVozKlNUCDao1U9108a7HFoZmoD-QTwfxFsJp4PYSoW3qd7X3vAP_nPnIGCZ0sDZ99qJ0YdMwRz4QKa14ki4gsdO-IewokZOpR4x1YJD_RHGwjH9p33S1R7zXp3cyRXCoOPdZCXxFaoXpxkzRHNZYtjLY_lMffcDjPGSR04KBs4bCjB8XHL48_CpdM-qO6lul4UaM--YyUf2Ag7gz4pUwz62uq_dlItdxlYl08IaWpfGhJ2_EtQAFrZx5pffRg-UqT41SMPWgX8sTxQgtEajRIcNjimjGjpRrr_lkzAmYTpWlX15dh6H1aD5THvIh9CJnmrtJbzLqF_xkitLJVKNIGQYnc76-Xd5DT_kcOXHhrahDxQrNEzTCIAMrVhzcN5U5obMsttrNu8vcj5BzwxCU2ztSw9qhmA5_X5W7bY5Ol7hc7MOn8Jf_p2AhOzR3bnYkXfOnWQx15uzyNp-jhMjFF0t0_soRhPh07M4-KL1vZxNfOoKEG-OKs6JsNutgesHoQ3A_O3xMBPVDLG70szVyQJ2XeqcHIhuRmivQ0w3mkAHsOSt1bbhRyuBVCfNkGxIYMpkC1dqLPDHqEpVKw2yMzdOgmmsH6kRQsNYjS7Qwzcdj-Z0F0iDdBuhuKVuPVXM6_0hhxP6zieHpJMXp8V_jABcomAT_h-5WO7WwmDEWvQpLFhyH1mpYDmACyEwn0SkVOkHxupSNpD2Kz-O0lSE-wGxVyO23dDm42GiKKSy6dBkjXsBmSv4XErj4kxMgG5gfGN4gvgmgdWzNSSRwR_LbRf5STrZSOHuZRR_VpYfDz6T8qQIN-EenUlSm3lY-zCnFExhfsPEtVS91IJXLpsVm00)

``` plantuml
@startuml

left to right direction

title 微服务架构

skinparam frame  {
	BackgroundColor<<黑边青底>> #81B0D5
	BorderColor<<黑边青底>> #242E6B

	BackgroundColor<<黑边粉底>> #E7B6D0
	BorderColor<<黑边粉底>> #040A0B

	BackgroundColor<<黑边绿底>> #A0CC66
	BorderColor<<黑边绿底>> #43194D

	BackgroundColor<<黑边蓝底>> #CCFFFF
	BorderColor<<黑边蓝底>> #43194D

	BackgroundColor<<黑边橘底>> #e69138
	BorderColor<<黑边橘底>> #43194D
}

skinparam node {

	BackgroundColor<<业务服务>> #CCFFFF
	BorderColor<<业务服务>> #43194D

	BackgroundColor<<中间件服务>> #81B0D5
	BorderColor<<中间件服务>> #242E6B

	BackgroundColor<<三方服务>> #E7B6D0
	BorderColor<<三方服务>> #040A0B
}



frame 通讯协议组件 { 

	component TCP_component
	component HTTP_component
	component MQTT_component

}

frame 业务服务组件 {
	component 核心服务组件 as core_component
	
	component 用户控制台组件 as userConsole_component
	
	component 租户设备管理平台组件 as adminConsole_component
	
	component 三方服务组件 as OAuth_component
}


frame 通用组件 {

	component 缓存组件
	
	component OSS文件存储组件
	
	component 消息队列组件
	
	component 视频流服务组件
}


node 网关服务 <<业务服务>> as gateway 

node 核心服务 <<业务服务>> as core

node 用户控制台 <<业务服务>> as userConsole

node 租户设备管理平台 <<业务服务>> as adminConsole

node 三方服务 <<业务服务>> as OAuth 

frame 中间件服务 {

	node emq集群 <<中间件服务>> as emq_broken
	
	node kafka集群 <<中间件服务>>
	
	node 用户服务 <<中间件服务>>
	
	node 配置中心 <<中间件服务>>
	
	node 监控平台 <<中间件服务>> as prometheus
	
	node 监控数据平台 <<中间件服务>> as grafana
	
	node skywalkingOAP服务 <<中间件服务>>

	node NFS <<中间件服务>>
}

frame 三方云服务 {

	node 萤石云 <<三方服务>>
	
	node LiveGBS <<三方服务>>
	
	node 阿里云OSS <<三方服务>>
	
	node 紫光云OSS <<三方服务>>
}

gateway ..> 通讯协议组件 : 依赖 
core ..> core_component : 依赖
userConsole ..> userConsole_component : 依赖
adminConsole ..> adminConsole_component : 依赖
OAuth ..> OAuth_component : 依赖

gateway --> 消息队列组件 : 上行数据
core ..> 消息队列组件 : 订阅上行数据topic
core <--> 三方云服务 : 视频、文件存储

userConsole --> gateway : 下行指令
adminConsole --> gateway : 下行指令

通用组件 ..> 中间件服务 : 依赖中间件服务实现

@enduml
```

#### standalong架构

![image](https://www.plantuml.com/plantuml/png/dPHTJnfB6CUlcKynoRdMe78uQemHKC-vE2qsS6-sCE26POVkBcqQGwAjBsX5fLObMhKQ4cqQLrELghJuOUg-xBVeu4xeCjd5f5vi8i_l_vzddfSPaHLEKl9P0GWefK05GubFJoamoKieeV1O143X5G51meb9Ji1YMj-lwVjp0CWPNinn4fU5AV9144u3NvXBPD8Ipel921QmD3HarSjcuyRQUw_TRWuFmx_wUyB-qRy9YAKaaZoWta3lM30CFEsChqkK7Fid71prCtOiv0_uG_vETlKxIeRyaKWmoDenKA2lPo0mwcrdRknHCX8P9t-i7GCzPgT_hb0I1GTw-lfPEmQYTWK8d1qISHB1QRT3xgzFhUtB-tfD_rZIbWyzUyHmdcsYy9A-TKD9hpQn46rJWPqipyHtY93AlOhDGAtg060xirCDMSaq96VRh3MpOwfdMcdDL5MZFaTA0KdK1to-1Cxci8X41SOZ4vEj_zeY_yNZNg4dp-9nPmZu2kJZ0FHFrzhTMpibUZ8dmmIMa5FcL1WVJlJYDttrM2lMjBNptwAytAoDA6F1MtjyGBIcsj2gYuPwQAmlQ3SNRIPSCikBZxZGxZ8vHqDvPSeX0H2sYajofc2ptyw4lczefnKxHW9jiMWifcyjaW0Xj3Sdxf1UA-gpPrRbG2jkknFcyO9rLDQlPfq9apxOUqId1J8xy93dmsQXx0jhTy5eLA7Red1o4vXyBk4C4e6FQZ9SAiDvgreSRQLzOYVGcYyPFrGI9xFIsN6tJ2R3RgfNmZa9Pv4oXV8o8zCtp_MIsbcSbhWK9t8jfPnv_OeJo5Adew693ZUX-DFnc6kuq6g4FLptjsMs4UPwrJYu8078lY-jPFxdNw9_mp5lmAhSMOihn2CQwq0Pbr-q-IMMSgRgybvqTGsx32CS91TQDWzNxDg2ziM6tTqU8bCziYhpJgc2Sto2rGyrJsKhDaX7_kVCh7D_071z81uIP-z34t_NF7s5M5G1VHV-J0pQdW1oQNRTceB6ddcrnVokgVl6wZaOGM8odnL-0G00)


```plantuml
@startuml

left to right direction

title standalong架构

skinparam frame  {
	BackgroundColor<<黑边青底>> #81B0D5
	BorderColor<<黑边青底>> #242E6B

	BackgroundColor<<黑边粉底>> #E7B6D0
	BorderColor<<黑边粉底>> #040A0B

	BackgroundColor<<黑边绿底>> #A0CC66
	BorderColor<<黑边绿底>> #43194D

	BackgroundColor<<黑边蓝底>> #CCFFFF
	BorderColor<<黑边蓝底>> #43194D

	BackgroundColor<<黑边橘底>> #e69138
	BorderColor<<黑边橘底>> #43194D
} 


skinparam node {

	BackgroundColor<<中间件服务>> #81B0D5
	BorderColor<<中间件服务>> #242E6B

	BackgroundColor<<三方服务>> #E7B6D0
	BorderColor<<三方服务>> #040A0B
}

skinparam frame {

	BackgroundColor<<业务服务>> #CCFFFF
	BorderColor<<业务服务>> #43194D
}

frame standalong架构 <<业务服务>>{ 

	frame 通讯协议组件 { 
	
		component TCP_component
		component HTTP_component
		component MQTT_component
	
	}
	
	component 核心服务组件 as core_component
	
	component 用户控制台组件 as userConsole_component
	
	component 租户设备管理平台组件 as adminConsole_component
	
	component 三方服务组件 as OAuth_component

  frame 通用组件 {

		component 缓存组件
		
		component OSS文件存储组件
		
		component 消息队列组件
		
		component 视频流服务组件
	}
} 




frame 中间件服务 {

	node emq集群 <<中间件服务>> as emq_broken
	
	node kafka集群 <<中间件服务>>
	
	node 用户服务 <<中间件服务>>
	
	node 配置中心 <<中间件服务>>
	
	node 监控平台 <<中间件服务>> as prometheus
	
	node 监控数据平台 <<中间件服务>> as grafana
	
	node skywalkingOAP服务 <<中间件服务>>

	node NFS <<中间件服务>>
}


frame 三方云服务 {

	node 萤石云 <<三方服务>>
	
	node LiveGBS <<三方服务>>
	
	node 阿里云OSS <<三方服务>>
	
	node 紫光云OSS <<三方服务>>
}


通讯协议组件 --> 消息队列组件 : 上行数据
core_component ..> 消息队列组件 : 订阅上行数据topic
core_component <--> 三方云服务 : 视频、文件存储

userConsole_component --> 通讯协议组件 : 下行指令
adminConsole_component --> 通讯协议组件 : 下行指令

通用组件 ..> 中间件服务 : 依赖中间件服务实现

@enduml

```
