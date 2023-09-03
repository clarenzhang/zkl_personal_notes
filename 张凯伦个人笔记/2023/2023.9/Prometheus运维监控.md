**Prometheus运维监控**

[文章分享链接](https://www.yuque.com/xinglujianzhi/ops/xefgl5nb66gp911o?singleDoc)

梳理总结一下生产中使用到的prometheus相关知识。包括但不限于传统服务(主机、Nginx、Tomcat、Haproxy等)、云原生(Kubernetes)周边生态的监控等等。

部署使用中最直观的感受就是，prometheus只是一个时序数据库，像规则匹配、告警都是在后端机器上配置或借助于其他插件完成。没有Zabbix监控功能齐全，各种成熟的模板、告警媒介插件、规则配置、dashboard定义等。转折来了，随着云原生肉眼可见发展的速度及周边生态不断完善，prometheus监控使用非常广泛，趋势不可违。

 一、监控的理论 

简单概述，主要以使用使用为主

 1、监控的重要性 

 通过业务监控系统，全⾯掌握业务环境的运⾏状态，通过⽩盒监控能够提前预知业务瓶颈，通过⿊盒监 控能够第⼀时间发现业务故障并通过告警通告运维⼈员进⾏紧急恢复，从⽽将业务影响降到最低。  

 ⿊盒监控，关注的是时时的状态，⼀般都是正在发⽣的事件，⽐如nginx web界⾯打开的是界⾯报错503、 磁盘⽆法报错数据等，即⿊盒监控重点在于能对正在发⽣的故障进⾏通知告警。 ⽩盒监控，关注的是原因，也就是系统内部暴露的⼀些指标数据，⽐如nginx 后端服务器的响应时⻓、磁盘 的I/O负载值等。  

 监控系统需要能够有效的⽀持⽩盒监控和⿊盒监控，通过⽩盒能够了解其内部的实际运⾏状态，以及对 监控指标的观察能够预判可能出现的潜在问题，从⽽对潜在的不确定因素进⾏提前优化并避免问题的发 ⽣，⽽通过⿊盒监控，⽐如常⻅的如HTTP探针、TCP探针等，可以在系统或者服务在发⽣故障时能够快 速通知相关的⼈员进⾏处理，通过建⽴完善的监控体系，从⽽达到以下⽬的：  

 1.1、 ⻓期趋势分析 

 通过对监控样本数据的持续收集和统计，对监控指标进⾏⻓期趋势分析。例如，通过对磁盘 空间增⻓率的判断，我们可以提前预测在未来什么时间节点上需要对资源进⾏扩容。  

 1.2、 对照分析   

 两个版本的系统运⾏资源使⽤情况的差异如何？在不同容量情况下系统的并发和负载变化如何？ 通过监控能够⽅便的对系统进⾏跟踪和⽐较。  

 1.3、 告警通知 

 当系统出现或者即将出现故障时，监控系统需要迅速反应并通知管理员，从⽽能够对问题进⾏快速的 处理或者提前预防问题的发⽣，避免出现对业务的影响。  

 .1.4、  故障分析与定位 

 当问题发⽣后，需要对问题进⾏调查和处理。通过对不同监控监控以及历史数据的分析， 能够找到并解决根源问题。  

 .1.5、  故障分析与定位 

 通过可视化仪表盘能够直接获取系统的运⾏状态、资源使⽤情况、以及服务运⾏状态等直观的 信息。  

 2、 了解常⻅的监控⽅案 

 开源监控软件：zabbix、smokeping、open-falcon。等

很少能用到，不用了解cacti、nagios (14年前)

 2.1、Zabbix 

[www.zabbix.com](https://www.zabbix.com/cn/)

监控

页面/运维服务

ZABBIX对比PROMETHEUS

由用户-9C8CF创建于九月23,2020

ZABBIX

PROMETHEUS

后端用GOLANG开发,前端是GRAFANA,JSON编辑即可解决.定制化难度较低.

后端用C开发,界面用PHP开发,定制化难度很高.

支持更大的集群规模,速度也更快.

集群规模上限为10000个节点.

更适合监控物理机环境.

更适合云环境的监控,对 OPENSTACK,KUBERNETES 有更好的集成.

监控数据存储在关系型数据库内,如MYSQL,很难从现有数据中扩展维度.

监控数据存储在基于时间序列的数据库内,便于对已有数据进行新的聚合.

安装相对复杂,监控,告警和界面都分属于不同的组件.

安装简单,ZABBIX-SERVER 一个软件包中包括了所有的服务端功能.

图形化界面比较成熟,界面上基本上能完成全部的配置操作.

界面相对较弱,很多配置需要修改配置文件.

发展时间更长,对于很多监控场景,都有现成的解决方案.

2015年后开始快速发展,但发展时间较短,成熟度不及ZABBIX.

心踩

你赞了它

![image.png](https://cdn.nlark.com/yuque/0/2022/png/25866599/1667721560478-82c1f9bd-8266-4a62-bf3d-e7d8025dd57e.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_32%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 ⽬前使⽤较多的开源监控软件，可横向扩展、⾃定义监控项、⽀持多种监控⽅式、可监控⽹络与服务等。  

 2.2、 SmokePing 

[oss.oetiker.ch](https://oss.oetiker.ch/smokeping/)

 Smokeping是⼀款⽤于⽹络性能监测的开源监控软件，主要⽤于对IDC的⽹络状况，⽹络质量，稳定性等做 检测，通过rrdtool制图⽅式，图形化地展示⽹络的时延情况，进⽽能够清楚的判断出⽹络的即时通信情 况。  

 2.3、 Open-falcon   

[www.open-falcon.org](https://www.open-falcon.org/)

 二、Prometheus Server使用 

| 主机列表            | 本次分享中角色                  | 备注                             |
| ------------------- | ------------------------------- | -------------------------------- |
| 192.168.101.100     | 二进制Prometheus Server+Grafana | 收集exporter的数据和K8s数据      |
| 192.168.101.101     | 二进制部署各种exporter          | 完成指标采集                     |
| 192.168.101.200-206 | Kubernetes cluster              | 部署Prometheus Pod，收集集群信息 |

二进制所有服务均存放在目录中

/data/server/

例子

1、prometheus

/data/server/prometheus

2、alermanger

/data/server/alertmanger

3、counsul

/data/server/consul

 2、 prometheus 简介 

[prometheus官网](https://prometheus.io/docs/introduction/overview/)

 Prometheus是基于go语⾔开发的⼀套开源的监控、报警和时间序列数据库的组合，是由SoundCloud 公司开发的开源监控系统，Prometheus于2016年加⼊CNCF（Cloud Native Computing Foundation, 云原⽣计算基⾦会）,2018年8⽉9⽇prometheus成为CNCF继kubernetes 之后毕业的第⼆个项⽬， prometheus在容器和微服务领域中得到了⼴泛的应⽤，其特点主要如下：  

 使⽤key-value的多维度(多个⻆度，多个层⾯，多个⽅⾯)格式保存数据 数据不使⽤MySQL这样的传统数据库，⽽是使⽤时序数据库，⽬前是使⽤的TSDB ⽀持第三⽅dashboard实现更绚丽的图形界⾯，如grafana(Grafana 2.5.0版本及以上) 组件模块化 不需要依赖存储，数据可以本地保存也可以远程保存 平均每个采样点仅占3.5 bytes，且⼀个Prometheus server可以处理数百万级别的的metrics指标数据。 ⽀持服务⾃动化发现(基于consul等⽅式动态发现被监控的⽬标服务) 强⼤的数据查询语句功(PromQL,Prometheus Query Language) 数据可以直接进⾏算术运算 易于横向伸缩 众多官⽅和第三⽅的exporter实现不同的指标数据收集  



 3、官网架构图 

 prometheus server：主服务，接受外部http请求，收集、存储与查询数据等

 prometheus targets: 静态收集的⽬标服务数据 

service discovery：动态发现服务

 prometheus alerting：报警通知

 push gateway：数据收集代理服务器(类似于zabbix proxy)

 data visualization and export： 数据可视化与数据导出(访问客户端)  

SERVICE DISCOVERY

PAGERDUTY

SHORT-LIVED

JOBS

KUBERNETES

FILE SD

ALERTMANAGER

EMAIL

PUSH METRICS

AT EXIT

DISCOVER

NOTIFY

TARGETS

ETC

PUSHGATEWAY

PROMETHEUS SERVER

PUSH

ALERTS

HTTP

PULL

TSDB

RETRIEVAL

SERVER

METRICS

PROMETHEUS

WEBUI

GRAFANA

NODE

HDD/SSD

JOBS/

EXPORTERS

APL CLIENTS

![prometheus官网原图.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672670963672-e750d6d9-37a9-4724-bb96-b222b7c47d79.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_39%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 3、prometheus使用完成效果 

 3.1、集群联邦 

二进制高配机器部署prometheus server 与K8S中的sprometheus监控混着用

 3.2、使用二进制prometheus server 

一阶段监控传统服务，第二阶段监控云原生。

1、收集非K8s业务服务监控指标

2、配置告警规则

3、结合多种媒介进行告警通知

 4、prometheus 部署使用 

 可以通过不同的⽅式安装部署prometheus监控环境，支持二进制、docker、docker-compose、prometheus等多种方式部署，实际⽣产环境要根据实际需求选择其中⼀种⽅式部署即可，不过⽆论是使⽤哪⼀种⽅式安 装部署的prometheus server，使⽤方法都是⼀样的。本次分享主要以二进制方式为主，因为后面集群联邦的时候二进制prometheus-server会接收所有K8s集群prometheus-server的数据和各种exporter数据。

 4.1、二进制部署Prometheus server 

官方提供的服务和插件，满足不了需求可以去github上找第三方插件。

40

PROMETHEUS

SUPPDRT&TRAININGBLOG

DOWNLOAD

DOWNLOAD

WE PROVIDE PRECOMPILED BINARIES AND BOCKER IMAGES FOR MOST AFFICIALLY MAINTAINED

PROMETHEUS COMPONENTS.IF A COMPONENT IS NOT LISTED HECK THECK THE RESPECTIVE

REPOSITORY ON GITHUB FOR FURTHER INSTRUCTIONS.

THERE S ALSO A CONSTANTLY MAING NUMBER OF INDEPENDIENDIENTLY MAINTAINED EXPORTED OF

EXPARTERS AND INTEGRATIONS

PUSHGATEWAY

STATSD_EXPORTER

ARCHITECTURE  AMD64`

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672729288471-dc783c67-88cf-4e75-8650-ac45131b98fb.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_73%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.1.1、下载prometheus server 

[Prometheus最新版本](https://prometheus.io/download/)

[Github选择更多历史版本](https://github.com/prometheus/prometheus/tags)

[prometheus-2.36.2.linux-amd64.tar.gz](https://github.com/prometheus/prometheus/releases/download/v2.36.2/prometheus-2.36.2.linux-amd64.tar.gz)

V2.36.2

WZIP

)ON JUN 20,2022

DOWNLOADS

D7E7B8E

TAR.GZ

NOTES

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672729629066-0633b2cf-e443-4107-9962-17318f418ba4.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_20%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



2022年6月20号版本

不建议使用最新的prometheus，会出现grafana上模板没有数据，不兼容的新规则的问题

 4.1.2、解压安装包 



[ROOTEPROMETHEUS-SERVER SERVER]# TAR -XF PROMETHEUS-2.36.2.LINUX-AMD64.TAR.9Z -C

LN-SVPRO

[ROOT@PROMETHEUS-SERVER SERVER]#

SV PROMETHEUS-2.36.2.LINUX-AMD64 PROMETHEUS

'PROMETHEUS-2.36.2.LINUX-AMD64'

,PROMETHEUS

SERVER]# LS

[ROOT@PROMETHEUS-SERVER

PROMETHEUS-2.36.2.LINUX-AMD64 PROMETHEUS-2.36.2.LINUX-AMD64.TAR.GZ

PROMETHEUS

\#MKDIR-P /DATA/PROMETHEUS-PKGS

[ROOT@PROMETHEUS-SERVER

SERVER]#

/DATA/PROMETHEUS-PKGS

-2.36.2.LINUX-AMD64.TAR.GZ

SERVERL# MV PROMETH

[ROOT@PROMETHEUS-SERVER

SERVER]# LS

LROOT@PROMETHEUS-SERVER

PROMETHEUS-2.36.2.LINUX-AMD64

PROMETHEUS

[ROOT@PROMETHEUS-SERVER SERVER]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1691324573293-208131d8-c6d0-4d5a-8bfe-b957a3d96bd5.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_51%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.1.3、配置启动服务 

添加守护进程启动文件

cat /usr/lib/systemd/system/prometheus.service 



设置开机自启



[ROOT@PROMETHEUS-SERVER SERVER]# SYSTEMCTL STATUS PROMETHEUS

PROMETHEUS.SERVICE - PROMETHEUS SERVER DAEMON

LOADED: LOADED (/UST/LLB/SYSTEMD/SYSTEM/PROMETHEUS-SERVICE; DISABLED; VENDOR PRESET: DISABLED)

ACTIVE:INACTIVE (DEAD)

[ROOT@PROMETHEUS-SERVER SERVERL# SYSTEMCTL START PROMETHEUS

[ROOT@PROMETHEUS-SERVER SERVER]# PS-E"

EF|GREP PROMETHEUS

11714

ROOT

/SERVER/DATA --STORAGE.TSDB.RETENTION.TIME-120D --VEB.ENABLE-LIFECYCL

00:00:00GREP-COLORAUTO

11722  11682    23:48  PTS/1

ROOT

PROMETHEUS

[ROOT@PROMETHEUS-SERVER SERVER]# NETSTAT -NTLPLGRE

LGREP PROME

LISTEN

11714/PROMETHEUS

TCP6

0606:00

水;:

[ROOT@PROMETHEUS-SERVER SERVER]# SYSTEMCTL ENABLE PROMETHEUS.SERVICE

[ROOT@PROMETHEUS-SERVER SERVER]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1691324819125-224f2f42-c2a3-479c-90b6-7e1d64b68836.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_992%2Climit_0)





 4.1.5、了解prometheus配置文件 





ILROOTOFANHT-OPS-PROMETHEUS-SERVER PROMETHEUS]# CAT PROMETHEUS.YML

\# MY GLOBAL CONFIG

GLOBAL:

SCRAPE-INTERVAI: 155 # SET THE SCRAPE INTERVAL TO EVERY 15 S8CONDS. DOFAULT IS EVERY I MINUTERYUTE"

EVALUATION.INTERVAL: 15S # EVALUATE RULES EVERY 15 SECONDS. THE DEFAULT IS EVERY 1 MINUTE,

\# SCRAPE-TIMEOUT IS SET TO THE GLOBAL DEFAULT (10S).

\# ALERTMANAGER CONFIGURATION

ALERTING:

ALERTMANAGERS:

告警通知配置,目前还用不到

STATIC_CONFIGS:

TARGETS:

\#-ALERTMANAGER:9093

ID DERIODICALLY EVALUATE THEM ACCORDING TO THE GLOBAL 'EVALUATION-INTERVAL'.

LOAD RULES ONCE AND D

RULE_FILES

配置告警规则配置,目前还用不到

"FIRST RULES.YML"

井井

'SECOND_RULES.YML"

\# A SCRAPE CONFIGURATION CONTAINING EXACTLY ONE ENDPOINT TO SCRAPE:

\# HERE IT'S PROMETHEUS ITSELF.

SCRAPE_CONFIGS:

21 JOB三<JOB-NAME>' TO ANY TIMESERIES SCRAPED FROM THIS CONFIG.

THE JOB NAME IS ADDED AS A LAB

LABEL JOB-

JOB_NAME:'PROMETHEUS"

\# METRICS PATH DEFAULTS TO '/METRICS'

\#SCHEME DEFAULTS TO 'HTTP'.

启动后添加主机要用到的配置

STATIC_CONFIGS:

\- TARGETS:["LOCALHOST:9090"]

LROOT@FANHT-OPS-PROMETHEUS-SERVER PROMETHEUS]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672713270979-dac0b11a-abcc-4e05-bfbe-461b25f4f6ae.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_74%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_865%2Climit_0)



 4.1.6、访问prometheus前端页面 

http://192.168.101.200:9090/



192168.101200909090/GRAPHZGO,EXPR-&GO.TAB-18GOSTACKED-ORGOSHOW EXEMPLARS-ORGORANGE INPUT-INPUT-1H

不安全

0

PROMETHEUS

STATUSHELP

GRAPH

ALERTS

ENABLE HIGHLIGHTING

ENABLE AUTOCOMPLETE

USE LOCAL TIME

ENABLE QUERY HISTORY

ENABLE LINTER

EXECUTE

EXPRESSION (PRESS SHIFT+ENTER FOR NEWLINES)

TABLE

GRAPH

\>

EVALUATION TIME

NO DATA QUERIED YET

REMOVE PANEL

ADD PANEL

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672758989460-0c874b22-b5da-4ddb-8340-73be94b8cdff.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)





 4.2、了解Exporter相关知识 

广义上讲所有可以向Prometheus提供监控样本数据的程序都可以被称为一个Exporter。而Exporter的一个实例称为target，如下所示，Prometheus通过轮询的方式定期从这些target中获取样本数据:

EXPORTER

TARGET

5S

TARGET

PROMETHEUS-SERVER

TARGET

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672728077068-61e5a146-2d19-4585-933f-d150d92d94b5.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_38%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.2.1、使用Exporter原因 

云原生组件支持prometheus接口监控。etcd、Kubernetes、CoreDNS等，可以直接被prometheus监控。

大多数服务在Prometheus诞生前的很多年就已发布，考虑到安全性、稳定性及代码耦合等因素的影响，软件作者并不愿意将监控代码加入现有代码中。

在此背景之下，Exporter 应运而生，Prometheus 在面对众多繁杂的监控对象时并没有采用逐一适配的方式，而是制定了一套独特的监控数据规范，符合这套规范的监控数据都可以被Prometheus统一采集、分析和展现。说白了你想用prometheus收集数据要么改造软件支持prometheus数据格式，要不然开发第三方插件Exporter。

 4.2.2、Exporter获取监控数据的方式 

HTTPS方式 

HTTP方式

TCP方式

本地文件方式

 4.2.3、举例Node_Exporter 

Prometheus Server并不直接服务监控特定的目标，其主要任务负责数据的收集、存储、对外提供数据查询,的支持。

收集操作系统和硬件信息的组件

举例NODE_EXPORTER

LINUX系统

NODE_EXPORTER

使用ROOT用户部署,要不然没有

权限收集系统信息.KUBERNETES

环境给ACCOUNT权限

默认PULL数据

PROMETHEUS

-SERVER

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672728640385-9425db66-a00d-4cc4-b6b5-b949cfad4bd5.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_33%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.2.4、举例Nginx_Exporter 

exporter先通过nginx模块(nginx-module-vts)收集指标数据，进行加工转换成prometheus可以识别到的kv/metrics。

所有的Exporter程序都需要按照Prometheus的规范，返回监控的样本数据。

举例NGINX_EXPORTER

模块NGINX-

MODULE-VTS

PROMETHEUS

NGINX服务

NGINX_EXPORTER

-SERVER

通过EXPORTER获取NGINX数据监控数据,据格

式转换成PROMETHEUS可以识别/METRICS,

PUSH或PULL数据给到PROMETHEUS

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672726901651-b2529dc4-7635-40cf-90bd-7048e564748c.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_41%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.3、部署node_exporter 

| Ip规划          | 部署角色      | 版本                                                         |
| --------------- | ------------- | ------------------------------------------------------------ |
| 192.168.101.100 | node_exporter | [node_exporter-1.4.0.linux-amd64.tar.gz](https://github.com/prometheus/node_exporter/releases/download/v1.4.0/node_exporter-1.4.0.linux-amd64.tar.gz) |
| 192.168.101.101 | node_exporter | [node_exporter-1.4.0.linux-amd64.tar.gz](https://github.com/prometheus/node_exporter/releases/download/v1.4.0/node_exporter-1.4.0.linux-amd64.tar.gz) |

 4.3.1、下载node_exporter 

这个稳定版本与prometheus版本发布时间最合适。

TAGS

V1.5.0

VERIFIED

I ZIP

NOTESDOWNLOADS

ON NOV 30,2022-O-1B48970

TAR.GZ

VERIFIED

V1.4.1

阁 ZIP

|TAR.GZ

姓&DOWNLOADS

NOTES

A954C9F

ON NOV 30,2022

VERIFIED

V1.4.0

NOTESDOWNLOADS

)ON SEP 26,2022

ZIP

7DA1321

TAR.GZ

V1.4.0-RC.0

VERIFIED

图 ZIP

[I TAR.GZ

业DOWNLOADS

NOTES

ON JUL 28,2022

73DABDF

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672730395789-1cbc848d-0917-477e-a8d7-42bf556321c2.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_32%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



1、[官网最新版本](https://prometheus.io/download/#node_exporter)

2、[node_exporter其他版本下载](https://github.com/prometheus/node_exporter/releases/)

3、[docker、docker-compose方式部署参考](https://github.com/prometheus/node_exporter/)

[node_exporter-1.4.0.linux-wamd64.tar.gz](https://github.com/prometheus/node_exporter/releases/download/v1.4.0/node_exporter-1.4.0.linux-amd64.tar.gz)

 4.3.1、192.168.101.200 安装配置node_exporter 

1、解压安装包



[ROOT@PROMETHEUS-SERVER SERVER]#LS

PROMETHEUS-2.36.2.LINUX-AMD64

NODE_EXPORTER-1.4.0.LINUX-AMD64.TAR.GZ

PROMETHEUS

[IROOTEPROMETHEUS-SERVER SERVERL# TAR -XF NODE-EXPORTER-L.4.0.LINUX-AMD64.TAR.92

LIROOTEPROMETHEUS-SERVER SERVER)# IN -SV NODE-EXPORTER-1.4.0.LINUX-AMD64 NODE_EXPORTER

'NODE EXPORTER' -> 'NODE EXPORTER-1.4.0.LINUX-AMD64'

[ROOT@PROMETHEUS-SERVER SERVER]# LS -L

TOTAL 9876

31 AUG 6 09:03 NODE_EXPORTER

LRWXRWXRWX 1 ROOT ROOT

-> NODE_EXPORTER-1,4.0.LINUX-AMD64

1.4.0. LINUX-AMD64

56 SEP 26 2022 NODE_EXPORTER-1

DRWXR-XR-X 2 3434 3434

-RW-R--R-- 1 ROOT ROOT 10111972 SEP 26 20

2022 NODE_EXPORTER-1.4.0.LIN

LINUX-AMD64.TAR.GZ

29 JUN 3 23:43 PROMETHEUS->

LRWXRWXRWX 1 ROOT ROOT

PROMETHEUS-2.36.2.LINUX-AMD64

132 AUG 6 08:36 PROMETHEUS-2.36.2.

2.36.2.LINUX-AMD64

DRWXR-XR-X 4 3434 3434

[ROOT@PROMETHEUS-SERVER SERVER]# CD NODE EXPORTER

[ROOT@PROMETHEUS-SERVER NODE EXPORTER]# LS

LICENSE NODE EXPORTER NOTICE

[ROOT@PROMETHEUS-SERVER NODE_EXPORTER]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1691327119864-4a326176-89e3-4de2-a24a-a7fe57547fc9.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_57%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_750%2Climit_0)



2、配置Nodde_Exporter系统启动







 4.3.2、192.168.100.101 安装配置node_exporter 

手动安装操作步骤同4.3.1

 4.3.3、批量安装node_exporter 

如果公司内有几十台上百台机器需要安装node_exporter，可以使用ansible批量安装

使用ansible 批量安装node_exporter







 4.3.4、prometheus server收集node节点信息 

 cat /data/prometheus/prometheus.yml 



19 # A SCRAPE CONFIGURATION CONTAINING EXACTLY ONE ENDPOINT TO SCRAPE:

20

IT'S PROMETHEUS ITSELF,

\#HERE

SCRAPE_CONFIGS:

IB NAME IS ADDED AS A LABEL JOB-<JOB NAME>' TO ANY TIMESERIES SCRAPED FROM THIS CONFIG.

JOB NAME

\# THE

JOB_NAME:"PROMETHEUS"

E METRICS_PATH DEFAULTS TO '/METRICS'

O 'HTTP'

SCHEME DEFAULTS TO 'H

STATIC_CONFIGS:

S:["LOCALHOST:9090"]

TARGETS:

JOB_NAME:"NODE

EXPORTER

STATIC CONFIQS

92.168.101.100:9100","192.168.101.101:9100"]

["192.10

TARGETS:

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1691327878101-87281915-53cc-4d2c-b0f4-33fed1602448.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_56%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.3.5、调用prometheus接口热加载配置 



如果报错也会检查到，类似nginx -s reload

-X POST 192.168.101.200:9090/-/RELOAD

[ROOTOFANHT-OPS-PROMETHEUS-SERVER ANSIBLE-INSTALL-NODE_EXPORTER]# CURL

您在

/VAR/SPOOL/MAIL/ROOT 中有新邮件

[ROOTOFANHT-OPS-PROMETHEUS-SERVER ANSIBLE-INSTALL-NODE EXPOITER]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672740602433-464a8a4b-bae9-4ec3-b081-3738681f6bb8.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_58%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.4、登录prometheus-server前端 

http://192.168.101.100:9090/

PROMETHEUS

GRAPHSTATUS*HELP

LERTS

RUNTIME & BUILD INFORMATION

ENABLE HIGHLIGHTING

ENABLE LINTER

USE LOCALTIME

ENABLE QUERY HIST

配合ALERMANGER使用

TSDB STATUS

PROMETHEUS数据库状态

EXECUTE

EXPRESSION (PRESS SHIT+ENTER FOR NE

COMMAND-LINE FLAGS

前端查看PROMETHEUS-SERVER配置文件

CONFIGURATION

TABLE

GRAPH

RULES

告警规则,还没有配置

EVALUATION TIME

TARGETS

定义的监控主机组,TARGET

SERVICE DISCOVERY

NO DATA QUERIED YET

REMOVE PANEL

服务发现,配合其他组件CONSUL.在K8S中SERVICE

ADD PANEL

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692495322808-c49ac0ea-bd0b-41f3-b348-397bf462a266.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_82%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_963%2Climit_0)



 4.4.1、查看Target验证node_exporter 

有图更好理解Target

Exporter的一个实例称为Target

可以向Prometheus提供监控样本数据的程序都可以被称为一个Exporter。

 4.4.2、查看node_exporter metrics 

[http://192.168.101.100:9100/metrics](http://192.168.101.101:9100/metrics)

http://192.168.101.101:9100/metrics

以k:v形式，收集所有指标数据

食

不安全

192.168.101.101:9100/METRICS

更新

\# HELP GO GC DURATION SECONDS A SUMMARY OF THE PAUSE DURATION OF GARBAGE COLLECTION CYOLES

\# TYPE GO GE DURATION SECONDE SURMARY

GO GO DURATION SECONDS{QUANTILE-"O']3.3461E-05

GO_GE_DURATION_SECONDS(QUANTILE-"O.25")8.302E-05

GO_GC_DURATION SECONDS{QUANTILE-"O.5" 0.000189446

GO_GC_DURATION SECONDS{QUANTILE-"O.75"] 0.000294884

GO GE DURATION SECONDS(QUANTILE-"1*]0.024042343

GO_GC_DURATION_SECONDS_SUM 0.217500637

GO_GA DURATION BECONDA COUNT 695

\# HELP GO GOROUTINES WUMBER OF GOROUTINES THAT CURRENTLY EXIST.

&TYPE GO GOROUTINES GAUGE

\# HELP GO INFO INFORMATION ABOUT THE GO ENVIRONMENT.

A TYPE GO INFO GAUGE

GO INFO(VERSION 'GO1.19.1"> 1

\# HELP GO MEMETATS ALLAC BYTES NUMBER OF BYTES ALLOCATED AND AND ETILL IN USE.

\# TYPE GO MEMSTATS ALLOC BYTES GAUGE

GO MEMSTATS ALLOC BYTE8 2.623576E+06

\# HELR GO MEMATATE ALLOC BYTES TOTAL ROTAL NUMBER OF BYTEA ALLOCATED, EVEN IF IF IREED

\# TYPE GO MEMSTATS ALLOC BYTES TOTAL COUNTER

GO_MEMSTATS ALLOC BYTES TOTAL 1.165434432E+09

BER OF BYTES USED BY THE PROFILING BUCKET HASH TU

\# HELP GO MEMSTATS BUCK HASH SYS BYTES NUMBER OF

\# TYPE GO MEMETATS BUCK HASH SYS BYTES GAUGE

GO MEMSTATS BUCK HASH SYS BYTES 1.597173E+06

\# HELP GO MEMSTATS FREES TOTAL TOTAL NUMBER OF FREES.

\#TYPE GO MEMETATS FREES TOTAL COUNTER

GO MEMSTATS FREES TOTAL 1.429261E+07

\# HELP GO.MENSLATE GO SYS BYTES MUMBER OF BYTES USED EOR GARBAGO OOLLECTION HYBTEM METADATADATADATA

\# TYPE GO MEMSTATS GC SYS BYTES GAUGE

GO MEMSTATS GC SYS BYTES 9.522808E+06

 HELP GO MOMSTATS HEAP ALLOC BYTES BUMBER OF HEAP BYTES ALLOCATED AND STLL IN USO

F TYPE GO MEMSTATS HEAP ALLOC BYTES GAUGE

GO MEMATATS HEAP ALLOC BYTES 2.623576E+06

\# HELP GO MEMSTATS HEAP IDLE BYTES HUMBER OF HEAP BYTES WAITING TO BE USED.

PYPE GO MEMSTATS HEAP_IDLE_BYTES GAUGE

GO MEMSTATS HEAP IDLE BYTER 3.489792E+06

\# HELP GO MEMSTATS HEAP INUSE BYTES NUMBER OF HEAP BYTES THAT ARE IN USE.

&TYPE GO MEMSTATS HEAP_INUSE BYTES GAUGE

GO HMEMATATICHEAD-INNESPLUBJECTS QUUBEETOF ALLOCATED OBJECTS.

TYPE GO MEMSTATS HEAP OBJECTS GAUGE

GO MEMSTATS HEAP OBJECTS 22850

\# HELP GO MEMATATE HEAP RELEASED BYTES XUMBER OF HEAP BYTES RELEASED TO OS.

\# TYPE GO MEMSTATS HEAP RELEASED BYTES GAUGE

GO_MEMSTATS HEAP_RELEASED BYTES 2.531328E+06

\# HELP GO MEMALATS HEAP GYS BYTES WUMBER OF HEAP BYTES OBTAINED FROM SYSTEM.

\# TYPE GO MEMSTATS HEAP SYS BYTES GAUGE

GO MEMSTATS HEAP SYS BYTES 7.897088E+06

\# HETR GO MENATALS LAST,GO.TIME BECONDA MUMBER OF RECONDS BINCE 1970 OF LAST GARBAGE COLLECTION

\# TYPE GO MEMSTATS LAST GC TIME GECONDS GAUGE

GO MEMSTATS LAST GC_TIME_SECONDS 1.692495440673553E+09

\# HELP GO-MEMSTATS_LOOKUPS TOTAL TOTAL NUMBER OF POINTER LOOKUPS.

\# FYPE QO MEMATATS LOOKUPS TOTAL COUNTER

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692495470533-488c1bcc-2a25-42ac-9021-a9ce4ebf1a48.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1007%2Climit_0)



 4.4.1、简单使用PromQL 

[prometheus官网学习案例](https://prometheus.io/docs/prometheus/latest/querying/examples/)

 Prometheus提供⼀个函数式的表达式语⾔PromQL (Prometheus Query Language)，可以使⽤户实时 地查找和聚合时间序列数据，表达式计算结果可以在图表中展示，也可以在Prometheus表达式浏览器 中以表格形式展示，或者作为数据源, 以HTTP API的⽅式提供给外部系统使⽤。  

192.168.101.101:9100/METRICS

合

更新

不安全

'GAA IA NTAAKNTETETT HATAYMTAT

INNTROTR AASIN ATASHN ITAM ANAM ANAM TANT MANT MANN MANN IN

\# TYPE NODE MEMORY HUGETIB BYTES

2/3

X

ODE_MEMORY_MEMTOTAL_BYTESL

NODE MEMORY HUGETIB BYTEA 0

D INACTIVE ANON BYTES.

\# HELP NODE MEMORY INACTIVE ANON BYTES MORY IN

ORY INFORMATION FIELD IN

TYPE NODE MENORY INACTIVE ANON BYTES GAUGE

NODE MEMORY INACTIVE ANON BYTES 4.3020288E+07

\# HBLP NODE MEMORY INACTIVE BYTES HEMORY INFORMATION FIELD INACTIVE BYTES.

\# RYPE NODE MEMORY INACTIVE BYTES GAUGE

NODE MEMORY INACTIVE BYTES 4.0323072E+08

\# WELP NODE MEMORY INACTIVE EILE BYLEA MEMORY INFORMALION FIELD INACTLVE FILE BYTEA.

\#TYPE NODE MEMORY INACTIVE FILE BYTES GAUGE

NODE MEMORY INACTIVE FILE BYTES 3.60210432E+08

RMATION FIELD KRECLAIMABLE BYTES.

\# HELP NODE MEMORY-KRECLAIMABLE BYTES MEMORY INFORMATION

\# TYPE NODE MEMORY KRECLAIMABLE BYTES GAUGE

NODE MEMORY KREELAIMABLE BYTES 6.8788224E+07

\# EBLP NODO-MOMORY RERNELSTACK BYTES HEMORY INFORMATION FIELD RERNELSTACK BYTES.

ORY INFORMATION

\# TYPE NODE MEMORY KERNELSTACK BYTES GAUGE

NODE MEMORY KERNELSTACK BYTES 4.931584E+06

\# HBLP NODE MEMORY MAPPED BYTES LEMORY INFORMATION FIELD MAPPED BYTES.

A TYPE NODE_MEMORY_MAPPED_BYTES GAUGE

NODE MEMORY MAPPED BYTES 1.52756224E+08

\# HBLL NODE HEMORY MEMAVAILABLE BYTES HEMORY INTORMATION FIELD MEMAVALLABLE BYTES

\# PYPE NODE MENORY MEMAVAILABLE BYTES GAUGE

NODE_MEMORY MEMAVAILABLE BYTES 3.411017728E+09

\# EBLP NODE MEMORY BEMBREE BYTEA NEMORY INFORMATION FIELD MEMBIEE BYTED

\# TYPE NODE MEMORY MEMPREE BYTES GAUGE

NODE_MEMORY MEMFREE BYTES 3.00695552E+09

\# EBLP NODE MEMORY MEMTOTAL BYTES MEMORY INFORMATION FIELD MEMTOTAL BYTES

\# TYPE NODE MENORY HEMROTAL BYTEA GAUGE

NODE MEMORY MENTOTAL BYTES 4.114817024E+09

\# EBLR NODE MEINORY HLOCKED BYTES HEMORY INFORMATION ELD MLOCKED BYTES.

\# TYPE NODE MEMORY MLOCKED BYTES GAUGE

NODE_MEMORY_MLOCKED_BYTES 0

\# EBLP NODE MEMORY UPS UNSTABLE BYTES HEMORY INFORMATION EIGLD APS UNSTABLE BYTES

\#TYPE NODE MEMORY NFS UNSTABLE_BYTES GAUGE

NODE MEMORY NFS UNSTABLE BYTES 0

\# HELP NODE MEMORY PAGETABLEB BYTES WEMORY INFORMATION FIELD PAGERABLES BYTES.

A TYPE NODE MEMORY PAGERABLES BYTES GAUGE

NODE MEMORY_PAGERABLES BYTES 5.533696E+06

\# HELP NODE MEMORY PERCPU BYTES HEMORY INFORMATION FIELD PERCPU BYTES.

\#TYPE NODE MEMORY PEROPU BYTES GAUGE

NODE MEMORY_PERCPU_BYTES 4.6661632E+07

\# HELP NODE MENORY SRECLAIMABLE BYTES MEMORY

BEMORY INFORMATION FIELD SRECLAIMABLE_BYTES.

\#TYPE NODE_MEMORY_SRECLAIMABLE BYTES GAUGE

NODE MEMORY SRECLAIMABLE BYTES 6.8788224E+07

\# HBLP NODE MEMORY SUNRECLAIM BYTES MEMORY INFORMATION FIELD SUNRECLAIM BYTOS.

\# TYPE NODE MEMORY_SUNRECLAIM BYTES GAUGE

Y_SUNRECLAIM BYTES 9.2819456E+07

NODE MEMORY_S

S MEMORY INFORMATION FIELD SHMEMHUGEPAGES BYTES.

\# HELP NODE MEMORY SHMEMHUGEPAGES BYTES MEMORY I

F TYPE NODE M

MEMORY SHMEMHUGEPAGES BYTES GAUGE

SHMEMHUGEPAGES BYTES 0

4 HELP

IP NODE MEMORY SHMEMPMDHAPPED DYTES HEMORY INFORMATION FIELD SHMEMBMDMAPPED BYTES.

A TYPE NODE MEMORY

SHMOMPMDMAPPED

BYTES GAUGE

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692495493955-63c646bc-a58d-4492-9704-9191cf887ad7.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1005%2Climit_0)



1、查看node节点总内存 



不安全

更新

0

PROMETHEUS

ALERTS GRAPH STATUS*HELP

学

ENABLE AUTOCOMPLETE

ENABLE QUERY HISTORY

ENABLE LINTER

ENABLE HIGHLIGHTING

USE LOCAL TIME

EXECUTE

ISTANCE-"192.168.101.101:9100", JOB-"NODE EXPORTER"]

NODE MEMORY MEMTOTAL BYTES(INSTANCE

LOAD TIME: 23MS RESOLUTION:14S RESULT SERIES:1

GRAPH

TABLE

SHOW EXEMPLARS

END TIME

十

RES.(S

1H

5.50G

5.00G

4.50G

4.00G

3.50G

3.00G

01:30

01:20

01:10

01:25

01:15

01:35

01:00

00:45

00:50

00:55

01:05

00:40

NODE-MEMORY-MEMTOTAL-BYTES(INSTANCE二"192,168,101:9101:9100", JOB二"NODE-EXPORTER")

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692495524776-748125fe-71ad-4418-8127-da57f25e21a2.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1005%2Climit_0)



Node节点验证内存

![img](data:image/svg+xml;utf8,%3C%3Fxml%20version%3D%221.0%22%20encoding%3D%22UTF-8%22%3F%3E%0A%3Csvg%20width%3D%2218px%22%20height%3D%2216px%22%20viewBox%3D%220%200%2018%2016%22%20version%3D%221.1%22%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20xmlns%3Axlink%3D%22http%3A%2F%2Fwww.w3.org%2F1999%2Fxlink%22%3E%0A%20%20%20%20%3Cg%20id%3D%22%E9%A1%B5%E9%9D%A2-2%22%20stroke%3D%22none%22%20stroke-width%3D%221%22%20fill%3D%22none%22%20fill-rule%3D%22evenodd%22%3E%0A%20%20%20%20%20%20%20%20%3Cg%20id%3D%22%E5%85%B6%E4%BB%96%E5%8D%A1%E7%89%87%22%20transform%3D%22translate(-831.000000%2C%20-7078.000000)%22%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%3Cg%20id%3D%22%E7%BC%96%E7%BB%84-10%22%20transform%3D%22translate(729.000000%2C%207032.000000)%22%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cg%20id%3D%22%E7%BC%96%E7%BB%84-15%22%20transform%3D%22translate(102.000000%2C%2046.000000)%22%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cg%20id%3D%22image%E5%A4%87%E4%BB%BD%22%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Crect%20id%3D%22%E7%9F%A9%E5%BD%A2%22%20fill%3D%22%23000000%22%20fill-rule%3D%22nonzero%22%20opacity%3D%220%22%20x%3D%220%22%20y%3D%220%22%20width%3D%2216%22%20height%3D%2216%22%3E%3C%2Frect%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cpath%20d%3D%22M2.125%2C12.375%20L2.125%2C10.4960156%20L4.194125%2C8.042375%20C4.24407812%2C7.98315625%204.33529688%2C7.98315625%204.38525%2C8.042375%20L6.63471875%2C10.7098437%20L10.1869531%2C6.49754687%20C10.2369063%2C6.43832812%2010.328125%2C6.43832812%2010.3780781%2C6.49754687%20L13.875%2C10.64425%20L13.875%2C12.375%20L13.875%2C3.625%20L2.125%2C3.625%20L2.125%2C12.375%20Z%20M1.5%2C2.5%20L14.5%2C2.5%20C14.7761406%2C2.5%2015%2C2.72385937%2015%2C3%20L15%2C13%20C15%2C13.2761406%2014.7761406%2C13.5%2014.5%2C13.5%20L1.5%2C13.5%20C1.22385937%2C13.5%201%2C13.2761406%201%2C13%20L1%2C3%20C1%2C2.72385937%201.22385937%2C2.5%201.5%2C2.5%20Z%20M4.75%2C7%20C4.05964063%2C7%203.5%2C6.44035937%203.5%2C5.75%20C3.5%2C5.05964063%204.05964063%2C4.5%204.75%2C4.5%20C5.44035937%2C4.5%206%2C5.05964063%206%2C5.75%20C6%2C6.44035937%205.44035937%2C7%204.75%2C7%20Z%22%20id%3D%22%E5%BD%A2%E7%8A%B6%22%20fill%3D%22%238C8C8C%22%3E%3C%2Fpath%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3C%2Fg%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cg%20id%3D%22alert-fill%22%20transform%3D%22translate(9.000000%2C%207.000000)%22%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cpath%20d%3D%22M4.870227%2C0.216197695%20L8.94272401%2C7.35134981%20C9.01909103%2C7.48514349%209.01909206%2C7.64998385%208.94272672%2C7.78377851%20C8.86636138%2C7.91757317%208.72523106%2C8%208.57249701%2C8%20L0.427502991%2C8%20C0.274768942%2C8%200.133638625%2C7.91757317%200.0572732809%2C7.78377851%20C-0.0190920629%2C7.64998385%20-0.0190910287%2C7.48514349%200.0572759941%2C7.35134981%20L4.129773%2C0.216197695%20C4.20614384%2C0.0824130438%204.34727177%2C0%204.5%2C0%20C4.65272823%2C0%204.79385616%2C0.0824130438%204.870227%2C0.216197695%20Z%22%20id%3D%22%E5%BD%A2%E7%8A%B6%22%20fill%3D%22%23F7D844%22%3E%3C%2Fpath%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cpolygon%20id%3D%22%E8%B7%AF%E5%BE%84%22%20fill%3D%22%23FFFFFF%22%20points%3D%224.07248614%205.8378327%204.07248614%206.70269962%204.92751386%206.70269962%204.92751386%205.8378327%22%3E%3C%2Fpolygon%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cpolygon%20id%3D%22%E8%B7%AF%E5%BE%84%22%20fill%3D%22%23FFFFFF%22%20points%3D%224.07248614%202.81079846%204.07248614%204.97296577%204.92751386%204.97296577%204.92751386%202.81079846%204.07248614%202.81079846%22%3E%3C%2Fpolygon%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3C%2Fg%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3C%2Fg%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%3C%2Fg%3E%0A%20%20%20%20%20%20%20%20%3C%2Fg%3E%0A%20%20%20%20%3C%2Fg%3E%0A%3C%2Fsvg%3E%0A)



2、统计server可用内存

available = free + buffer + cache



0

PROMETHEUS

中

STATUS*HELP

GRAPH

ALERTS

ENABLE HIGHLIGHTING

ENABLE QUERY HISTORY

ENABLE LINTER

ENABLE AUTOCOMPLETE

USE LOCAL TIME

Q

NODE MEMORY_CACHED BYTES + NODE MEMORY MEMFREE BYTES/1024/1024

NODE_MEMORY_BUFFERS_BYTES

EXECUTE

RESOLUTION:14S

LOAD TIME:38MS

RESULT SERIES: 2

TABLE

GRAPH

1H

SHOW EXEMPLARS

END TIME

RES.(S)

585.00M

BYTES/1024/1024后单位

580.00M

2023-01-03 21:49:35 +08:00

VALUE:581902557.9257812

575.00M

单位BYTE

SENE5:

INSTANCE:192.168.101.200:9100

570.00M

565.00M

555.00M

550.00M

545.00M

535.00M

530.00M

21:25

21:20

21:35

21:15

21:40

21:05

21:10

21:45

21:00

20:55

21:30

TINSTANCE-'192.168,101 2009100 ,JOB-"FANHT-OPS-ALL-NODES"

(INSTANCE二192.168.101.201:9100,JOBB"FANHT-OPS-ALL-NODES"

CLICK CALOCT COEINE CTEL - CLIDKSTOSALE SSULTINLTINLE EARIOR

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672753910493-2739d0ca-5d8b-43ea-8c49-b5394e31be0e.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_999%2Climit_0)



linux查看内存剩余情况

[ROOTAFANHT-OPS-PROMETHEUS-SERVER ANSIBLE-INSTALL-NODE EXPORTER]# 1

\# FREE-M

TOTAL

BUFF/CACHE

SHARED

FREE

AVAILABLE

USED

MEM:

1485

75

675

219

590

590

0

O

O

SWAP:

IN /VAR/SPOOL/MAIL/ROOT

YOU HAVE NEW MAIL

[ROOTQFANHT-OPS-PROMETHEUS-SERVER ANSIBLE-INSTALL-NODE_EXPORTER]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672753923328-169408d2-f9b7-4a3f-be1d-71e9ce6baca2.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_43%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



2、查看server节点的cpu使用率





公

不安全

更新

0

PROMETHEUS

中

GRAPHSTATUS*HELP

ALERTS

ENABLE HIGHLIGHTING

USE LOCAL TIME

ENABLE AUTOCOMPLETE

ENABLE LINTER

ENABLE QUERY HISTORY

EXECUTE

AVG(IRATE(NODE OPU SECONDS TOTAL(INSTANCE;'192.168.101.101:9101:9100',MODER"IDLE"HI)

RESOLUTION:14S

LOAD TIME:35MS

RESULT SERIES:1

GRAPH

TABLE

十

SHOW EXEMPLARS

END TIME

1H

RES.(S)

0.99

0.99

FANMAN

RWYMA

0.98

0.98

2023-08-20 00:57:38+00:00

VALUE:0.979000000000874

0.97

SERIES

NO TABELS

0.97

0.96

0.96

0.95

0.95

01:00

01:40

01:50

01:45

01:35

01:05

01:15

01:20

01:10

01:25

00:55

01:30

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692496292204-2f44d049-8166-4fec-943b-0d052f5f5d5f.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1008%2Climit_0)



[ROOT@XINGLU-OPS-EXPORTER SERVER]# W

09:51:37 UP 10:15, 1 USER,

LOAD

AVERAGE:0.02,0.03,0.00

LOGIN@

JCPU

USER

FROM

IDLE

TTY

PCPUWHAT

192.168.101.82

00S00.W

14:45

0.00S

0.29S

ROOT

PTS/0

[ROOT@XINGLU-OPS-EXPORTER SERVER]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692496303729-0cbb0d11-d7d8-4f92-a941-befb305341e5.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_37%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.4.2、node_exporter 体验总结 

prometheus前端使用PromQL语法，跟mysql 语句类似。后面使用Grafana模板呈现出来各种炫酷的图表底层还是通过语句各种查询拼凑，如果碰到定制化需求还是要自己会写查询语句。

 三、PromQL了解 

[prometheus PromQL帮助文档](https://prometheus.io/docs/prometheus/latest/querying/basics/)

纯理论多些，枯燥，还是得继续难受一下

prometheus核心功能已经体验，会用prometheus各种数据查询语法。

 1、 PromQL数据基础 

 1.1、数据分类 

 1、瞬时向量
瞬时数据(instant vector):是⼀组时间序列，每个时间序列包含单个数据样本，⽐如 node_memory_MemFree_bytes查询当前剩余内存就是⼀个瞬时向量，该表达式的返回值中只会包含该时 间序列中的最新的⼀个样本值，⽽相应的这样的表达式称之为瞬时向量表达式，例如：例如： prometheus_http_requests_total 
2、范围向量
范围数据(range vector):是指在任何⼀个时间范围内，抓取的所有度量指标数据.⽐如最近⼀ 天的⽹卡流量趋势图， 例如：prometheus_http_requests_total[5m] 
3、标量
纯量数据(scalar)：是⼀个浮点数类型的数据值，使⽤node_load1获取到时⼀个瞬时向量，但是 可⽤使⽤内置函数scalar()将瞬时向量转换为标量，例如：scalar(sum(node_load1)) 字符串(string):简单的字符串类型的数据，⽬前未使⽤，（a simple string value; currently unused）。  

 1.2、 数据类型   

 1.2.1、 Counter(计数器) 

使用频次非常高

 计数器,Counter类型代表⼀个累积的指标数据，在没有被重置的前提下只增不减，⽐如磁盘I/O 总数、nginx的请求总数、⽹卡流经的报⽂总数等  

prometheus_http_requests_total

统计/metric 正常访问次数，数据只增不减

PROMETHEUS

GRAPH STATUS*HELP

ALERTS

ENABLE HIGHLIGHTING

ENABLE AUTOCOMPLETE

USE LOCAL TIME

ENABLE LINTER

ENABLE QUERY HISTORY

R

PROMETHEUS HTTP REQUESTS TOTALFCODE-"200",HANDLER-"/METRICS"Y

EXECUTE

RESOLUTION:14S

RESULT SERIES:1

LOAD TIME: 37MS

TABLE

GRAPH

区

十

SHOW EXEMPLARS

1H

END TIME

RES.(S)

2.80K

2.75K

2.70K

2.65K

2.60K

2.55K

2.50K

08:05

07:35

08:15

08:30

07:45

08:25

07:50

07:40

08:20

08:00

07:55

08:10

I PROMETHEUS HTTP REQUESTANCE-  CODE; 200: HANDLER:" /METRICS  INSTANCE- TOCA HOST9090:  PROMERHEUS7

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672792413537-4b779e4f-4e8e-4d76-8627-57ca4a935ec1.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1000%2Climit_0)



 1.2.2、 Gauge（仪表盘）  

使用频次非常高

 Gauge类型代表⼀个可以任意变化的指标数据，值可以随时增⾼或减少，如带宽速率、 CPU负载、内存利⽤率、nginx 活动连接数等。  

192.168.101,100;9090/GRAPH?9O.EXPRENODE-LOAD15&O.STAB-O8GO.STACKED-ORGO.SHOW,EXEMPLARS:JNGE INPUI-1

不安全

更新

PROMETHEUS

0

中

GRAPHSTATUS*HELP

ALERTS

ENABLE LINTER

ENABLE AUTOCOMPLETE

ENABLE HIGHLIGHTING

ENABLE QUERY HISTORY

USE LOCAL TIME

EXECUTE

NODE LOAD15

RESOLUTION:14S

LOAD TIME:18MS

RESULT SERIES: 2

TABLE

GRAPH

SHOW EXEMPLARS

十

RES.(S)

1H

END TIME

0.40

0.35

0.30

0.25

0.20

0.15

0.10

0.05

0.00

09:35

09:25

09:50

09:45

09:20

09:55

09:30

09:40

10:00

10:05

09:10

09:15

NODE LOAD15(INSTANCE-"192.168.101.100:9100",JOB-"NODE EXPORTER"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692497388910-4cd8f9fd-9783-433e-a861-177d391035f0.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1006%2Climit_0)



 1.2.3、 Histogram 直方数据 

绘制热点图搭配Histogram使用

指标生成的是直方数据，Prometheus抓取间隔的百分位数指标

 Histogram会在⼀段时间范围内对数据进⾏采样(通常是请求持续时间或响应⼤ ⼩等),假如每分钟产⽣⼀个当前的活跃连接数，那么⼀天就会产⽣1440个数据，查看数据的每间隔的绘图跨 度为2⼩时，那么2点的柱状图(bucket)会包含0点到2点即两个⼩时的数据，⽽4点的柱状图(bucket)则会 包含0点到4点的数据，⽽6点的柱状图(bucket)则会包含0点到6点的数据。

ITEM

COUNT

40

TOTAL COUNT20+40+30+10+5105

30

20

40

30

20

10

+LNF

10

5

BUCKET

RANGE

0250

250

100

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672793226764-4a31767b-c637-415e-a586-57007e217b11.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_52%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 1.2.4、 Summary  摘要   

 摘要，也是⼀组数据，统计的不是区间的个数⽽是统计分位数,从0到1，表示是0%~100%，如下 统计的是 0、0.25、0.5、0.75、1的数据量分别是多少  



食

不安全

更新

0

PROMETHEUS

GRAPHSTATUSHELP

尊

ALERTS

ENABLE LINTER

ENABLE HIGHLIGHTING

ENABLE AUTOCOMPLETE

ENABLE QUERY HISTORY

USE I

E LOCAL TIME

EXECUTE

GO_GE_DURATION_SECONDS{QUANTILE"0.25"]

LOAD TIME : 21MS

RESOLUTION:14S

GRAPH

TABLE

K 区

SHOW EXEMPLARS

RES.(S)

END TIME

1H

0.45M

0.40M

0.35M

0.30M

0.25M

0.20M

0.15M

0.10M

0.05M

0.00

17:20

17:25

17:40

16:50

17:35

17:10

17:15

17:30

17:05

16:45

17:00

16:55

ANCE二*192.168.101,100:9100",JOB-"NODE EXPORTER",QUANTILE二"0.25"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692524602237-72061078-f546-43ab-8e29-f3443c44887a.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1005%2Climit_0)





Untyped 位置类型使用很少，不介绍。



  2、PromQL-指标数据   

node_memory_MemTotal_bytes #查询node节点总内存⼤⼩ node_memory_MemFree_bytes #查询node节点剩余可⽤内存 node_memory_MemTotal_bytes{instance="192.168.101.101:9100"} #基于标签查询指定节点的总 内存 node_memory_MemFree_bytes{instance="192.168.101.101:9100"} #基于标签查询指定节点的可⽤ 内存 node_disk_io_time_seconds_total{device="sda"} #查询指定磁盘的每秒磁盘io node_filesystem_free_bytes{device="/dev/sda1",fstype="xfs",mountpoint="/"} #查看 指定磁盘的磁盘剩余空间

 \# HELP node_load1 1m load average. #CPU负载
 \# TYPE node_load1 gauge
 node_load1  

\# HELP node_load15 15m load average. 
\# TYPE node_load15 gauge 
node_load15  

\# HELP node_load5 5m load average. 
\# TYPE node_load5 gauge 
node_load5  











 3、 PromQL-匹配器   

 = :选择与提供的字符串完全相同的标签，精确匹配。 
!= :选择与提供的字符串不相同的标签，取反。
 =~ :选择正则表达式与提供的字符串（或⼦字符串）相匹配的标签。
 !~ :选择正则表达式与提供的字符串（或⼦字符串）不匹配的标签。  

 3.1.1、 查询格式   

查询格式<metric name>{<label name>=<label value>,...}

 node_load1{instance="192.168.101.100:9100", job="node_exporter"}

不安全

更新

PROMETHEUS

0

神

STATUS`HELP

ALERTS

GRAPH

ENABLE LINTER

ENABLE HIGHLIGHTING

ENABLE AUTOCOMPLETE

USE LOCAL TIME

ENABLE QUERY HISTORY

NODE LOADL[INSTANCE-"192.168.101.101:9100", JOB-"NODE EXPORTER"]

EXECUTE

LOAD TIME:49MS RESOLUTION ;14S

RESULT SERIES:1

GRAPH

TABLE

KK

十

SHOW EXEMPLARS

RES.(S)

1H

END TIME

0.70

0.60

2023-08-20 16:59:49+08:00

0.50

NODE LOAD1:0.53

NODE LOAD1

INSTANCE:192.168.101.101:9100

0.40

0.30

0.20

0.10

0.00

17:25

17:35

17:20

17:40

17:15

17:45

17:05

17:30

17:50

16:55

17:00

17:10

NODE-LOADL(INSTANCE-"192.168.101.101:9100",JOB-"NODE_EXPORTER,

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692525136197-b337d624-d04a-4586-9a0a-867b955720be.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1006%2Climit_0)

node_load1{job="promethues-node",instance="192.168.101.101:9100"} #精确匹配 node_load1{job="promethues-node",instance!="192.168.101.101:9100"} #取反 



192.168.101.100:9090/GRAPH?QO.EXPRENODE-LOAD1%7BJOB%3D"NODE_EXPORTER"7

不安全

更新

0

PROMETHEUS

尊

ALERTS GRAPH STATUS*HELP

ENABLE LINTER

ENABLE HIGHLIGHTING

USE LOCAL TIME

ENABLE QUERY HISTORY

ENABLE AUTOCOMPLETE

NODE LOADL(JOB-"NODE EXPORTER",INSTANCE!-"192.168.101.101:9100")

EXECUTE

LOAD TIME:18MS

RESOLUTION:14S

RESULT SERIES:1

GRAPH

TABLE

SHOW EXEMPLARS

十

1H

END TIME

RES.(S)

0.80

0.70

0.60

2023-08-20 17:00:53+08:00

NODE LOAD1:0.65

0.50

SERLES:

NODE LOAD1

INSTANCE:192.16B.101.100:9100

0.40

JOB:NODE EXPORTER

0.30

W

0.20

0.10

0.00

17:35

17:40

17:30

17:20

16:55

17:45

17:10

17:50

17:15

17:00

17:25

17:05

NODE LOAD1(INSTANCE-*192.168.101.100:9100~,JOB-"NODE_EXPORTER"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692525186709-52e98c68-514e-44f8-8f00-957f04fb193d.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1006%2Climit_0)

node_load1{instance=~"192.168.101.*:9100$"} #包含正则且匹配 



合

不安全

更新

PROMETHEUS

0

停

ALERTS GRAPH STATUS*HELP

ENABLE AUTOCOMPLETE

ENABLE QUERY HISTORY

ENABLE LINTER

ENABLE HIGHLIGHTING

USE LOCAL TIME

NODE LOADL(INSTANCE-"192.168.101.*:9100$"]

EXECUTE

LOAD TIME: 26MS

RESOLUTION:14S

RESULT SERIES: 2

TABLE

GRAPH

SHOW EXEMPLARS

END TIME

RES.(S)

1H

1.20

1.00

0.80

0.60

0.40

0.20

0.00

17:15

17:20

17:35

17:25

17:50

16:55

17:05

17:45

17:30

17:10

17:40

17:00

NODE_LOAD1(INSTANCE二*192.168.101.100:9100",JOB-"NODE_EXPORTER"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692525223539-bea7d10c-44a1-4c6a-bbe1-7f05d1a5fb58.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1007%2Climit_0)



\#包含正则且取反  

node_load1{instance!~"192.168.101.200:9100"} 

192.168-101-100:9090/GRAPH?9O.EXPR-NODE LOAD1967BINSTANCE!--192.168,101%3A9100.

不安全

更新

90.STACKED1&G0.SHOW_EXEMPLARS-0&GO.RANGE_I...

PROMETHEUS

0

梦

ALERTS GRAPH STATUS*HELP

ENABLE AUTOCOMPLETE

ENABLE QUERY HISTORY

ENABLE LINTER

ENABLE HIGHLIGHTING

USE LOCAL TIME

EXECUTE

NODE_LOADL{INSTANCE!-"192.168.101.101:9100"}

RESOLUTION:14S

LOAD TIME:15MS

TABLE

GRAPH

SHOW EXEMPLARS

RES.(S)

END TIME

1H

0.80

0.70

0.60

0.50

0.40

0.30

W

0.20

0.10

0.00

17:15

17:25

17:20

17:40

17:45

17:35

17:30

17:05

17:10

17:50

17:00

16:55

NODE-LOAD1(INSTANCE二"192.168.101.100:9100",JOB-"NODE_EXPORTER"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692525261960-da2132db-b411-47f9-b167-274bfd4e6327.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1007%2Climit_0)





 4、PromQL-时间范围 

s - 秒 m - 分钟 h - ⼩时 d - 天 w - 周 y - 年
\#瞬时向量表达式，选择当前最新的数据 
node_memory_MemTotal_bytes{} 
\#区间向量表达式，选择以当前时间为基准，查询所有节点
node_memory_MemTotal_bytes指标5分钟内 的数据 

100 * sum_over_time(node_memory_Active_bytes[5m]) / sum_over_time(node_memory_MemTotal_bytes[5m])
\#区间向量表达式，选择以当前时间为基准，查询指定节点
node_memory_MemTotal_bytes
指标5分钟内 的数据 
node_memory_MemTotal_bytes{instance="192.168.101.200:9100"} [5m]

 4.1、演示部分截 

食

119218, 0130030LR3OLERTERTORESKESKESKERTER IMELNUDERTESHENOV,ACIVE,DRBBRBBRBBA2FA2032FA20UN  INER INE

不安全

更新

_OVER_TIME(NODE_MEMORY_MEMTOT...

PROMETHEUS

0

GRAPHSTATUS*HELP

ALERTSG

ENABLE AUTOCOMPLETE O ENABLE HIGHLIGHTING

ENABLE LINTER

ENABLE QUERY HISTORY

USE LOCAL TIME

\* SUM OVER TIME(NODE MEMORY ACTIVE BYTES(SM)) / EUM OVER TIME(NODE MEMORY HEMTOTAL BYTES(SM])

EXECUTE

100*

LOAD TIME:22MS RESOLUTION:3S RESULT SERIES:2

TABLE

GRAPH

SHOW EXEMPLARS

END TIME

15M

RES.(S)

12.00

10.00

8.00

6.00

4.00

2.00

0.00

18:05

18:02

17:57

18:00

17:58

18:01

18:03

17:55

18:04

18:06

17:54

17:59

17:56

17:53

17:52

[INSTANCE-"192.168.101.100:9100",JOB-"NODE_EXPORTER]

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692526035174-b57a3733-d13b-4516-97a4-6324128b29e5.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1008%2Climit_0)





 5、PromQL-运算符 

\+ 加法 - 减法 * 乘法 / 除法 % 模 ^ 幂等
node_memory_MemFree_bytes/1024/1024 #将内存进⾏单位从字节转⾏为Mb
node_disk_read_bytes_total{device="sda"} + node_disk_written_bytes_total{device="sda"} #计算磁盘读写数据量

不安全  192.168.100:9090/GRAPH?GO.EXPRZNODE_MEMORY

更新

2F10248GO.TAB-08GO.STACKED-18GO.SHOW_EXEMPLARS-0&GO.RANGE_INP...

PROMETHEUS

ALERTS  GRAPH STATUS*HELP

ENABLE HIGHLIGHTING

ENABLE LINTER

ENABLE AUTOCOMPLETE

ENABLE QUERY HISTORY

USE LOCAL TIME

EXECUTE

NODE_MEMORY_MEMFREE BYTES/1024/1024/1024

LOAD TIME:27MS RESOLUTION:3S

S RESULT SERIES:2

GRAPH

TABLE

SHOW EXEMPLARS

END TIME

RES.(S)

15M

3.00

2.50

2.00

1.50

1.00

0.50

0.00

17:56

17:54

17:55

18:01

18:02

18:07

17:59

18:04

17:57

18:00

18:06

18:05

18:03

17:58

FINSTANCEX192.101.100:9100:JOB"NODE_EXPORTER"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692526147251-b6bb3336-694d-465d-b3fe-ad29d5471791.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1002%2Climit_0)



 6、PromQL-聚合运算 

 6.1、max、min、avg 

max() #最⼤值
 min() #最⼩值
 avg() #平均值 
计算每个节点的最⼤的网卡接收流量值： max(node_network_receive_bytes_total) by (instance) 计算每个节点最近五分钟每个device的最⼤流量 max(rate(node_network_receive_bytes_total[5m])) by (device)

PROMETHEUS

梦

STATUSHELP

GRAPH

ALERTS

ENABLE HIGHLIGHTING

ENABLE LINTER

ENABLE AUTOCOMPLETE

USE LOCAL TIME

ENABLE QUERY HISTORY

EXECUTE

MAX(NODE NETWORK RECEIVE BYTES TOTAL) BY (INSTANCE)

LOAD TIME: 51MS RESOLUTION:3S RESULT SERIES:2

GRAPH

TABLE

区6

SHOW EXEMPLARS

END TIME

RES.(S)

15M

450.00M

400.00M

350.00M

300.00M

250.00M

200.00M

150.00M

100.00M

50.00M

0.00

18:08

17:55

18:03

18:06

18:05

17:59

18:01

17:57

17:58

18:04

18:02

18:07

17:56

18:00

18:09

(INSTANCE'192.168.101.100:9100")

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692526200163-3e716b98-c5ca-4b02-976f-3eb35c29b4fe.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1007%2Climit_0)



 6.2、sum、count 

sum() #求数据值相加的和(总数)
sum(prometheus_http_requests_total) #最近总共请求数为3318次，⽤于计算返回值的总数(如http请求次数)

count() #统计返回值的条数
count(node_os_version) #⼀共两条返回的数据，可以⽤于统计节点数、Pod数量等 count_values() #对value的个数(⾏数)进⾏计数 count_values("node_version",node_os_version) #统计不同的系统版本节点有多少

192.168.101.100:9090/GRAPH?GO.EXPR-SUM(PROMETHEUS_HTTP-REQUESTS

不安全

更新

&G0.RANGE_INPUT15M

PROMETHEUS

GRAPHSTATUSHELP

0

ALERTS

停

ENABLE AUTOCOMPLETE

ENABLE LINTER

ENABLE QUERY HISTORY

ENABLE HIGHLIGHTING

USE LOCAL TIME

EXECUTE

SUM(PROMETHEUS HTTP REQUESTS TOTAL)

LOAD TIME :13MS

RESOLUTION:3S RESULT SERIES:1

GRAPH

TABLE

EVALUATION TIME

3318

REMOVE PANEL

ADD PANEL

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692526411741-32e4979a-33c4-4e78-88e0-09e86a3eada8.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1006%2Climit_0)





 6.3、abs、absent 补充图片 

abs() #返回指标数据的值 abs(sum(prometheus_http_requests_total{handler="/metrics"})) 
absent() #如果监指标有数据就返回空，如果监控项没有数据就返回1，可⽤于对监控项设置告警通知 absent(sum(prometheus_http_requests_total{handler="/metrics"}))

不安全

食

更新

PROMETHEUS

ALERTS

STATUSHELP

GRAPH

ENABLE AUTOCOMPLETE

ENABLE LINTER

ENABLE HIGHLIGHTING

USE LOCAL TIME

ENABLE QUERY HISTORY

EXECUTE

ABS(SUM(PROMETHEUS HTTP REQUESTS TOTAL(HANDLER-"/METRICS"))

LOAD TIME:19MS RESOLUTION:3S

RESULT SERIES:1

GRAPH

TABLE

EVALUATION TIME

2614

REMOVE PANEL

ADD PANEL

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692526489974-f0e5d77c-e282-4330-90ee-1a1de16ba1e3.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1004%2Climit_0)



 6.4、stddev、stdvar 

stddev() #标准偏差差
stddev(prometheus_http_requests_total) #5+5=10,1+9=10,1+9这⼀组的数据差异较⼤， 在系统是数据波动较⼤，不稳定 。

stdvar() #求⽅差 
stdvar(prometheus_http_requests_total)

 6.5、topk、bottomk 

topk() #样本值排名最⼤的N个数据 #取从⼤到⼩的前6个
 topk(6, prometheus_http_requests_total) 
bottomk() #样本值排名最⼩的N个数据 #取从⼩到⼤的前6个 
bottomk(6, prometheus_http_requests_total)

PROMETHEUS

GRAPH STATUS*HELP

ALERTS

ENABLE LINTER

ENABLE HIGHLIGHTING

USE LOCAL TIME

ENABLE QUERY HISTORY

ENABLE AUTOCOMPLETE

EXECUTE

TOPK(6, PROMETHEUS HTTP REQUESTS TOTAL)

LOAD TIME:31MS RESOLUTION:3S RESULT SERIES:8

GRAPH

TABLE

Y

END TIME

SHOW EXEMPLARS

RES.(S)

15M

3.50K

3.00K

2.50K

2.00K

1.50K

1.00K

500.00

0.00

18:20

18:22

18:12

18:10

18:23

18:14

18:21

18:16

18:17

18:13

18:15

18:11

18:19

18:18

18:24

PROUS" INS HTP REQUESTS JOTANCE;" INST 300;  JSTANDLER 'APIMINTANENALUES, INSTANCE; IOCIHOSOMETHETHEU

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692527085300-22acf23f-6a15-4e8e-b013-727136f18190.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1008%2Climit_0)



 6.6、rate、irate 

irate函数专门搭配counter数据类型使用的函数，irate获取的是指定时间范围内最近的两个数据来计算数据的差异，irate抖动更明显。

rate() #函数是专⻔搭配counter数据类型使⽤函数，功能是取counter数据类型在这个时间段中平均 每秒的增量平均数,适合⽤于计算相对平稳的数据。 
rate(prometheus_http_requests_total[5m]) 
rate(node_network_receive_bytes_total[5m]) 
irate() #函数是专⻔搭配counter数据类型使⽤函数，irate获取的是指定时间范围内最近的两个数据 来计算数据的速率，适合计算数据变化⽐较⼤的数据，显示的数据相对⽐较准确。 irate(prometheus_http_requests_total[5m]) irate(node_network_receive_bytes_total[5m])

PROMETHEUS

GRAPHSTATUS*HELP

ALERTS

ENABLE HIGHLIGHTING

ENABLE AUTOCOMPLETE

USE LOCAL TIME

ENABLE LINTER

)ENABLE QUERY HISTORY

IRATE(PROMETHEUS HTTP REQUESTS TOTAL(5ML)

EXECUTE

RESULT SERIES:  21

LOAD TIME:39MS RESOLUTION:35

GRAPH

TABLE

SHOW EXEMPLARS

RES.(S)

END TIME

15M

0.50

0.45

0.40

0.35

0.30

0.25

2023-08-20 18:15:58  +08:00

0.20

0.15

SERIOS:

CODE:500

HANDLER:///RELOAD

0.10

INSTANCE:LOCALHOST:9090

0.05

0.00

18:21

18:25

18:20

18:19

18:22

18:23

18:24

18:12

18:18

18:17

18:13

18:15

18:16

18:26

18:14

[CODE-"200",HANDLER二"-FREADY",INSTANCE-"LOCALHOST:9090",JOB二"PROMETHEUS"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692527195356-b4ed9484-a96d-48b6-8b6e-4004121814d8.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1008%2Climit_0)



 6.7、by、without 

\#by，在计算结果中，只保留by指定的标签的值，并移除其它所有的sum(rate(node_network_receive_packets_total{instance=~".*"}[10m])) by (instance) 
sum(rate(node_memory_MemFree_bytes[5m])) by (increase) 
\#without，从计算结果中移除列举的instance,job标签，保留其它标签 sum(prometheus_http_requests_total) without (instance,job)

 四、安装配置Grafana部署 

在prometheus-server上部署Grafana

安装节点： 192.168.101.100

 监听端口： 30000



计划第三部分写PromQL深入使用，prometheus各种数据查询语法必须要会的。又考虑到文章看了这么长还没见到好看的图表。

 1、Grafana简介 

Grafana是一款用Go语言开发的开源数据可视化工具，可以做数据监控和数据统计，带有告警功能。(告警功能会通过其他组件对接prometheus完成)

![img](https://cdn.nlark.com/yuque/0/2022/png/25866599/1657011791117-a83d1662-eba7-4e09-b1d4-53a9d1fbf9c9.png?x-oss-process=image%2Fresize%2Cw_825%2Climit_0%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_24%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_750%2Climit_0)



 2、二进制安装Grafana 

 2.1、常用地址 

1、[grafana官网](https://grafana.com/grafana/download)

2、[更多部署方式](https://grafana.com/docs/grafana/latest/setup-grafana/installation/)

3、[2023-01-03-当前最新版9.3.2](https://dl.grafana.com/enterprise/release/grafana-enterprise-9.3.2.linux-amd64.tar.gz)

4、[更多版本选择](https://github.com/grafana/grafana/releases)

5、[指定安装9.0.4版本](https://grafana.com/grafana/download/9.0.4)

建议不要下载最新版本，否则会出现跟一些模板上的查询数据不兼容问题。

 2.2、安装Grafana 

 2.2.1、yum 安装rpm包 

机器有网直接把rpm包下载到本地，



PM

--3033-03-04 97:52:40:- HTELEASE//GRARANA.3.385,64:TPRASE/TELEASE/EASE/GTANA-ENTERPRASE-9.3.385,64:TP

RESOLVING DL.GRAFANA.COM (AL.GRAFANA,COM)... 199:332.199.317. 199:332.199:317. 2394:404:4042:728.

CONNECTING TO DL.GRAFANA.COM (DL.GRAFANA.COM)1199:232.194:2171:443.... CONNECTED

HTTP REQUEST SENT, AWAITING RESPONSE... 200 OK

LENGTH:89548404 (85H)[APPLICATION/X-REDHAT-PACKAGE-MANAGER)

SAVING TO:'GRAFANA-ENTERPRISE-9.0,4-1.X86 64.RPM'

11.0

89,548,404

1008[ LEBBILLITESTER ILL

[89548404/89548404]

2023-01-04 07:52:52 (8.22 MB/S)

GRAFANA-ENTERPRISE-9.0.4-1.X86 64.RPM'SAVED

[ROOTENHT-OPS-PRAMETHEUS-SERVERVER PKGSI# SUDO YUM INSTALL GRAFANA-ENTERPRISE-9:0.4-1.X36.64.TPM

LOADED PLUGINS: FASTESTMIRROR

EXAMINING GRAFANA-ENTERPRISE-9.0.4-1.X86 64.RPM: GRAFANA-ENTERPRISE-9.0.4-1.X86 64

MARKING GRAFANA-ENTERPRISE-9.0.4-1.X86 64.RPM TO BE INSTALLED

RESOLVING DEPENDENCIES

.-> RUNNING TRANSACTION CHECK

---> PACKAGE GRAFANA-ENTERPRISE.X86 64 0:9.0.4-1 WILL BE INSTALLED

--- PROCESSING DEPENDENCY: FONTCONFIG FOR PACKAGE: GRAFANA-ENTERPRISE-9.0.4-1.X86.64

LOADING MIRROR SPEEDS FROM CACHED HOSTFILE

1 7.7

EPEL/X86 64/METALINK

BASE; MIRRORS.BFSU.EDU.CN

EPEL: MIRRORS.BFSU.EDU.CN

EXTRAS:MIRRORS.BFSU.EDU.CN

UBDATES: MIRRORS.BFSU.EDU.CN

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672790095856-7752adc5-1c63-4c47-99c2-a820d40051ca.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_990%2Climit_0)



 2.2.2、修改启动端口 



[ROOTEPROMETHEUS-SERVER SERVER]# SYSTEMCTL ENABLE GRAFANA-SERVER.SERVICE

[ROOT@PROMETHEUS-SERVER SERVER]# SYSTEMCTL START GRAFANA-SERVER.SERVICE

PS -EF|[ROOT@PROMETHEUS-SERVER SERVER]# PS -EF|GREP GRAFANA

132

GRAFANA12132

OVISIONING

ROOT  11877 0 09:43 PTS/0

00:00:00GREP-COLORAUTOGRAFANA

[ROOT@PROMETHEUS-SERVER SERVER]# NETSTAT -NTIP|GREP GRAFANA

12132/GRAFANA-SERVE

LISTEN

TCP6

0 :0000

*::

[ROOT@PROMETHEUS-SERVER SERVER]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1691329461396-0a424803-a037-461e-ba02-dd35ff3bd99c.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_84%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_982%2Climit_0)



 2.4、Grafana访问并配置数据源 

 2.4.1、访问grafana前端 

用户名admin

初始密码admin



 2.4.2、配置prometheus数据源 

DASHBOARDS

品

MANAGE DASHBOARDS AND FOLDERS

粤PLAYLISTS

LIBRARY PANELS

@ SNAPSHOTS

BROWSE

NEW FOLDER

SEARCH DASHBOARDS BY NAME

NEW DASHBOARD

LMPORT

FILTER BY TAG

FILTER BY STARRED

TESORT(DEFAULT A-Z)

NO DASHBOARDS MATCHING YOUR QUERY WERE FOUND.

ORGANIZATION:MAIN ORG

API KEYS

PREFERENCES

PLUGINS

TEAMS

USERS

DATA SOURCES

CONFIGURATION

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672760467953-906b5be1-3f3e-4574-b9f9-40ee72472736.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1000%2Climit_0)



CONFIGURATION

ORGANIZATION:MAIN ORG.

TH PREFERENCES

PLUGINS

]目

DATA SOURCES

RUSERS

TEAMS

API KEYS

NO DATA SOURCES DEFINED

ADD DATA SOURCE

? PROTIP:YOU CAN ALSO DEFINE DATA SOURCES THROUGH CONFIGURATION FILES.LEAM MORE

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672760558805-b5d6b871-8344-4503-a96f-2511a95d8476.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



数据源选择prometheus

ADD DATA SOURCE

CHOOSE A DATA SOURCE TYPE

品

FILTER BY NAME OR TYPE

TIME SERIES DATABASES

PROMETHEUS

OPEN SOURCE TIME SERIES DATABASE & ALERTING

CORE

GRAPHITE

OPEN SOURCE TIME SERIES DATABASE

CORE

INFLUXDB

OPEN SOURCE TIME SERIES DATABASE

CORE

OPENTSDB

OPEN SOURCE TIME SERIES DATABASE

CORE

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672760589568-e012f418-4fd0-4eb8-8a5d-b3344d6291be.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1000%2Climit_0)



DATA SOURCES/PROMETHEUS

TYPE:PROMETHEUS

HLT SETTINGS

品 DASHBOARDS

DEFAULT

PROMETHEUS

NAME O

HTTP

HTTP://192.168.101.100:9090

URL

SERVER(DEFAULT)

ACCESS

HELP

NEW TAG(ENTER KEY TO AR

TIMEOUT IN SECONDS

AUTH

WITH CREDENTIALS

BASIC AUTH

TLS CLIENT AUTH

WITH CA CERT

SKIP TLS VERIFY

FORWARD OAUTH IDENTITY

CUSTOM HTTP HEADERS

ADD HEADER

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1691329640144-3d3958e9-5ffe-4960-8beb-0436d3e6d3af.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1006%2Climit_0)



测试成功保存即可

ALERTING

X

DATASOURCE UPDATED

MANAGE ATERTS VIA ALERTING UI

品

CHOOSE

ALERTMANAGER DATA SOURCE

15S

SCRAPE INTERVAL

乌

QUERY TIMEOUT

60S

POST

O

HTTP METHOD

MISC

DISABLE METRICS LOOKUP

CUSTOM QUERY PARAMETERS

EXAMPLE:MAX_SOURCE_RESOLUTION-5M&TIMEOUT-10

EXEMPLARS

+ADD

TESTING...

EXPLORE

DELETE

SAVE &TEST

BACK

M DOCUMENTETION 1 ENFERERT 1 ENTERED) 1 ENTERERERERERERERED) 1 V9.0.4 GENSED) 1 V9.0.0.4 (G;

4 (C25601297) I   CH NEW VERSION AVALLABLE!

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672760825138-5ec1360f-7f1f-4599-86d8-83ee45c6be8e.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_999%2Climit_0)



 3、使用Grafana模板 

[官网模板](https://grafana.com/grafana/dashboards/)

 1、node_exporter模板 

 1.1、推荐模板ID 

1860、15172(基于11074模板做的优化)

DOWNLOADS

GRAFANA LABS

PRODUCTS

SIGN IN

CONTACT US

LEARN

SOLUTIONS

COMPANY

26

117

VISUALIZE YOUR ORACLE DATA

VISUALIZE YOUR JIRA DATA

MONITOR YOUR KUBERNETES

VISUALIZE YOUR MONGODB DATA

DEPLOYMENT

FILTER BY:

NODE_EXPORTER

CATEGORY

243 RESULTS

ALL

PANEL

ALL

DATA SOURCE

NODE EXPORTER FOR

NODE EXPORTER FULL

1NODE EXPORTER FOR

ALL

PROMETHEUS DASHBOARD EN

PROMETHEUS DASHBOARD

4.85/5 60 RATINGS

20201010

BASED ON 11074

22.0M DOWNLOADS

COLLECTOR T

TYPES

4.43/5 42 RATINGS

5/5   RATINGS

PROMETHEUS

受欢迎程度

ALL

404K DOWNLOADS

494K DOWNLOADS

PROMETHEUS

SORT BY

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672761048771-73297524-af0b-4a5a-917f-84be99dd6977.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1000%2Climit_0)



 1.2、导入模板两种方法 

import 模板ID需要机器能够上网，如果机器不能上网通过导入json文件完成

PRODUCTS

SOLUTIONS

SIGN IN

GRAL

COMPANY

DOWNLOADS

CONTACT US

OPEN SOURCE

ALL DASHBOARDS

NODE EXPORTER FULL

SIGN UP FOR GRAFANA CLOUD

CREATE FREE ACCOUNT+

REVIEWS

REVISIONS

OVERVIEW

GET THIS DASHBOARD

DATA SOURCE:

GRAFANA 9.2.3  1

PROMETHEUS 1.0.0

DEPENDENCIES:

GAUGE STAT

NEARLY ALL DEFAULT VALUES EXPORTED BY PROMETHEUS NODE EXPORTER GRAPHED.

LMPORT THE DASHBOARD TEMPLATE:

ONLY REQUIRES THE DEFAULT JOB-NAME:NODE,ADD AS MANY TARGETS AS YOU NEED IN

COPY ID TO CLIPBOARD

/ETC/PROMETHEUS/PROMETHEUS.YML!

JOB NAME:NODE

DOWNLOAD JSON

STATIC_CONFIGS:

TARGETS:['L'LOCALHOST:9100']

DOCS:IMPORTING DASHBOARDS

RECOMMENDED FOR PROMETHEUS-NODE-EXPORTER THE ARGUMENTS'-COLLECTORSYSTEMD -COLLECTOR:PROCESSESSES

BECAUSE THE GRAPH USES SOME OF THEIR METRICS.

SINCE REVISION 16, FOR PROMETHEUS-NODE-EXPORTER VO:18 OR NEWER, SINCE REVISION 12, FOR PROMETHEUS-

ID:1860

NODE-EXPORTER VO.16 OR NEWER.

BY: RFMOZ

AVAILABLE ON GITHUB: HTTPS://GITHUB.COM/RFMOZ/GRAFANA-DASHBOARDS.GIT

LAST UPDATE:2022-11-08T20:05:45

DOWNLOADS:22018.165

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672761161442-5c8efdae-24eb-48de-ba49-639407d54ce6.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1000%2Climit_0)



 1.2.1、ID导入1860模板 

不安全  192.168.101.2000:3000/DASHBOARDS

DASHBOARDS

品

MANAGE DASHBOARDS AND FOLDERS

品

冥PLAYLLSTS

品 LIBRARY PANELS

BROWSE

SNAPSHOTS

SEARCH DASHBOARDS BY NAME

NEW FOLDER

NEW DASHBOARD

LMPORT

1三SORT(DEFAULT A-Z)

FILTER BY STARRED

FILTER BY TAG

NO DASHBOARDS MATCHING YOUR QUERY WERE FOUND.

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672761240987-a8be5155-ef97-47cb-95a7-07a2984ea604.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



0

LMPORT

IMPORT DASHBOARD FROM FILE OR GRAFANA.COM

品

IMPORTING DASHBOARD FROM GRAFANA.COM

PUBLISHED BY

RFMOZ

2022-11-09 04:05:45

UPDATED ON

备注

OPTIONS

NODE_EXPORTER-1860

FOLDER

GENERAL

UNIQUE IDENTIFLER(UID)

THE UNIQUE IDENTIFIER (UID) OF A DASHBOARD CAN BE USED FOR UNIQUELY IDENTIFY A

DASHBOARD BETWEEN MULTIPLE GRAFANA INSTALLS. THE UID ALLOWS HAVING CONSISTENT URLS

FOR ACCESSING DASHBOARDS SO CHANGING THE TITLE OF A DASHBOARD WILL NOT BREAK ANY

BOOKMARKED LINKS TO THAT DASHBOARD.

RYDDDIPWK

CHANGE UID

选择给那个数据源导入模板

PROMETHEUS-SERVER

IMPORT

CANCEL

SED) I V9.0.4 (C25601297) I RH NEW VERSION AVAILABLE!

(FREEMUNITY I ENTERPRISE (FREE&UNLICENSED) I VEVER

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672761417984-91b81ecd-6e1f-4892-a1a7-366c8e3e2f58.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_998%2Climit_0)



G

品 GENERAL /NODE_EXPORTER-1860

LAST 24 HOURS

DEFAULT

192.168.101.200:9100

FANHT-OPS-ALL-NODES,

GRAFANA

JOB

HOST:

品

QUICK  CPU / DISK

SWAPUSED

CPU BUSY

RAM USED

UPTIME

PUCORES

SYS LOAD(5M AVG)

ROOT FS USED

SYS LOAD(15M AVG)

3.4 DAY

I

SWAP TO...

RAM TOTAL

ROOTFS T...

76%

N/A

16%

25.2%

7.08%

10%

26 GIB

0B

1 GIB

BASIC CPU/MEM/NET/DISK

MEMORY BASIC

CPUBASIC

100%

1.40 GIB

954 MIB

50%

477 MIB

25%

2023-01-03 08:44:00

0%

0B

BUSY USER

22:00  00:00

10:00

18:00

16:00

02:00  04:00

08:00

20:00

20:00

08:00

22:00

18:00

04:00

02:00

06:00

12:00  14:00

00:00

16:00

06:00

BUSY LOWAIT

RAM CACHE+BUFFER

RAM TOTAL

LDLE

SWAP USED

BUSY  SYSTEM

LUSY OTHER

BUSY USER

RAM USED

BUSY IRQS

BUSY OTHER

DISK SPACE USED BASIC

LDLE

100%

2 MB/S

75%

50%

25%

18:00

14:00

20:00

16:00

12:00

00:00

04:00

22:00

02:00

08:00

10:00

06:90

0%

TRANS ENS33

TRANS DOCKERO

TRANS LO

22:00

16:00

08:00

12:00

00:00

10:00

02:00  04:00

74:00

06:00

20:00

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672761662541-14f41837-bc04-495c-a0ea-b11edfaa175d.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_995%2Climit_0)



 1.2.2、Json文件上传11074模板 

GRAFANA LABS

PRODUCTS

SOLUTIONS

DOWNLOADS

OPEN SOURCE

SIGN IN

COMPANY

CONTACT US

-ALL DASHBOARDS

NODE EXPORTER FOR

PROMETHEUS

SIGN UP FOR GRAFANA CLOUD

DASHBOARD BASED ON 11074

CREATE FREE ACCOUNT

DASHBOARD FOR PRODUCTION ENV TROUBLESHOOTING,MODIFIED BASED ON

HTTPS://GRAFANA.COM/GRAFANA/DASHBOARDS/11074 ENHANCEMENTS, THE OVERALL RESOURCE OVERVIEW!

SUPPORT GRAFANA687,SUPPORT NODE EXPORTER VO.16 AND ABOVE.OPTIMIZE THE MAIN METRICS

GET THIS DASHBOARD

DISPLAY. INCLUDES:CPU,MEMORY, DISK 10,NETWORK, TEMPERATURE AND OTHER MONITORING METRICS

DATA SOURCE:

REVIEWS

OVERVIEWREVISIONS

PROMETHEUS 1.0.C

GRAFANA 8.2.2

DEPENDENCIES:

TABLE(OLD)

BAR GAUGE GRAPH(OLD) STAT

TIME SERIES

IMPORT THE DASHBOARD TEMPLATE:

COPY ID TO CLIPBOARD

DOWNLOAD JSON

DASHBOARD FOR PRODUCTION ENV TROUBLESHOOTING,MODIFIED BASED ON

HTTPS//GRAFANA.COM/GRAFANA/DASHBOARDS//11074 ENHANCEMENTS, THE OVERAL  RESOURCE OVERVIEW/ SUPPORT

DOCS:IMPORTING DASHBOARDS

GRAFANAB87,SUPPORT NODE EXPORTER VO.16 AND ABOVE.OPTIMIZE THE MAIN METRICS DISPLAY, INCLUDES: CPU,

MEMORY,DISK IO,NETWORK,TEMPERATURE AND OTHER MONITORING METRICS

ID:15172

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672762019420-effbbd8b-e6b8-4524-bc42-9e0a4cf82de3.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1000%2Climit_0)



导入下载好的json文件

O

LMPORT

IMPORT DASHBOARD FROM FILE OR GRAFANA.COM

品

UPLOAD JSON FILE

LMPORT VIA GRAFANA.COM

GRAFANA.COM DASHBOARD URL OR ID

LOAD

IMPORT VIA PANEL JSON

LOAD

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672762139899-67b31b24-f437-4d4a-8faf-adc21fb74313.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



LMPORT

IMPORT DASHBOARD FROM FILE OR GRAFANA.COM

品

OPTIONS

NODE EXPORTER FOR 11074

GENERAL

UNIQUE IDENTIFIER(UID)

THE UNIQUE IDENTIFIER(UID) OF A DASHBOARD CAN BE USED FOR UNIQUELY IDENTIFY A

DASHBOARD BETVEEN MULTIPLE GRAFANA INSTALLS. THE UID ALLOWS HAVING CONSISTENT URLS

FOR ACCESSING DASHBOARDS SO CHANGING THE TITLE OF A DASHBOARD WILL NOT BREAK ANY

BOOKMARKED LINKS TO THAT DASHBOARD.

W5KDRDKNZ

CHANGE UID

PROMETHEUS

PROMETHEUS-SERVER

IMPORT

CANCEL

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672789300008-84285bce-c328-4928-9840-ed2c0b535010.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



8

品 GENERAL / NODE EXPORTER FO

R  11074

LAST 1 HOUR

30S

192.168.101.200:9100+192.168.101.201:9100

GITHUB

FANHT-OPS-ALL-NODES,

ALL`

NIC

ORIGIN.PROM

HOST

NONE

品

RESOURCE OVERVIEW

ALL NODE OVERVIEW [JOB:FANHT-OPS-ALL-NODES,TOTAL:2]

IP(LINK TO DETAILS)

UPTIME

CPU

TCP_TW

HOSTNAME

TRANSMIT

MEM

CPU USED%

192.168.101.201:9100

1.99

13.65

FANHT-OPS-PROMETHEUS-NODE

O

5.99KB/S

401.63 B/S

0.00B/S

45.42%

0.59%

0.02

KB/S

GIB

DAY

192.168.101.200:9100

3.71

272.52

144.50

1.45

648.53

FANHT-OPS-PROMETHEUS-SERVER

32.19%

24.89%

95

0.00B/S

35

0.14

KB/S

KB/S

DAY

B/S

GIB

FANHT-OPS-ALL-NODES:OVERALL TOTAL DISK&AVERAGE DISK USED%

FANHT-OPS-ALL-NODES: OVERALL TOTAL 5M LOAD & AVERAGE CPU USED%

FANHT-OPS-ALL-NODES:OVERALL TOTAL MEMORY & AVERAGE MEMORY USE...

33.1%

10%

5.59 GIB

34%

55.9GIB

OVERALL AVERAGE

33.1%

TATAL 5M LOAD

ERALL AVERAGE USED%

37.3GIB

3.73 GIB

33.1%

AVERAGE USED%

32%

1.86 GIB

18.6 GIB

USED%

33.1%

0

31%

0.0B

07:40

07:20

07:30

07:30

07:20

07:10

07:00

07:00

07:40

07:40

07:00

06:50

07:10

07:10

07:20

07:30

06:50

TOTAL USED CURRENT:15.3 GIB

CPU CORES CURRENT:3

TOTAL USED CURRENT:1.8 GIB

TOTAL 5M LOAD CURRENT:0

TOTAL CURRENT:5.3 GIB

NT:0.380

TOTAL CURRENT:47.4 GIB

OVERALL AVERAGE USED%CURRENT:33.1%

OVERALL AVERAGE USED% CURRENT:9.3%

OVERALL AVERAGE USED% CURRENT:33.5%

RESOURCE DETAILS:[FANHT-OPS-PROMETHEUS-SERVER]

CPU IOWAIT

[FANHT-OPS-PROMETHEUS-SERVER]: DISK SPACE USED BASIC(EXT?/XFS)

UPTIME

INTERNET TRAFFIC PER HOUR ALL

4.2%

CPU BU...

DEVICE

MOUNTED ON SIZE A

0.00%

3 DAY

19.1 MIB

73.1%

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672789424062-808571bf-c949-46de-a3eb-e108c5d724ce.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1000%2Climit_0)



 2、生产模版ID汇总-待补充 

 2.1、xxx监控使用模版ID 

 2.2、xxx监控使用模版ID 

 4、插件管理 

缺少饼图插件



 4.1、在线安装插件 

 饼图插件未安装，需要提前安装  



LI PLUGINS INSTALL NATEL-DISCRETE-PANEL

[[ROOT@FANHT-OPS-PROMETHEUS-SERVER ~]# GRAFANA-CLI

DOWNLOADED NATEL-DISCRETE-PANEL VO.1.1 ZIP SUCCESSFULLY

PLEASA RASTART GRAFANA AFTOR INSTALLING PLUDINS, REFER TO  GTAFANS DOCURENTATION FOR INSTIONS IT NEES

您在/VAR/SPOOL/MAIL/ROOT 中有新邮件

L# SYSTEMCTL RESTART GRAFANA-SERVER.SERVICE

[[ROOT@FANHT-OPS-PROMETHEUS-SERVER ~]# SY;

[ROOT@FANHT-OPS-PROMETHEUS-SERVER ~]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672827187107-c22d23a6-1d9e-4088-b5d2-a44f51828225.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_77%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 4.2、离线安装插件 

[grafana官网插件](https://grafana.com/grafana/plugins/)



 4.3、查看安装完插件效果 

品 GENERAL / REDIS REDIS_EXPORTER 14615

2023-01-0417:20:45 TO 2023-01-04 18:14:35

四

三十

FANHT-OPS-REDIS-SERVER

通过插件已经安装成功,需要优化格式

CLUSTER UPTIME

UPTIME

MASTER

_"REDIS_CONNECTED_SLAVES",INSTANCE-"192.1680

NAME - "REDIS UP"INSTA

NAME

17:30

17:30

18:00

18:00

NO DATA

(-NAME--'REDIS_CONNECTED-SLAVES',INSTANCE-"192 168,101.201:9121''JOB--FANHT-OPS-REDIS-SERVERY:

{_NAME_"REDIS_UP

INSTANCE"192.168.101.201:9121,JOB-"FANHT-

0(1 小时,100.00%,1X)  TRANSITIONS:1

OPS-REDIS-SERVER":1(1 小时,99.54%.1X)

TRANSITIONS:1 DISTINCT:1

COMMANDS EXECUTED/SEC

0.500

HITS/MISSES PER SEC

CURRENT

{INSTANCE-'192.168.101.201:9121,JOB:'FANHT-OPS-REDIS-SERVER"

0.400

0.400

CURRENT

0.00

0.300

0.750

0.00

0.200

0.500

0.100

0.250

0

18:00

17:30

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672827365586-57b9f38f-654d-4014-9b14-94cc8565b003.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_82%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_959%2Climit_0)



 6、Grafana 页面调整演示 

 1、处理图片显示格式问题 

品 GENERAL / REDIS REDIS_EXPORTER 14615

2023-01-0417:20:45 TO 2023-01-04 18:14:35

四

三十

FANHT-OPS-REDIS-SERVER

通过插件已经安装成功,需要优化格式

CLUSTER UPTIME

UPTIME

MASTER

_"REDIS_CONNECTED_SLAVES",INSTANCE-"192.1680

NAME - "REDIS UP"INSTA

NAME

17:30

17:30

18:00

18:00

NO DATA

(-NAME--'REDIS_CONNECTED-SLAVES',INSTANCE-"192 168,101.201:9121''JOB--FANHT-OPS-REDIS-SERVERY:

{_NAME_"REDIS_UP

INSTANCE"192.168.101.201:9121,JOB-"FANHT-

0(1 小时,100.00%,1X)  TRANSITIONS:1

OPS-REDIS-SERVER":1(1 小时,99.54%.1X)

TRANSITIONS:1 DISTINCT:1

COMMANDS EXECUTED/SEC

0.500

HITS/MISSES PER SEC

CURRENT

{INSTANCE-'192.168.101.201:9121,JOB:'FANHT-OPS-REDIS-SERVER"

0.400

0.400

CURRENT

0.00

0.300

0.750

0.00

0.200

0.500

0.100

0.250

0

18:00

17:30

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672827365586-57b9f38f-654d-4014-9b14-94cc8565b003.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_82%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_959%2Climit_0)





 五、配置告警 

| 角色        | 机器            |
| ----------- | --------------- |
| Alermanager | 192.168.101.100 |

 1、Alertmanager 安装 

[alertmanager官网](https://prometheus.io/docs/alerting/latest/alertmanager/)

SERVICE DISCOVERY

PAGERDUTY

SHORT-LIVED

JOBS

KUBERNETES

FILE SD

ALERTMANAGER

EMAIL

PUSH METRICS

AT EXIT

DISCOVER

NOTIFY

TARGETS

ETC

PUSHGATEWAY

PROMETHEUS SERVER

PUSH

ALERTS

HTTP

PULL

TSDB

RETRIEVAL

SERVER

METRICS

PROMETHEUS

WEBUI

GRAFANA

NODE

HDD/SSD

JOBS/

EXPORTERS

APL CLIENTS

![prometheus官网原图.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672670963672-e750d6d9-37a9-4724-bb96-b222b7c47d79.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_39%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



Alertmanager是Prometheus监控系统的一个组件，它负责接收Prometheus发出的报警信息，并将报警信息转发给适当的接收人或系统

prometheus 触发告警过程

prometheus--->触发阈值--->超出持续时间--->alertmanager--->分组|抑制|静默--->媒体类型--->邮件|钉钉|飞书等。

PROMETHEUS告警流程了解

ALERTMANAGER

告警媒介

PROMETHEUS SERVER

PUSH ALRTS

EMAIL

NOTIFY

告警规则

ROUTER

钉钉

告警

RECEIVER

FEISHU

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672996637411-f40acc3f-7bb9-4beb-91ef-487c68b06acf.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_56%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)





 1.1、准备环境 

[当前最新版](https://github.com/prometheus/alertmanager/releases/download/v0.25.0/alertmanager-0.25.0.linux-amd64.tar.gz)

ALERTMANAGER

PROMETHEUS ALERTMANAGER O PROMETHEUS/ALERTMANAGER

0.25.0/2022-12-22 RELEASE NOTES

FILE NAME

OS

ARCH

SIZE

SHA256 CHECKSUM

ALERTMANAGER-0.25.0.DARWIN-AMD64.TAR.GZ

27.48 MIB

DARWIN

E9FAO7F094B8EFA3FLF209DC7D5LA7CI428574906C7FD8EAC9A3AED08B03ED63

AMD64

27.90 MIB

LINUX

ALERTMANAGER-0.25.0.LINUX-AMD64.TAR.GZ

AMD64

206CF787C01921574CAL71220BB9B48B043C3AD6E744017030FED586EB48E04B

ALERTMANAGER-0.25.0.WINDOWS-AMD64.ZIP

AMD64

28.54 MIB

WINDOWS

609359143F89B3AE3CDC86475EBF8710DB891318093C2881DC691B30D8C6FOAC

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672996997654-4018da0f-a336-440e-9311-2296eed0f7ba.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_34%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



使用次新稳定版本

[alertmanager-0.24.0.linux-amd64.tar.gz](https://github.com/prometheus/alertmanager/releases/download/v0.24.0/alertmanager-0.24.0.linux-amd64.tar.gz)

PALERTMANAGER-0.24.0.ILLUMOS-AMD64.TAR.GZ

24.4 MB

MAR 25,2022

ALERTMANAGER-0.24.0.LINUX-386.TAR.GZ

23.5MB

MAR 25,2022

ALERTMANAGER-0.24.0.LINUX-AMD64.TAR.GZ

24.7 MB

MAR 25,2022

ALERTMANAGER-0.24.0.LINUX-ARM64.TAR.GZ

22.9 MB

MAR 25, 2022

ALERTMANAGER-0.24.0.LINUX-ARMV5.TAR.GZ

22.9 MB

MAR 25,2022

ALERTMANAGER-0.24.0.LINUX-ARMV6.TAR.GZ

22.9 MB

MAR 25,2022

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1672997164145-7d804b05-6708-442a-800a-58fd5c4a6c4f.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_34%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 1.2、安装Alertmanager 

解压创建软连接

tar -xf alertmanager-0.24.0.linux-amd64.tar.gz -C /data/server
cd /data/server
ln -sv alertmanager-0.24.0.linux-amd64/ alertmanager

 1.3、服务系统启动 

vim /usr/lib/systemd/system/alertmanager.service 



 systemctl daemon-reload 
 systemctl enable alertmanager
 systemctl start alertmanager
 ps -ef |grep alertmanager
 netstat -ntlp|grep alert

 2、prometheus告警配置 

 2.1、添加告警规则 

mkdir -p /data/server/prometheus/rulers



 2.2、prometheus加载报警规则 



 2.3、规则验证 

/data/server/prometheus/promtool check rules /data/server/prometheus/rulers/node_exporter.yml

 2.4、prometheus热加载 

curl -X POST 192.168.101.100:9090/-/reload

 2.5、prometheus前端验证 

192.168.101.100:9090/ALERTS?SEARCH

不安全

更新

PROMETHEUS

0

C

ALERTS

GRAPHSTATUSHELP

SHOW ANNOTATIONS

PENDING(O

INACTIVE(10)

FIRING(0)

A

FILTER BY NAME OR LABELS

/DATA/SERVER/PROMETHEUS/RULERS/NODE-EXPORTER.YML>NODE-EXPORTER

INACTIVE

磁盘空间使用率告警(0ACTIVE)

服务器宏机告警(0 ACTIVE)

\>磁盘INODE使用率告警(0 ACTIVE)

内存使用率告警(0 ACTIVE)

\>CPU使用率告警(0 ACTIVE)

\> CPU负载告警(0 ACTIVE)

\>主机入方向流量告警(O ACTIVE)

主机出方向流量告警(0ACTIVE)

主机磁盘读告警(0ACTIVE)

主机磁盘写告警(0ACTIVE)

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692534084310-59c3486a-d01e-4111-83b4-7697d8d13ae3.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1006%2Climit_0)



 3、AlerManager配置告警媒介 

 3.1、邮件 

更新

网易免费邮

163

支持邮件全文搜索

自助查询

设置手机APP下载桌面端参与调研

开通邮箱会员

OPS09527@163.COM(3)

MAIL.163.COM

首页

通讯录

收件箱常规设置

应用中心

二写信

收信

邮箱密码修改

迹

OPS09527,

U

收件箱(3)

ENGLISH

红旗邮件

账号与邮箱中心

待

未读邮件

待办邮件

星标联系人邮件

邮箱安全设置

草础箱

看世界

邮推荐

网易严选

POP3/SMTP/IMAP

已发送

其他2个文件夹

[老罗推荐]A类天竺棉

更换皮肤

全棉针织拼色四件套

邮件标签

内衣般柔弹,纯棕吸汗透气,舒适操睡

邮箱中心

开通邮箱会员

超大附件

立即抢购

文件存储

邮箱附件

升级邮箱会员,尊享专属皮肤

办公工具

PDF转换工具

马上开通

APPT模板

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692542569801-a512586e-9891-42ea-9ac9-2a45a1356fee.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1004%2Climit_0)



网易免费邮

163

支持邮件全文搜索

PP下载桌面端

参与调研自助查询

开通邮箱会员

OPS09527@163.COM(3)~      手机APP

MAIL.163.COM

首页

收件箱

企业邮箱

设置

应用中心

通讯录

常规设置

建议您使用网易邮箱官方客户端,确保邮箱安全

邮箱密码修改

官方客户端具有邮件加密传输,邮笔登录二次验证等安全

签名

保障功能,有效避免信息泄露,账号被盗等潜在安全风险.

来信分类

扫一扫下载官网APP

账号与邮箱中心

POP3/SMTP/IMAP

邮箱安全设置

反垃圾/黑白名单

开启服务:

已关闭|开启

IMAP/SMTP服务

POP3/SMTP/IMAP

已关闭|开启

POP3/SMTP服务

POP3/SMTP/IMAP服务能让你在本地客户缩上收发邮件,了解更多>

文件夹和标签

多标签窗口

温馨提示:在第三方登录网易邮箱,可能存在邮件泄露风险,甚至危吉APPLE或其他

换肤

平台账户安全

提示

服务器地址:

POP3服务器:POP.163.COM

SMTP服务器:SMTP.163.COM

IMAP服务器:IMAP.163.COM

安全支持:

POP3/SMTP/IMAP服务全部支持SSL连接

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692542625192-7ec67261-ceb5-43d3-9f6b-f9cd601622b9.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1004%2Climit_0)



网易免费邮

163

支持邮件全文搜索

参与调研

设置

开通邮箱会员

下载桌面端

手机APP

自助查询

OPS09527@163.COM(3)

MAIL.163.COM

首页

通讯录

收件箱

企业邮箱

应用中心

设置

X

常规设置

POP3/SMTP/IMAP

部箱密码修改

开启服务:

IMAP/SMTP服务

已关闭|开启

签名

POP3/SMTP服务

已开启

关闭

来信分类

POP3/SMTP/IMAP服务能让你在本地客户端上收发邮件,了解更多>

账号与邮箱中心

温馨提示:在第三方登录网易邮箱,可能存在邮件泄露风险,甚至危吉APPLE或其他

邮箱安全设置

平台账户安全

反垃圾/黑白名单

POP3/SMTP/IMAP

收取选项:

收取最近30天邮件

文件夹和标签

收取全部邮件

多标签窗口

温馨提示:收取大量邮件,会耗费您更多的流量,建议您选择"收取最近30天邮件"

换肤

开启客户端删除邮件提醒

通知提醒:

当邮件客户端大量删除邮件时,系统会发送提醒信息

授权密码管理:

授权码是用于登录第三方邮件客户端的专用密码.

适用于登录以下服务:您开启的服务(例如POP3/IMAP/SMTP),EXCHANGE/CARDDAV/CALDAV服务.

使用设备

操作

启用时间

地除

2023.8.20

设备1

新增授权密码

每个账号最多设置5个授权密码

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692542600335-e6ab4abb-908d-4b93-8bd0-68ef152a80d3.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_990%2Climit_0)





192.168.101.100:9093/#/ALERTS

不安全

更新

ALERTMANAGER

NEW SILENCE

ALERTS SILENCES STATUS

HELP

RECEIVER:ALL

SILENCED

INHIBITED

FILTER

GROUP

文 SILENCE

CUSTOM MATCHER,E.G. ENV"PRODUCTION'

EXPAND ALL GROUPS

ALERTNAME"内存使用率告警.

2 ALERTS

十

2023-08-20T14:08:06.612Z

SILENCE

INFO

SOURCE

JOB-'NODE_EXPORTER'

OPSALERTNAME"内存使用率告警"

INSTANCE192.168.101.100:9100"

SEVERITYCRITICAL

&SILENCE

2023-08-20T14:08:06.612Z

\+ INFO

SOURCE

INSTANCE"192.168101.101:9100"

JOB-'NODE_EXPORTER

OPSALERTNAME"内存使用率告警"

SEVERITYCRITICAL"

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692542656162-571fed20-fd22-4544-9fe0-6b8b29198906.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1007%2Climit_0)



2 ALERTS FOR ALERTNAME内存使用率告警

VIEW IN ALERTMAGER

[2] FIRING

LABELS

ALERTNAME 内存使用率告警

192.168.101.100:9100

INSTANCE

JOB NODE_EXPORTER

OPSALERTNAME内存使用率告警

SEVERITY CRITICAL

ANNOTATIONS

DESCRIPTION  内存使用率告警15.88%,大于告警阈值90%

SUMMARY  内存使用率告警

SOURCE

LABELS

内存使用率告警

ALERTNAME内

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692542703442-e3d0213a-aea8-458d-9c07-4bd8a8dc695d.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_31%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 3.2、钉钉告警-钉钉插件(感兴趣了解) 

 3.2.1、创建钉钉机器人 

 3.2.2、[github](https://github.com/timonwong/prometheus-webhook-dingtalk/releases) 

 3.2.3、下载插件安装包 

[prometheus-webhook-dingtalk-2.1.0.linux-amd64.tar.gz](https://github.com/timonwong/prometheus-webhook-dingtalk/releases/download/v2.1.0/prometheus-webhook-dingtalk-2.1.0.linux-amd64.tar.gz) 

 3.2.4、参考钉钉官网使用 

[钉钉开发者文档](https://open.dingtalk.com/integration?integration-visible)

 3.2.5、安装Dingtalk组件 



6300TERI--B33-PROBETHELS-2.2,BRJE --B /AF -AF  /AF   BROOC-2333-2333-2,BRONETIR-231-33---R /A3TOLE [D

1K/

您在/VAR/SPOOL/MAIL/ROOT 中有新邮件

[ROOTOFANHT-OPS-PROMETHEUS-SERVER PKGS]# CD /DATA/PROMETHEUS/DINGTALK/

[ROOT@FANHT-OPS-PROMETHEUS-SERVER DINGTALK]# LS

PROMETHEUS-WEBHOOK-DINGTALK-2.1.0.LINUX-AMD64

C3OETEFSR-ORE PROUETTOUS-_R,BROT ANSTALE AN -AY PROETTEUS-4RTECK,TING3ALR

"PROMETHEUS-WEBHOOK-DINGTALK" -/ "PROMETHEUS-WEBHOOK-DINGTALK-2.1.9.LINUX-AMD64/"

VER DINGTALK]# LS PROMETHEUS-WEBHOOK-DINGTALK

[ROOT@FANHT-OPS-PROMETHEUS-SERVER

LICENSE PROMETHEUS-WEBHOOK-DINGTALK

CONFIG.EXAMPLE.YML

CONTRIB

[ROOT@FANHT-OPS-PROMETHEUS-SERVER DINGTALK]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1673161246574-9107279b-6b8f-41fa-8cc1-4274ac525ef6.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_81%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_943%2Climit_0)



 3.2.5、修改dingtalk 配置 

 3.2.6、dingtalk配置系统启动 

cat /usr/lib/systemd/system/prometheus-webhook-dingtalk.service



[ROOTOFANHT-OP3-PROMETHEUS-SERVER ")# CAT /UST/LIB/SYSTEMD/AYSTAM/PEOMETHEUS-WEKHOQK-DINGTOLK.SGRVICE

[UNIT]

DESCRIPTION-HTTPS://GITHUB.COM/TIMONWONG/PROMETHEUS-WEBHOOK-DINGTALK/TELEASES/

AFTERNETWORK-ONLINE.TARGET

[SERVICE]

RESTARTON-FAILURE

TALK/PROMETHEUS-WEBHOOK-DINGTALK/CONFIG.YML

[INSTALL]

WANTEDBYMULTI-USER.TARGET

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1673170841802-eb3af6f5-0875-4ce9-934b-31de161d0f64.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_81%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



cp config.example.yml config.yml

LROOTEFENHT-OPS-PROMETHEUS-SERVER PRONETHEUS-VVEBHOOK-DINGTALKJ# CD /DATA/PROMETHEUS/DINGTALK/

CROOT@FANHT

DINGTALK]# LS

T-OPS-PROMETHEUS-SERVER

PROMETHEUS-WEBHOOK-DINGTALK-2.1.0.1INUX-AMD64

PROMETHEUS-WEBHOOK-DINGTALK

[]# CD PROMETHEUS-WEBHOOK-DINGTALK

DINGTALK]# C

ROOT@FANHT-OPS-PROMETHEUS-SERVER

LROOTQFANHT-OPS-PROMETHEUS-SERVER PROMETHEUS-WEBHOOK-DINGTALK]# 1S

PROMETHEUS-WEBHOOK-DINGTALK

LICENSE

CONFIG.EXAMPLE.YML

CONTRIB

LROOTOFANHT-OPS-PROMETHEUS-SERVER PROMETHEUS-WEBHOOK-DINGTALKJ# CP CONFIG.EXAMPLE.YML

YML CONFIGYML

[ROOTQFANHT-OPS-PROMETHEUS-SERVER PROMETHEUS-WEBHOOK-DINGTALK]# VIM CONFIG-YML

@FANHT-OPS-PROMETHEUS-SERVER

PROMETHEUS-WEBHOOK-DINGTALK]#

OOTO

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1673168195324-68b865f4-b530-4cf0-b07a-cb2e18fd0f3d.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_62%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_750%2Climit_0)



vim config.yml 

url: https://oapi.dingtalk.com/robot/send?access_token=97c9a4c1fc86dfac161c4eda0fbdf018c0b8a0e2a1bfe96ef5e6cbbf38ae6aab

\# TARGETS, PREVIOUSLY WAS KNOWN AS

\###

"PROFILES"

TARGETS:

WEBHOOK1:

\# SECRET FOR SIGNATURE

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1673168130046-e7f1025d-a837-4977-8e46-9d4b0409181b.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_82%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



启动和停止服务命令

\# 查看状态
systemctl status prometheus-webhook-dingtalk
\# 启动
systemctl enable prometheus-webhook-dingtalk.service
systemctl start prometheus-webhook-dingtalk.service

[ROOTOFANHT-OPS-PROMETHEUS-SERVER ~]# SYSTEMCTL DAEMON-RELOAD

LROOTOFANHT-OPS-PROMETHEUS-SERVER ~!# SYSTEMCTL START PROMETHEUS-WEBHOOK-DINGTALK.SEIVICE

[ROOTQFANHT-OPS-PROMETHEUS-SERVER ~]# PS -EF |GREP DINGT

LK/PROMETHEUS-WEBHOOK-DINGTAL

1017:37?

52965

00:00:00 /DATA/PROMETHEUS/DINGTALK/PROMETHEUS-WEBHOOK-DINGTALK/P

ROOT

S/DINGTALK/PROMETHEUS-WEBHOOK-DINGTALK/CONFIG.YM1

K--CONFIG.FILE-/DATA/PROMETHEUS/DIN

0 17:40

54930  52584

0 PTS/1

00:00:00GREP--COLOR-AUTO DINGT

ROOT

1 ENABLE PROMETHEUS-WEBHOOK-DINGTALK.SERVICE

SYSTEMCTL ENAB

[ROOT@FANHT-OPS-PROMETHEUS-SERVER ~]#SY

您在/VAR/SPOOL/MAIL/ROOT中有新邮件

[ROOT@FANHT-OPS-PROMETHEUS-SERVER ~]#

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1673170849768-2e89ec38-f215-4f4b-9fa6-61d19bb87fc7.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_81%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_943%2Climit_0)



Alertmanager配置

vim alertmanager.yml



 3.4、PrometheusAlert(推荐使用) 

[Github](https://github.com/feiyu563/PrometheusAlert)

PrometheusAlert是开源的运维告警中心消息转发系统，支持主流的监控系统Prometheus、Zabbix，日志系统Graylog2，Graylog3、数据可视化系统Grafana、SonarQube。阿里云-云监控，以及所有支持WebHook接口的系统发出的预警消息，支持将收到的这些消息发送到钉钉，微信，email，飞书，腾讯短信，腾讯电话，阿里云短信，阿里云电话，华为短信，百度云短信，容联云电话，七陌短信，七陌语音，TeleGram，百度Hi(如流)等。

README.MD

[APP.CONF默认参数配置]

钉钉告警配置

飞书告警配置

企业微信告警配置

企业微信应用告警配置

阿里云短信和电话告警配置

腾讯云短信和电话告警配置

容联云电话告警配置

华为云短信配置

百度云短信配置

EMAIL配置

七陌短信和电话告警配置

TELEGRAM告警配置

BARK告警配置

百度HI(如流)告警配置

告警记录-ES接入配置

语音播报

.飞书机器人应用

[告警系统接入PROMETHEUSALERT配置]

PROMETHEUS接入配置

GRAYLOG接入配置

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1673171921902-15ea6ebe-17e3-426e-abe0-47f8caaa36a3.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_51%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_750%2Climit_0)





 3.4.1、安装PrometheusAlert 



设置

机器人名字:

PROMETHEUS监控告警

接收群组:

行路见知-告警通知

消息推送:

开启

WEBHOOK:

复制

HTTPS://OAPI.DINGTALK.COM/ROBOT/SEND?ACC

重置

请保管好此WEBHOOK 地址,不要公布在外部网站上,泄露有安全风险

取消

完成

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692626382359-0dd15cac-7062-481c-8b6f-456c14b3bdd6.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_38%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_674%2Climit_0)



curl 测试钉钉通知



curl -X POST "https://oapi.dingtalk.com/robot/send?access_token=f904300273b34c7bc27fe68367fdb9c69cd54516582760d2692843bdac4f20a1" -H "Content-Type: application/json" -d '{"msgtype": "text", "text": {"content": "This is a test message"}}'

机器人

PROMETHEUS监控告警

THIS IS A TEST MESSAGE

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692632271518-6819a939-051c-44d8-95b0-c9abecdf991f.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_24%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 3.4.2、登录控制台 

用户名：prometheusalert

密码：prometheusalert

192.168.101,100:8080/LOGIN

不安全

更新

PROMETHEUSALERT

登录控制中心

PROMETHEUSALERT

登录

REMEMBER ME

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692543833604-06a789dc-9e82-4588-8588-ffdb83b7f6c4.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_86%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1008%2Climit_0)



 3.4.2、Alertmanager配置告警媒介 





alertmanager.yml

YAML

 

[1692631636620377896]{ RECEIVER';"E

EMAIL-RECEIVER","STATUS"; "FIRING","ALERTS":I("STATUS",NFIRING","LABELS","ALERTNAME";"内存使用

][VALUE.GO:543]

2023/08/21  23:27:16.620 [D][]

率告警","INSTANCE':"192.168.101.100:9100",JOB

INSTONCE"""392,198,10L,1919198",","NODE,EXPORTER","OPSALER","NODE":" 'CRITICL

BELS":{"ALERTNAME"内存使用率告警","INSTANC

ITICAL"L,ANNOTATIONS":{"DESCRIPTION

"GENERATORURL"HTTP://XINGTU-

TAB-1""FINGERPRINT"B5783E41F885B7

EDALERTS":0}

2023/08/21  23:27:16.620 [E]

%3D%22CRITICA1822%2C

2023/08/21 23:27:16.621 [I] [PROMETHEU

[1692631636620377896] [DINGDING]["ERRCODE':0,"ERRMSQ":"OK"]

2023/08/21  23:27:16.741 [I]

[PROMETHEUSALERT,QO:449]

2023/08/21  23:27:16.742 [E]

[PROMETHEUSALERT,QO:402]

Y%3D%22CRITICAL%22%2C

2023/08/21 23:27:16.742 [PROMETHEUSALERT.GO:449]

LLYDEDITELLYDERTALLYULDETULLYDUTELLYDERTALLYDEDITELLYDEDITHINGLYDDY UNDEDITELLYDEDITELLYDEDILLYDDITIL

IVER INTEGRATION-EMAILLE) MSG:"NOTIFY ATTEMPT FAILED, WILL RETRY LATER" AT

687373-88-3ITI5I77:10.7562 CALLERENOTITY.90:73? LEVELEVELENARN CONPONENTEDISPATCHER RECEIVERECERECER

TEMPTS:L ERR"*EMAIL.LOGINAUTH AUTH: 550 USER HAS NO PERMISSION""

2023/08/21  23:27:16.839 [I] [

]ERRCODE':0,ERRMSG':'OK']

[PROMETHEUSALERT.GO:449] [1692631636620377896] [DINGDINGDING]

39 [D][SERVER.GO:2879] 1192.168.101.100| 200 | 218.953638MS

2023/08/21  23:27:16.839

POST

R/PROMETHEUSALERT

/PROMETHEUSALERT

MATCH

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692632220066-82942283-240a-46af-b3fd-57cf7f11d176.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_85%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_989%2Climit_0)



21:59

机器人

PROMETHEUS监控告警

PROMETHEUSALERT

测试告警

告警级别:测试

PROMETHEUSALERT

158****88888

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25866599/1692626411458-73fdd1d2-8f15-45a7-a050-95e6598ae50a.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_32%2Ctext_6KGM6Lev6KeB55-l%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



 3.4.3、调试告警格式 



 六、Exporter类型丰富 

待补充
