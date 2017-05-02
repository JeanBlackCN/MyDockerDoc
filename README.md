## k8s环境说明文档


*  20161226
		1.增加ocauth的时间戳校验关闭环境变量


*	20161208

		1.需要部署的容器－>网关->增加statisticalanalysis



### 1.主机环境

*	主机

		10.1.74.35
		10.1.74.36
		10.1.74.37
		
		//10.1.65.50
		
*	用户密码

		root ym!@2016
		
### 2.安装软件


### 3.需要部署的容器

*	前端业务

		1.huidao_node_file_server		//文件服务器:node
		
		2.huidao_server					//后端服务工程:java
		
		3.huidao_manage					//管理端工程:node
		    
		        ocauth  
                    ENV FIND_HTTP_AUTH_PROVIDER_SERVER_IP http://10.1.64.83:8500/v1/catalog/service/ocauth-3000  
                    ENV FIND_RPC_AUTH_PROVIDER_SERVER_IP http://10.1.64.83:8500/v1/catalog/service/ocauth-3001  
                    ENV FIND_HUIDAO_MANAGE_HOST_PROVIDER_SERVER_IP http://10.1.64.83:8500/v1/catalog/service/huidao_manage  
                    ENV FIND_HUIDAO_SERVER_HOST_PROVIDER_SERVER_IP http://10.1.64.83:8500/v1/catalog/service/huidao_server

                nsq  
                    ENV NSQL_REQUEST_URL 10.1.65.66  
                    ENV NSQL_REQUEST_PORT 4150  
                    ENV NSQL_MESSAGE_URL 10.1.65.66:4161      //nsq订阅通知的地址  
                    ENV NSQL_TOPIC_APP_CHANGED appChanged     // 应用发生变化的nsq topic 

                mongo  
                    ENV MONGODB_SESSION_URL mongodb://10.1.65.66:32770/huidao       //用来存session的mongodb地址  
                    ENV MONGODB_SERVICE_URL mongodb://10.1.65.66:32770/octopus     //用来查调用情况的mongodb地址 

                file_server
                    ENV UPLOAD_URL http://10.1.64.83:8001/upload      //上传文件地址
                    ENV FILE_URL http://10.1.64.83:8001/resources/   //获取文件服务器资源地址

                other
                    ENV APIGATEWAY_URL http//10.1.64.83:90                     //服务调试调用api网关地址  
                    ENV DOCKER_CONTAINER_HOST http://10.1.64.158:10024         //认证回调在docker模式时的反响代理地址  
                    ENV CUSTOM_PLATFORM_URL http://10.1.64.83:81/octopus/auth  //使用免登机制的 定制平台的地址登录地址 
		
		4.huidao_api_monitor			//api管理:node
	
*	网关

		1.ocauth					//鉴权中心:go
							
				mysql
					ENV DB_HOST 172.19.12.31
					ENV DB_PORT 5500
					ENV DB_NAME octopus
					ENV DB_USER octopusapp
					ENV DB_PW octoapp
				
				redis
					ENV REDIS_HOST 10.1.64.83
					ENV REDIS_PORT 49157
					
				mongo
					ENV MONGO_HOST 10.1.64.84
					ENV MONGO_PORT 32770
					ENV MONGO_DB db
					ENV MONGO_COLLECTION log
					
				关闭时间戳校验(FALSE为关闭)
				   ENV TIMESTAMP_CHECK FALSE
					
		
		2.apigateway				//api网关:go
		
				ocauth
					ENV CONFIG_CONSUL_HOSTS 10.1.64.83:5000,ip2:port2
					
				mysql
					ENV DB_HOST 172.19.12.31
					ENV DB_PORT 5500
					ENV DB_NAME octopus
					ENV DB_USER octopusapp
					ENV DB_PW octoapp
					
				redis
					ENV REDIS_HOST 172.18.11.71
					ENV REDIS_PORT 6385
					ENV REDIS_PASSWORD OctoPus
					
				mongo
					ENV MONGO_HOST 172.18.11.59
					ENV MONGO_PORT 30000
					ENV MONGO_USER huidao
					ENV MONGO_PW huidaopwd
					ENV MONGO_MECHANISM SCRAM-SHA-1
					ENV MONGO_SOURCE octopus
					
				htmlgateway
					ENV CONFIG_OS_CATALOGID_HTMLGATEWAY htmlgateway的consulid
					ENV HTML_GATEWAY_URL http://hg.huidao168.com(当此环境变量不存在时候，改为通过CONFIG_OS_CATALOGID_HTMLGATEWAY查询获得)
				
				NSQ
					ENV NSQ_LOOKUPD_ADD=10.1.65.66:4161
					ENV NSQD_ADD_TCP=10.1.65.66:4150
					ENV NSQD_TOPIC_APIGATEWAY=apigateway_log
				
		
		3.htmlgateway				//h5网关:go
		
				redis
					ENV REDIS_HOST 172.18.11.71
					ENV REDIS_PORT 6385
					ENV REDIS_PASSWORD OctoPus
					
				mongo
					ENV MONGO_HOST 172.18.11.59
					ENV MONGO_PORT 30000
					ENV MONGO_USER huidao
					ENV MONGO_PW huidaopwd
					ENV MONGO_MECHANISM SCRAM-SHA-1
					ENV MONGO_SOURCE octopus
					
		
		
		4.clientgateway			//汇到总网关:go
			
				ocauth,apigateway
					ENV CONFIG_CONSUL_HOSTS 10.1.64.83:5000,ip2:port2
					ENV CONFIG_OS_CATALOGID_HUIDAO_OCAUTH ocauth-3000
					ENV CONFIG_OS_CATALOGID_HUIDAO_OCAUTHRPC ocauth-3001
					ENV CONFIG_OS_CATALOGID_APIGATEWAY apigateway_test
					
		5.statisticalanalysis		//定时从mongo读数据到mysql
		
				mongo
					MONGO_URL   10.1.67.234:26017
					MONGO_NAME  octopus
					MONGO_COL   api

				mysql
					SQL_SOURCE  root:root@tcp(10.1.30.181:3306)/COUNT_SERVICE_BY_DAY?charset=utf8				
					
		
		6.			
					
			
		
*	支撑

		1.consul	//服务发现
		node1(leader):10.1.74.35:8500
		node2:10.1.74.36:8500
		node3:10.1.74.37:8500
		
		2.nginx	//负载均衡
		
		3.nsq		//业务转发
		nsqlookupd:10.1.74.35:4160(tcp)、10.1.74.35:4161(http)
		nsqd:10.1.74.35:4150(tcp)、10.1.74.35:4151(http)
		nsqadmin:10.1.74.35:4171(http)
		
	
*	数据库

		1.mysql
		10.1.74.35:50003
		
		2.redis
		10.1.74.35:50001
		
		3.mongo
		10.1.74.35:50000
		
		
		
