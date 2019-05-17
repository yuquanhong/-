# 微服务相关概念

## 什么是微服务？

微服务(（Microservices  Architecture)是一种架构风格，一个大型复杂软件应用由一个或多个微服务组成。系统中的各个微服务可被独立部署，各个微服务之间是松耦合的。每个微服务仅关注于完成一件任务并很好地完成该任务。在所有情况下，每个任务代表着一个小的业务能力。

微服务的概念源于2014年3月Martin Fowler所写的章“Microservices”http://martinfowler.com/articles/microservices.html

## 单体架构(Monolithic Architecture )

企业级的应用一般都会面临各种各样的业务需求，而常见的方式是把大量功能堆积到同一个单体架构中去。比如：常见的ERP、CRM等系统都以单体架构的方式运行，同时由于提供了大量的业务功能，随着功能的升级，整个研发、发布、定位问题，扩展，升级这样一个“怪物”系统会变得越来越困难。

这种架构模式就是把应用整体打包部署，具体的样式依赖本身应用采用的语言，如果采用java语言，自然你会打包成war包，部署在Tomcat或者Jetty这样的应用服务器上，如果你使用spring  boot还可以打包成jar包部署。其他还有Rails和Node.js应用以目录层次的形式打包

大部分企业通过SOA来解决上述问题，SOA的思路是把应用中相近的功能聚合到一起，以服务的形式提供出去。因此基于SOA架构的应用可以理解为一批服务的组合。SOA带来的问题是，引入了大量的服务、消息格式定义和规范。

多数情况下，SOA的服务直接相互独立，但是部署在同一个运行环境中（类似于一个Tomcat实例下，运行了很多web应用）。和单体架构类似，随着业务功能的增多SOA的服务会变得越来越复杂，本质上看没有因为使用SOA而变的更好。图1，是一个包含多种服务的在线零售网站，所有的服务部署在一个运行环境中，是一个典型的单体架构。

单体架构的应用一般有以下特点：

- 设计、开发、部署为一个单独的单元。

- 会变得越来越复杂，最后导致维护、升级、新增功能变得异常困难

- 很难以敏捷研发模式进行开发和发布

- 部分更新，都需要重新部署整个应用

- 水平扩展：必须以应用为单位进行扩展，在资源需求有冲突时扩展变得比较困难（部分服务需要更多的计算资源，部分需要更多内存资源）

- 可用性：一个服务的不稳定会导致整个应用出问题

- 创新困难：很难引入新的技术和框架，所有的功能都构建在同质的框架之上

- 运维困难：变更或升级的影响分析困难，任何一个小修改都可能导致单体应用整体运行出现故障。

  

## 微服务架构（Microservices Architecture）

微服务架构的核心思想是，一个应用是由多个小的、相互独立的、微服务组成，这些服务运行在自己的进程中，开发和发布都没有依赖。不同服务通过一些轻量级交互机制来通信，例如  RPC、HTTP  等，服务可独立扩展伸缩，每个服务定义了明确的边界，不同的服务甚至可以采用不同的编程语言来实现，由独立的团队来维护。简单的来说，一个系统的不同模块转变成不同的服务！而且服务可以使用不同的技术加以实现！

微服务设计

那我们在微服务中应该怎样设计呢。以下是微服务的设计指南：

- 职责单一原则（Single Responsibility Principle）：把某一个微服务的功能聚焦在特定业务或者有限的范围内会有助于敏捷开发和服务的发布。
- 设计阶段就需要把业务范围进行界定。
- 需要关心微服务的业务范围，而不是服务的数量和规模尽量小。数量和规模需要依照业务功能而定。
- 于SOA不同，某个微服务的功能、操作和消息协议尽量简单。
- 项目初期把服务的范围制定相对宽泛，随着深入，进一步重构服务，细分微服务是个很好的做法。

微服务消息

在单体架构中，不同功能之间通信通过方法调用，或者跨语言通信。SOA降低了这种语言直接的耦合度，采用基于SOAP协议的web服务。这种web服务的功能和消息体定义都十分复杂，微服务需要更轻量的机制。

同步消息 REST

同步消息就是客户端需要保持等待，直到服务器返回应答。REST是微服务中默认的同步消息方式，它提供了基于HTTP协议和资源API风格的简单消息格式，多数微服务都采用这种方式（每个功能代表了一个资源和对应的操作）

异步消息 – AMQP, STOMP, MQTT

异步消息就是客户端不需要一直等待服务应答，有应到后会得到通知。某些微服务需要用到异步消息，一般采用AMQP, STOMP, MQTT 这三种通讯协议

消息格式 – JSON, XML, Thrift, ProtoBuf, Avro

消息格式是微服务中另外一个很重要的因素。SOA的web服务一般采用文本消息，基于复杂的消息格式（SOAP）和消息定义（xsd）。微服务采用简单的文本协议JSON和XML，基于HTTP的资源API风格。如果需要二进制，通过用到Thrift,  ProtoBuf, Avro。

服务约定 – 定义接口 – Swagger, RAML, Thrift IDL

如果把功能实现为服务，并发布，需要定义一套约定。单体架构中，SOA采用WSDL，WSDL过于复杂并且和SOAP紧耦合，不适合微服务。

REST设计的微服务，通常采用Swagger和RAML定义约定。

对于不是基于REST设计的微服务，比如Thrift，通常采用IDL（Interface Definition Languages），比如Thrift IDL。

## 微服务集成 (服务间通信)

大部分微服务基于RPC、HTTP、JSON这样的标准协议，集成不同标准和格式变的不再重要。另外一个选择是采用轻量级的消息总线或者网关，有路由功能，没有复杂的业务逻辑。下面就介绍几种常见的架构方式。

点对点方式

点对点方式中，服务之间直接用。每个微服务都开放REST API，并且调用其它微服务的接口

很明显，在比较简单的微服务应用场景下，这种方式还可行，随着应用复杂度的提升，会变得越来越不可维护。这点有些类似SOA的ESB，尽量不采用点对点的集成方式。

API-网关方式

API网关方式的核心要点是，所有的客户端和消费端都通过统一的网关接入微服务，在网关层处理所有的非业务功能个。通常，网关也是提供REST/HTTP的访问API。服务端通过API-GW注册和管理服务。

所有的业务接口通过API网关暴露，是所有客户端接口的唯一入口。微服务之间的通信也通过API网关。

采用网关方式有如下优势：

- 有能力为微服务接口提供网关层次的抽象。比如：微服务的接口可以各种各样，在网关层，可以对外暴露统一的规范接口。
- 轻量的消息路由、格式转换。
- 统一控制安全、监控、限流等非业务功能。
- 每个微服务会变得更加轻量，非业务功能个都在网关层统一处理，微服务只需要关注业务逻辑

目前，API网关方式应该是微服务架构中应用最广泛的设计模式

消息代理方式

微服务也可以集成在异步的场景下，通过队列和订阅主题，实现消息的发布和订阅。一个微服务可以是消息的发布者，把消息通过异步的方式发送到队列或者订阅主题下。作为消费者的微服务可以从队列或者主题共获取消息。通过消息中间件把服务之间的直接调用解耦。

通常异步的生产者/消费者模式，通过AMQP, STOMP, MQTT 等异步消息通讯协议规范。

### 数据的去中心化

单体架构中，不同功能的服务模块都把数据存储在某个中心数据库中

每个微服务有自己私有的数据库，其它微服务不能直接访问。单体架构，用一个数据库存储所有数据

微服务方式，多个服务之间的设计相互独立，数据也应该相互独立（比如，某个微服务的数据库结构定义方式改变，可能会中断其它服务）。因此，每个微服务都应该有自己的数据库。

每个微服务有自己私有的数据库，其它微服务不能直接访问。每个微服务有自己私有的数据库，其它微服务不能直接访问。

数据去中心话的核心要点：

- 每个微服务有自己私有的数据库持久化业务数据
- 每个微服务只能访问自己的数据库，而不能访问其它服务的数据库
- 某些业务场景下，需要在一个事务中更新多个数据库。这种情况也不能直接访问其它微服务的数据库，而是通过对于微服务进行操作。

数据的去中心化，进一步降低了微服务之间的耦合度，不同服务可以采用不同的数据库技术（SQL、NoSQL等）。在复杂的业务场景下，如果包含多个微服务，通常在客户端或者中间层（网关）处理。

## 微服务架构的优点：

- 每个服务都比较简单，只关注于一个业务功能。
- 微服务架构方式是松耦合的，可以提供更高的灵活性。
- 微服务可通过最佳及最合适的不同的编程语言与工具进行开发，能够做到有的放矢地解决针对性问题。
- 每个微服务可由不同团队独立开发，互不影响，加快推出市场的速度。
- 微服务架构是持续交付(CD)的巨大推动力，允许在频繁发布不同服务的同时保持系统其他部分的可用性和稳定性。

## 微服务架构的缺点：

微服务的一些想法在实践上是好的，但当整体实现时也会呈现出其复杂性。

- 运维开销及成本增加：整体应用可能只需部署至一小片应用服务区集群，而微服务架构可能变成需要构建/测试/部署/运行数十个独立的服务，并可能需要支持多种语言和环境。这导致一个整体式系统如果由20个微服务组成，可能需要40~60个进程。
- 必须有坚实的DevOps开发运维一体化技能：开发人员需要熟知运维与投产环境，开发人员也需要掌握必要的数据存储技术如NoSQL，具有较强DevOps技能的人员比较稀缺，会带来招聘人才方面的挑战。
- 隐式接口及接口匹配问题：把系统分为多个协作组件后会产生新的接口，这意味着简单的交叉变化可能需要改变许多组件，并需协调一起发布。在实际环境中，一个新品发布可能被迫同时发布大量服务，由于集成点的大量增加，微服务架构会有更高的发布风险。
- 代码重复：某些底层功能需要被多个服务所用，为了避免将“同步耦合引入到系统中”，有时需要向不同服务添加一些代码，这就会导致代码重复。
- 分布式系统的复杂性：作为一种分布式系统，微服务引入了复杂性和其他若干问题，例如网络延迟、容错性、消息序列化、不可靠的网络、异步机制、版本化、差异化的工作负载等，开发人员需要考虑以上的分布式系统问题。
- 异步机制：微服务往往使用异步编程、消息与并行机制，如果应用存在跨微服务的事务性处理，事务的实现更具挑战性，其实现机制会变得复杂化。
- 可测性的挑战：在动态环境下服务间的交互会产生非常微妙的行为，难以可视化及全面测试。经典微服务往往不太重视测试，更多的是通过监控发现生产环境的异常，进而快速回滚或采取其他必要的行动。但对于特别在意风险规避监管或投产环境错误会产生显著影响的场景下需要特别注意。

## 关于微服务架构的取舍

- 在合适的项目，合适的团队，采用微服务架构收益会大于成本。
- 微服务架构有很多吸引人的地方，但在拥抱微服务之前，也需要认清它所带来的挑战。
- 需要避免为了“微服务”而“微服务”。
- 微服务架构引入策略 – 对传统企业而言，开始时可以考虑引入部分合适的微服务架构原则对已有系统进行改造或新建微服务应用，逐步探索及积累微服务架构经验，而非全盘实施微服务架构。

# Dubbo和Spring Cloud微服务架构

微服务架构是互联网很热门的话题，是互联网技术发展的必然结果。它提倡将单一应用程序划分成一组小的服务，服务之间互相协调、互相配合，为用户提供最终价值。虽然微服务架构没有公认的技术标准和规范或者草案，但业界已经有一些很有影响力的开源微服务架构框架提供了微服务的关键思路，例如Dubbo和Spring  Cloud。各大互联网公司也有自研的微服务框架，但其模式都于这二者相差不大。

**微服务主要的优势如下：**

1、降低复杂度

将原来偶合在一起的复杂业务拆分为单个服务，规避了原本复杂度无止境的积累。每一个微服务专注于单一功能，并通过定义良好的接口清晰表述服务边界。每个服务开发者只专注服务本身，通过使用缓存、DAL等各种技术手段来提升系统的性能，而对于消费方来说完全透明。

2、可独立部署

由于微服务具备独立的运行进程，所以每个微服务可以独立部署。当业务迭代时只需要发布相关服务的迭代即可，降低了测试的工作量同时也降低了服务发布的风险。

3、容错

在微服务架构下，当某一组件发生故障时，故障会被隔离在单个服务中。 通过限流、熔断等方式降低错误导致的危害，保障核心业务正常运行。

4、扩展

单块架构应用也可以实现横向扩展，就是将整个应用完整的复制到不同的节点。当应用的不同组件在扩展需求上存在差异时，微服务架构便体现出其灵活性，因为每个服务可以根据实际需求独立进行扩展。

本文主要围绕微服务的技术选型、通讯协议、服务依赖模式、开始模式、运行模式等几方面来综合比较Dubbo和Spring  Cloud  这2种开发框架。架构师可以根据公司的技术实力并结合项目的特点来选择某个合适的微服务架构平台，以此稳妥地实施项目的微服务化改造或开发进程。

## **一、核心部件**

微服务的核心要素在于服务的发现、注册、路由、熔断、降级、分布式配置，基于上述几种必要条件对Dubbo和Spring Cloud做出对比。

### **1、总体架构**

- Dubbo 核心部件（如下图）:
- Provider： 暴露服务的提供方，可以通过jar或者容器的方式启动服务
- Consumer：调用远程服务的服务消费方。
- Registry： 服务注册中心和发现中心。
- Monitor： 统计服务和调用次数，调用时间监控中心。（dubbo的控制台页面中可以显示，目前只有一个简单版本）
- Container：服务运行的容器。

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYP9g097UTKqhM9BYAMsAuJQzo1RIjoBHQaFicRunibDCWzKiaibJ0D3iavbkQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
▲Dubbo 总体架构

Spring Cloud总体架构如下图

- Service Provider： 暴露服务的提供方。
- Service Consumer：调用远程服务的服务消费方。
- EureKa Server： 服务注册中心和服务发现中心。

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYPp5OMHiaT70miaplmQX43jlj2QicOEAlFrBpXpx9dSjheoBTUILtK22BQA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
▲Spring Cloud总体架构

**点评：从整体架构上来看，二者模式接近，都需要需要服务提供方，注册中心，服务消费方。**

### **2、微服务架构核心要素**

|              | Dubbo         | Spring Cloud                 |
| ------------ | ------------- | ---------------------------- |
| 服务注册中心 | Zookeeper     | Spring Cloud Netflix Eureka  |
| 服务调用方式 | RPC           | REST API                     |
| 服务监控     | Dubbo-monitor | Spring Boot Admin            |
| 断路器       | 不完善        | Spring Cloud Netflix Hystrix |
| 服务网关     | 无            | Spring Cloud Netflix Zuul    |
| 分布式配置   | 无            | Spring Cloud Config          |
| 服务跟踪     | 无            | Spring Cloud Sleuth          |
| 消息总线     | 无            | Spring Cloud Bus             |
| 数据流       | 无            | Spring Cloud Stream          |
| 批量任务     | 无            | Spring Cloud Task            |
| ......       | ......        | ......                       |

Dubbo只是实现了服务治理，而Spring Cloud子项目分别覆盖了微服务架构下的众多部件，而服务治理只是其中的一个方面。Dubbo提供了各种Filter，对于上述中“无”的要素，可以通过扩展Filter来完善。

例如

1．分布式配置：可以使用淘宝的diamond、百度的disconf来实现分布式配置管理

2．服务跟踪：可以使用京东开源的Hydra，或者扩展Filter用Zippin来做服务跟踪

3．批量任务：可以使用当当开源的Elastic-Job、tbschedule

**点评：从核心要素来看，Spring Cloud 更胜一筹，在开发过程中只要整合Spring Cloud的子项目就可以顺利的完成各种组件的融合，而Dubbo缺需要通过实现各种Filter来做定制，开发成本以及技术难度略高。**

## **二、通讯协议**

基于通讯协议层面对2种框架支持的协议类型以及运行效率方面进行比较；

### **（一）、支持协议**

### 1、Dubbo：dubbo使用RPC通讯协议，提供序列化方式如下：

dubbo：Dubbo缺省协议采用单一长连接和NIO异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况

rmi：RMI协议采用JDK标准的java.rmi.*实现，采用阻塞式短连接和JDK标准序列化方式

Hessian:Hessian协议用于集成Hessian的服务，Hessian底层采用Http通讯，采用Servlet暴露服务，Dubbo缺省内嵌Jetty作为服务器实现

http:采用Spring的HttpInvoker实现

Webservice:基于CXF的frontend-simple和transports-http实现

2、Spring Cloud：Spring Cloud 使用HTTP协议的REST API

### **（二）、性能比较**

使用一个Pojo对象包含10个属性，请求10万次，Dubbo和Spring Cloud在不同的线程数量下，每次请求耗时（ms）如下：

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYPaPey42WR7PfB2Oa3rbEsicrBJyTnzcbB2ejfTVVtGsIUjHXnYn8MNGg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

说明：客户端和服务端配置均采用阿里云的ECS服务器，4核8G配置，dubbo采用默认的dubbo协议

**点评：dubbo支持各种通信协议，而且消费方和服务方使用长链接方式交互，通信速度上略胜Spring Cloud，如果对于系统的响应时间有严格要求，长链接更合适。**

## **三、服务依赖方式**

Dubbo：服务提供方与消费方通过接口的方式依赖，服务调用设计如下：

- interface层：服务接口层，定义了服务对外提供的所有接口
- Molel层：服务的DTO对象层，
- business层：业务实现层，实现interface接口并且和DB交互

因此需要为每个微服务定义了各自的interface接口，并通过持续集成发布到私有仓库中，调用方应用对微服务提供的抽象接口存在强依赖关系，开发、测试、集成环境都需要严格的管理版本依赖。如果想免费学习Java工程化、高性能及分布式、深入浅出。微服务、Spring，MyBatis，Netty源码分析的朋友可以加我的Java进阶群694549689，群里有阿里大牛直播讲解技术，以及Java大型互联网技术的视频免费分享给大家。

通过maven的install &  deploy命令把interface和Model层发布到仓库中，服务调用方只需要依赖interface和model层即可。在开发调试阶段只发布Snapshot版本。等到服务调试完成再发布Release版本，通过版本号来区分每次迭代的版本。通过xml配置方式即可方面接入dubbo，对程序无入侵。

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYPHkgdazapAkPiaH1fVoQGv6ZkNTWmiajwV8GdmDIwjVqY7K4TjGbia88xw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

▲Dubbo接口依赖方式

Spring Cloud：服务提供方和服务消费方通过json方式交互，因此只需要定义好相关json字段即可，消费方和提供方无接口依赖。通过注解方式来实现服务配置，对于程序有一定入侵。

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYPEJNesa1QeDKckap9jdxsibGSjMX60LAe7ANBCo3ficBQqjqaNtY9sWoA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

**点评：Dubbo服务依赖略重，需要有完善的版本管理机制，但是程序入侵少。而Spring Cloud通过Json交互，省略了版本管理的问题，但是具体字段含义需要统一管理，自身Rest API方式交互，为跨平台调用奠定了基础。**

## **四、组件运行流程**

下图中的每个组件都是需要部署在单独的服务器上，gateway用来接受前端请求、聚合服务，并批量调用后台原子服务。每个service层和单独的DB交互。

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYPWddFl8HhxkhVyGruRthDp8p73P001lZXAkHakIOywCP05GdWmBz05A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

▲Dubbo组件运行流程

- gateWay:前置网关，具体业务操作，gateWay通过dubbo提供的负载均衡机制自动完成
- Service：原子服务，只提供该业务相关的原子服务
- Zookeeper：原子服务注册到zk上

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYPPELIP0AwkaFfxrl1P0LPkFhsWaibalJtem8TdnWo1icVNiccj3d0f1flg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

▲Spring Cloud 组件运行

Spring Cloud

- 所有请求都统一通过 API 网关（Zuul）来访问内部服务。
- 网关接收到请求后，从注册中心（Eureka）获取可用服务。
- 由 Ribbon 进行均衡负载后，分发到后端的具体实例。
- 微服务之间通过 Feign 进行通信处理业务。

**点评：业务部署方式相同，都需要前置一个网关来隔绝外部直接调用原子服务的风险。Dubbo需要自己开发一套API 网关，而Spring Cloud则可以通过Zuul配置即可完成网关定制。使用方式上Spring Cloud略胜一筹。**

## **五、微服务架构组成以及注意事项**

到底使用是dubbo还是Spring Cloud其实并不重要，重点在于如何合理的利用微服务。下面是一张互联网通用的架构图,其中每个环节都是微服务的核心部分。

![img](http://mmbiz.qpic.cn/mmbiz_png/icdBCpcEToI87YOAYGvWVAYpTxEr0HFYPUEYa4lgYcczN8xHbHNDZxic8xLoYMCRicPXmuZ0FG1Z1Xia4dLYrJkaZg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

**（一）、架构分解**

- 网关集群：数据的聚合、实现对接入客户端的身份认证、防报文重放与防数据篡改、功能调用的业务鉴权、响应数据的脱敏、流量与并发控制等
- 业务集群：一般情况下移动端访问和浏览器访问的网关需要隔离，防止业务耦合
- Local Cache：由于客户端访问业务可能需要调用多个服务聚合，所以本地缓存有效的降低了服务调用的频次，同时也提示了访问速度。本地缓存一般使用自动过期方式，业务场景中允许有一定的数据延时。
- 服务层：原子服务层，实现基础的增删改查功能，如果需要依赖其他服务需要在Service层主动调用
- Remote Cache：访问DB前置一层分布式缓存，减少DB交互次数，提升系统的TPS
- DAL：数据访问层，如果单表数据量过大则需要通过DAL层做数据的分库分表处理。
- MQ：消息队列用来解耦服务之间的依赖，异步调用可以通过MQ的方式来执行
- 数据库主从：服务化过程中毕竟的阶段，用来提升系统的TPS

### **（二）注意事项**

- 服务启动方式建议使用jar方式启动，启动速度快，更容易监控
- 缓存、缓存、缓存，系统中能使用缓存的地方尽量使用缓存，通过合理的使用缓存可以有效的提高系统的TPS
- 服务拆分要合理，尽量避免因服务拆分而导致的服务循环依赖
- 合理的设置线程池，避免设置过大或者过小导致系统异常

## 六、总结

Dubbo出生于阿里系，是阿里巴巴服务化治理的核心框架，并被广泛应用于中国各互联网公司；只需要通过spring配置的方式即可完成服务化，对于应用无入侵。设计的目的还是服务于自身的业务为主。虽然阿里内部原因dubbo曾经一度暂停维护版本，但是框架本身的成熟度以及文档的完善程度，完全能满足各大互联网公司的业务需求。如果我们需要使用配置中心、分布式跟踪这些内容都需要自己去集成，这样无形中增加了使用  Dubbo 的难度。

Spring Cloud 是大名鼎鼎的 Spring 家族的产品， 专注于企业级开源框架的研发。 Spring Cloud 自从发展到现在，仍然在不断的高速发展，几乎考虑了服务治理的方方面面，开发起来非常的便利和简单。

# Eureka

**1、Eureka 简介：**

**Eureka 是 Netflix 出品的用于实现服务注册和发现的工具。 Spring Cloud 集成了 Eureka，并提供了开箱即用的支持。其中， Eureka 又可细分为 Eureka Server 和 Eureka Client。**

### 1.基本原理

上图是来自eureka的官方架构图，这是基于集群配置的eureka； 

- 处于不同节点的eureka通过Replicate进行数据同步 

- Application Service为服务提供者 

- Application Client为服务消费者 

- Make Remote Call完成一次服务调用

服务启动后向Eureka注册，Eureka Server会将注册信息向其他Eureka  Server进行同步，当服务消费者要调用服务提供者，则向服务注册中心获取服务提供者地址，然后会将服务提供者地址缓存在本地，下次再调用时，则直接从本地缓存中取，完成一次调用。

当服务注册中心Eureka Server检测到服务提供者因为宕机、网络原因不可用时，则在服务注册中心将服务置为`DOWN`状态，并把当前服务提供者状态向订阅者发布，订阅过的服务消费者更新本地缓存。

服务提供者在启动后，周期性（默认30秒）向Eureka Server发送心跳，以证明当前服务是可用状态。Eureka Server在一定的时间（默认90秒）未收到客户端的心跳，则认为服务宕机，注销该实例。

### 2. 自我保护机制

在自我保护模式中， Eurecka Server会保护服务注册表的信息，不再注销任何服务实例。当它收到的心跳数重新恢复到阈值以上时，该Eurecka Server节点就会自动退出自我保护模式。它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。一句话讲解：好死不如赖活着。

综上，自我保护模式是一种应对网络异常的安全保护措施。它的架构哲学是宁可同时保留所有微服务（健康的微服务和不健康的微服务都会保留），也不盲目注销任何健康的服务。使用自我保护模式，可以让Ｅｕｒｅｋａ集群更加的健壮、稳定.

在spring cloud中，可以使用eurecka.server.enable-self-preservation=false禁用自我保护模式。 

## ACID与CAP

RDBMS (mysql/orackle/sqlservier)     ＝＝＝＝> ACID

- A(Atomicity) 原子性
- C（Consistency)一致性
- I (Isolation)独立性
- D(Durability)持久性



NOSQL（redis/mongdb) ==>   CAP

- C(Consistency)强一致性

- A(Availability)可用性

- P(Partition tolerance) 分区容错性

  

  CP: MongoDB HBASE REDIS

  CA:RDBMS

  AP: Spring Cloud 

  

## 3. 作为服务注册中心，Eureka比Zookeeper好在哪里

著名的CAP理论指出，一个分布式系统不可能同时满足C(一致性)、A(可用性)和P(分区容错性)。由于分区容错性在是分布式系统中必须要保证的，因此我们只能在A和C之间进行权衡。在此Zookeeper保证的是CP, 而Eureka则是AP。

## 3.1 Zookeeper保证CP

当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的注册信息，但不能接受服务直接down掉不可用。也就是说，服务注册功能对可用性的要求要高于一致性。但是zk会出现这样一种情况，当master节点因为网络故障与其他节点失去联系时，剩余节点会重新进行leader选举。问题在于，选举leader的时间太长，30  ~ 120s,  且选举期间整个zk集群都是不可用的，这就导致在选举期间注册服务瘫痪。在云部署的环境下，因网络问题使得zk集群失去master节点是较大概率会发生的事，虽然服务能够最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。

## 3.2 Eureka保证AP

Eureka在设计时优先保证可用性。Eureka各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而Eureka的客户端在向某个Eureka注册或请求时如果发现连接失败，则会自动切换至其它节点，只要有一台Eureka还在，就能保证注册服务可用（保证可用性），只不过查到的信息可能不是最新的（不保证强一致性）。除此之外，Eureka还有一种自我保护机制，如果在15分钟内超过85％的节点都没有正常的心跳，那么Eureka就认客户端与注册中心出现了网络故障，此时会出现如下几种情况：

1. Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
2. Eureka仍然能够接收新服务的注册和查询请求，但是不会被同步到其它节点上（即保证当前节点依然可用）
3. 当网络稳定时，当前实例新的注册信息会被同步到其它节点中 

因此，Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像zookeeper那样使整个注册服务瘫痪。

# 基于Eureka的服务注册与发现实现

## 一、创建Eureka服务程

   1. 创建一个maven父项目，配置POM文件相关依赖,pakage为POM。

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <project xmlns="http://maven.apache.org/POM/4.0.0"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
          <modelVersion>4.0.0</modelVersion>
      
          <groupId>com.thr</groupId>
          <artifactId>scloud</artifactId>
          <version>1.0-SNAPSHOT</version>
          <modules>
              <module>../scloudCommon</module>
              <module>../scloudEureka</module>
              <module>../scloudUser</module>
              <module>../scloudShoping</module>
          </modules>
          <packaging>pom</packaging>
          <properties>
              <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
              <maven.compiler.source>1.8</maven.compiler.source>
              <maven.compiler.target>1.8</maven.compiler.target>
              <junit.version>4.12</junit.version>
              <log4j.version>1.2.17</log4j.version>
              <lombok.version>1.16.18</lombok.version>
          </properties>
          <dependencyManagement>
              <dependencies>
                  <dependency>
                      <groupId>org.springframework.cloud</groupId>
                      <artifactId>spring-cloud-dependencies</artifactId>
                      <version>Dalston.SR1</version>
                      <type>pom</type>
                      <scope>import</scope>
                  </dependency>
                  <dependency>
                      <groupId>org.springframework.boot</groupId>
                      <artifactId>spring-boot-dependencies</artifactId>
                      <version>1.5.9.RELEASE</version>
                      <type>pom</type>
                      <scope>import</scope>
                  </dependency>
                  <dependency>
                      <groupId>mysql</groupId>
                      <artifactId>mysql-connector-java</artifactId>
                      <version>5.0.4</version>
                  </dependency>
                  <dependency>
                      <groupId>org.mybatis.spring.boot</groupId>
                      <artifactId>mybatis-spring-boot-starter</artifactId>
                      <version>1.1.1</version>
                  </dependency>
                  <dependency>
                      <groupId>com.alibaba</groupId>
                      <artifactId>druid</artifactId>
                      <version>1.0.31</version>
                  </dependency>
                  <dependency>
                      <groupId>ch.qos.logback</groupId>
                      <artifactId>logback-core</artifactId>
                      <version>1.2.3</version>
                  </dependency>
                  <dependency>
                      <groupId>junit</groupId>
                      <artifactId>junit</artifactId>
                      <version>${junit.version}</version>
                      <scope>test</scope>
                  </dependency>
                  <dependency>
                      <groupId>log4j</groupId>
                      <artifactId>log4j</artifactId>
                      <version>${log4j.version}</version>
                  </dependency>
              </dependencies>
      
          </dependencyManagement>
          <build>
              <finalName>studycloud</finalName>
              <resources>
                  <resource>
                      <directory>src/main/resources</directory>
                      <filtering>true</filtering>
                  </resource>
              </resources>
              <plugins>
                  <plugin>
                      <groupId>org.springframework.plugins</groupId>
                      <artifactId>maven-resources-plugin</artifactId>
                      <configuration>
                          <delimiters>
                              <delimit>$</delimit>
                          </delimiters>
                      </configuration>
                  </plugin>
              </plugins>
          </build>
      
      </project>
      ```

   2. 创建MAVEN的Eureka服务项目，添加spring boot 启动依赖spring-boot-starter以及spring-cloud-eureka-server依赖

      ```
      <?xml version="1.0" encoding="UTF-8"?>
      <project xmlns="http://maven.apache.org/POM/4.0.0"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
          <parent>
              <artifactId>scloud</artifactId>
              <groupId>com.thr</groupId>
              <version>1.0-SNAPSHOT</version>
              <relativePath>../scloud/pom.xml</relativePath>
          </parent>
          <modelVersion>4.0.0</modelVersion>
      
          <artifactId>scloud-eureka</artifactId>
          <dependencies>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter</artifactId>
              </dependency>
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-starter-eureka-server</artifactId>
              </dependency>
          </dependencies>
      </project>
      ```

3. Eureka服务的yml配置

   ```yml
   eureka:
     client:
       fetch-registry: false  # false表示自己就是注册服务中心
       register-with-eureka: false # false表示不须向注册中心注册
       service-url:  # 服务地址配置
         defaultZone: http://eureka01:7001/eureka
     instance:  # 服务实例主机名
       hostname: eureka01
   server:
     port: 7001
   # 服务实例应用名
   spring:
     application:
       name: eureka
   ```

4. Eureka服务启动类添加@EnableEurekaServer注解

   ```java
   package com.thr.scloud;
   
   
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
   
   @SpringBootApplication
   @EnableEurekaServer
   public class ScloudEurekaApp {
   
       public static void main(String[] args) {
           SpringApplication.run(ScloudEurekaApp.class, args);
       }
   }
   ```

Eureka服务已经创建成功，浏览器访问结果

![1557909528292](/home/yuquanhong/.config/Typora/typora-user-images/1557909528292.png)





## 二、创建公共子模块

1. 创建公共部分子项目，用于共享接口及实体类，lombok可以减少getter,setter等模板代码的编写，POM如下，

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>scloud</artifactId>
        <groupId>com.thr</groupId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../scloud/pom.xml</relativePath>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>scloud-common</artifactId>
    <packaging>jar</packaging>
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
        </dependency>
    </dependencies>

</project>
```

2. 公共部分实体类

```java
package com.thr.scloud.entity;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.experimental.Accessors;

/**
 * 用户信息
 * @author  yu
 * @date
 */
@NoArgsConstructor
@Data
@Accessors(chain = true)
@AllArgsConstructor
public class User {
    private Long uid;
    private String username;
    private String password;
    private String email;
    private String dbSource;
}
```

3. 公共部分接口

```java
package com.thr.scloud.service;

import com.thr.scloud.entity.User;

import java.util.List;

public interface UserService {

    User get(Long uid);

    boolean add(User user);

    List<User> findAll();
}
```

## 三、创建服务注册

1. 服务注册类创建，pom中加入spring-cloud-eureka-client启动依赖，pom文件如下：

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>scloud</artifactId>
        <groupId>com.thr</groupId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../scloud/pom.xml</relativePath>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>scloud-user</artifactId>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>
        <dependency>
            <groupId>com.thr</groupId>
            <artifactId>scloud-common</artifactId>
            <version>${project.version}</version>
        </dependency>
    </dependencies>

</project>
```

2. 服务注册端的yml配置如下:

```yml
server:
  port: 8001

spring:
  datasource:
    url: jdbc:mysql://112.74.40.54:3306/scloud_user01??useUnicode=true&characterEncoding=utf8
    username: root
    password: root123
    type: com.alibaba.druid.pool.DruidDataSource
    dbcp2:
      min-idle: 5
      initial-size: 5
      max-total: 5
      max-wait-millis: 200
  application:
    name: scloud-user

eureka:
  instance:
    instance-id: scloud-user-01
  client:
    service-url:
      defaultZone: http://eureka01:7001/eureka
mybatis:
  mapper-locations: classpath:mapper/**/**.xml
  type-aliases-package: com.thr.scloud.entity
  configuration:
    mapUnderscoreToCamelCase: true
debug: true
```

3. mybatis相关映射及dao接口:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.thr.scloud.dao.UserDao">
    <insert id="save" parameterType="User" >
        insert into user(username,password,email,db_source)
        value (${username},${password},${email},DATABASE());
    </insert>

    <select id="findById" parameterType="Long" resultType="User">
        select uid,username,password,email,db_source
        from user where uid=#{uid}
    </select>
    <select id="findAll" resultType="User">
        select uid,username,password,email,db_source
        from user
    </select>
</mapper>
```

```java
package com.thr.scloud.dao;

import com.thr.scloud.entity.User;
import java.util.List;

public interface UserDao {

    User findById(Long uid);

    boolean save(User user);

    List<User> findAll();

}
```

4. Service实现层及控制层：

```java
package com.thr.scloud.service.impl;


import com.thr.scloud.dao.UserDao;
import com.thr.scloud.entity.User;
import com.thr.scloud.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @author yuquanhong
 */
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserDao userDao;
    @Override
    public User get(Long uid) {
        return userDao.findById(uid);
    }

    @Override
    public boolean add(User user) {
        return userDao.save(user);
    }

    @Override
    public List<User> findAll() {
        return userDao.findAll();
    }
}
```

```java
package com.thr.scloud.controller;

import com.thr.scloud.entity.User;
import com.thr.scloud.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping(value = "/user/get/{uid}", method = RequestMethod.GET)
    public User get(@PathVariable("uid") Long uid) {
        return userService.get(uid);
    }
}
```

5. 启动类加上@EnableEurekaClient注解标注是Eureka客户端，@MapperScanner指向的mybatis映射类

```java
package com.thr.scloud;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
@MapperScan(value = "com.thr.scloud.dao.**")
public class ScloudUserApp {

    public static void main(String[] args) {
        SpringApplication.run(ScloudUserApp.class, args);
    }

```

6. 服务注册端编写完毕，浏览器测试如下：

![1557911201326](/home/yuquanhong/.config/Typora/typora-user-images/1557911201326.png)

![1557911269711](/home/yuquanhong/.config/Typora/typora-user-images/1557911269711.png)

## 四、创建服务发现方

1. 创建服务发现端，pom如下，也是加入Eureka客户端依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>scloud</artifactId>
        <groupId>com.thr</groupId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../scloud/pom.xml</relativePath>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>scloud-shoping</artifactId>
    <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>
        <dependency>
            <groupId>com.thr</groupId>
            <artifactId>scloud-common</artifactId>
            <version>${project.version}</version>
        </dependency>
    </dependencies>

</project>
```

2. yml配置如下：

```yml
server:
  port: 9001


eureka:
  instance:
    instance-id: scloud-shoping01
  client:
    service-url:
      defaultZone: http://eureka01:7001/eureka

spring:
  application:
    name: shoping
debug: true
```

3. 消费主类可以注入DiscoveryClient来发现服务。

```java
package com.thr.scloud.controller;

import com.thr.scloud.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.HashMap;
import java.util.Map;

@RestController
public class ShoppingController {

    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    private DiscoveryClient discoveryClient;

    @RequestMapping(value = "/consumer/get/{cid}", method = RequestMethod.GET)
    public Map<String, User> consumer(@PathVariable("cid") Long id) {
        ServiceInstance serviceInstance = discoveryClient.getInstances("SCLOUD-USER").get(0);
        User user = restTemplate.getForObject(serviceInstance.getUri().toString() + "user/get/{uid}", User.class, id);
        Map<String, User> consumerMap = new HashMap<>(1);
        consumerMap.put("consumerInfo", user);
        return consumerMap;
    }
}
```

4. 消费控制类中RestTemplate需要一个配置类来创建,RestTemplate是一个封装的Restful风格的httpClient调用类

```
package com.thr.scloud.cfgs;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class BeanConfigs {

    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

}
```

5. 消费发现端的启动类如下：

```java
package com.thr.scloud;


import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;


@EnableEurekaClient
@SpringBootApplication
public class ScloudShopingApp {

    public static void main(String[] args) {
        SpringApplication.run(ScloudShopingApp.class, args);
    }
}
```

6. 启动消费发现端的项目,浏览器测试结果如下:

![1557912185034](/home/yuquanhong/.config/Typora/typora-user-images/1557912185034.png)

![1557912228529](/home/yuquanhong/.config/Typora/typora-user-images/1557912228529.png)

五、附加配置:显示info信息，及访问路么显示IP地址。需要添加配置maven插件用来获取应用信息。并且需要加入sprng-boot-starter-actuator来监控以获取info。

```yml
eureka:
  instance:
    instance-id: scloud-user-01
    prefer-ip-address: true #访问路径显示IP地址
  client:
    service-url:
      defaultZone: http://eureka01:7001/eureka

info:
  app.name: thr.scloud
  company.name: www.thr.com
  build.artifactId: ${project.artifactId}
  build.version: ${project.version}
```

* 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

* 父类pom添加：

```xml
<build>
    <finalName>studycloud</finalName>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
    <plugins>
        <plugin>
            <groupId>org.springframework.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <configuration>
                <delimiters>
                    <delimit>$</delimit>
                </delimiters>
            </configuration>
        </plugin>
    </plugins>
</build>
```

* 效果:

  ![1557914395236](/home/yuquanhong/.config/Typora/typora-user-images/1557914395236.png)

![1557914423524](/home/yuquanhong/.config/Typora/typora-user-images/1557914423524.png)

# Eureka集群搭建

## 服务端的集群

1. 添加spring-boot-maven-plugin插件配置，通过maven打包eureka服务项目。

2. 分别执行以下命令：

   java -jar scloud-eureka-1.0-SNAPSHOT.jar --server.port=7001 --eureka.client.service-url.defaultZone=http://eureka02:7002/eureka,http://eureka03:7003/eureka --eureka.instance.hostname=eureka01

   java -jar scloud-eureka-1.0-SNAPSHOT.jar --server.port=7002 --eureka.client.service-url.defaultZone=http://eureka01:7001/eureka,http://eureka03:7003/eureka --eureka.instance.hostname=eureka02

   java -jar scloud-eureka-1.0-SNAPSHOT.jar --server.port=7003 --eureka.client.service-url.defaultZone=http://eureka01:7001/eureka,http://eureka02:7002/eureka --eureka.instance.hostname=eureka03

   http://eureka01:7001/这样子就运行了三个eureka服务的集群。

   效果：

   ![1557925586029](/home/yuquanhong/.config/Typora/typora-user-images/1557925586029.png)

   

![1557925709007](/home/yuquanhong/.config/Typora/typora-user-images/1557925709007.png)



![1557925778274](/home/yuquanhong/.config/Typora/typora-user-images/1557925778274.png)



## 客户端的集群

1. 同样添加spring-boot-maven-plugin插件配置，通过maven打包eureka服务项目。**注意：maven打包的时候由于有模块依赖，会掉示打包失败，应先将父项目执行maven的install,然后再将子模块打包。**

2. 分别执行以下命令：

   java -jar scloud-user-1.0-SNAPSHOT.jar --server.port=8002 --eureka.instance.instance-id=scloud-user-02 --eureka.client.server-url.defaultZone=http://eureka01:7001/eureka,http://eureka02:7002/eureka,http://eureka03:7003/eureka

   java -jar scloud-user-1.0-SNAPSHOT.jar --server.port=8001 --eureka.instance.instance-id=scloud-user-01 --eureka.client.server-url.defaultZone=http://eureka01:7001/eureka,http://eureka02:7002/eureka,http://eureka03:7003/eureka

![1557931715254](/home/yuquanhong/.config/Typora/typora-user-images/1557931715254.png)

![1557931775677](/home/yuquanhong/.config/Typora/typora-user-images/1557931775677.png)

![1557931820848](/home/yuquanhong/.config/Typora/typora-user-images/1557931820848.png)

# Ribbon的负载均衡

* ribbon是客户端的负载均衡实现

## Ribbon 的服务发现实现

1. 使用Ribbon，需要先加入spring-cloud-starter-ribbon的启动依赖到POM文件

2. 服务发现方代码示例；

   ```java
   package com.thr.scloud.controller;
   
   import com.thr.scloud.entity.User;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.cloud.client.discovery.DiscoveryClient;
   import org.springframework.web.bind.annotation.PathVariable;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.web.client.RestTemplate;
   
   import java.util.HashMap;
   import java.util.Map;
   
   @RestController
   public class ShoppingController {
   
   
       private static final String USER_URL_PREFIX = "http://scloud-user/";
   
       @Autowired
       private RestTemplate restTemplate;
   
       @Autowired
       private DiscoveryClient discoveryClient;
   
       @RequestMapping(value = "/consumer/get/{cid}", method = RequestMethod.GET)
       public Map<String, User> consumer(@PathVariable("cid") Long id) {
           User user = restTemplate.getForObject(USER_URL_PREFIX + "user/get/{uid}", User.class, id);
           Map<String, User> consumerMap = new HashMap<>(1);
           consumerMap.put("consumerInfo", user);
           return consumerMap;
       }
   }
   ```

3. 服务调用方加入负载均衡注解

   ```
   package com.thr.scloud.cfgs;
   
   import org.springframework.cloud.client.loadbalancer.LoadBalanced;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.client.RestTemplate;
   
   @Configuration
   public class BeanConfigs {
   
       @LoadBalanced
       @Bean
       public RestTemplate restTemplate(){
           return new RestTemplate();
       }
   
   }
   ```

4. 建三个不同的库来区分不同的服务调用

   ```mysql
   CREATE DATABASE if not exists scloud_user01 charset utf8;
   
   USE scloud_user01;
   CREATE TABLE IF NOT EXISTS user(
                                    uid bigint(13) auto_increment primary key ,
                                    username varchar(250) not null ,
                                    password varchar(250) not null ,
                                    email varchar(250) not null ,
                                    db_source varchar(15)
   ) ;
   
   
   insert into user(username, password, email, db_source)
     value ('lisi','lisipwd','lisi@126.com',DATABASE()),
     ('zhansang','zhansang123','zhansang123@163.com',DATABASE()),
     ('wangwu','wangsupwd','wangwu@sina.com',DATABASE());
   
   
   CREATE DATABASE if not exists scloud_user02 charset utf8;
   
   USE scloud_user02;
   CREATE TABLE IF NOT EXISTS user(
                                    uid bigint(13) auto_increment primary key ,
                                    username varchar(250) not null ,
                                    password varchar(250) not null ,
                                    email varchar(250) not null ,
                                    db_source varchar(15)
   ) ;
   
   
   insert into user(username, password, email, db_source)
     value ('zhaoliu','zhaoliupwd','zhaoliu@126.com',DATABASE()),
     ('tianqi','tianqi123','tianqi123@163.com',DATABASE()),
     ('qianba','qianbapwd','qianba@sina.com',DATABASE());
   
   
   
   
   CREATE DATABASE if not exists scloud_user03 charset utf8;
   
   USE scloud_user03;
   CREATE TABLE IF NOT EXISTS user(
                                    uid bigint(13) auto_increment primary key ,
                                    username varchar(250) not null ,
                                    password varchar(250) not null ,
                                    email varchar(250) not null ,
                                    db_source varchar(15)
   ) ;
   
   
   insert into user(username, password, email, db_source)
     value ('liujiu','liujiuwd','liujiu@126.com',DATABASE()),
     ('yangshi','yangshi123','yangshi123@163.com',DATABASE()),
     ('guoshiyi','guoshiyipwd','guoshiyi@sina.com',DATABASE());
   ```

5. 以不同的端口，实例id，数据库路径启动三个提供方服务

6. 效果如图：

![1557935966063](/home/yuquanhong/.config/Typora/typora-user-images/1557935966063.png)

7. 启动服务发现方，效果

![1557973835389](/home/yuquanhong/.config/Typora/typora-user-images/1557973835389.png)

![1557973911610](/home/yuquanhong/.config/Typora/typora-user-images/1557973911610.png)

## Ribbon的负载均衡规则

* Ribbon 默认使用的是轮询的负截均衡

* Ribbon自带的负截均衡规则如下：

  1. RoundRobinRule (轮询)

  2. RandomRule（随机）

  3. AvailabilityFilteringRule（可用性过滤规则)

     会先过滤掉由于多次访问故障而处于断路跳闸状态的服务

     还有并发的连接数量超过阈值的服务，然后对剩余的服务列表按照轮询策略进行访问

  4. WeightedResponseTImeRule（访问时间权重规则)

     根据平均响应时间计算所有服务的权重，响应时间越快服务权重越大被选中的机率越大，刚启动时如果统计信息不足；则使用RoundRobinRule策略，等统计信息足够会切换到WeightedResponseTImeRule

  5. RetryRule (重试规则)

     先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取

  6. BestAvailableRule(高可用性规则，过滤无用，选最小并发)

     会先过滤掉由于多次访问故障而处于断路跳闸状态的服务，然后选择一个并发量最小的进行访问

  7. ZoneAvoidanceRule（区域回避，没有比重时，先用轮询，有了一定数量，按区域和可用性）

     默认规则，复合判断server所在区域的性能和server的可用性选择服务器

  ### 切换自带的负载均衡的实现

  1. 只要在config里面配置对应规则的Bean

  ```java
  @Bean
  public IRule myRule(){
      return new RandomRule();
  }
  ```

  ### 自定义负载均衡规则

  1. 在启动类上加入＠RibbonClient注解，name="微服实例id",configuration=自定义的负截均衡配置类

  ```java
  @RibbonClient(value = "scloud-user",configuration = MySelfRule.class)
  ```

  1. 自定义的配置类，不能放在spring注解的@ComponentScanner指定的包及子包下。

     ```java
     package com.thr.rule;
     
     import com.netflix.loadbalancer.IRule;
     import org.springframework.context.annotation.Bean;
     import org.springframework.context.annotation.Configuration;
     
     @Configuration
     public class MySelfRule {
     
         @Bean
         public IRule myRule(){
             return new MyLoadBalacedRule();
         }
     }
     ```

  2. 自定义负载均衡类

     ```
     package com.thr.rule;
     
     import com.netflix.client.config.IClientConfig;
     import com.netflix.loadbalancer.AbstractLoadBalancerRule;
     import com.netflix.loadbalancer.ILoadBalancer;
     import com.netflix.loadbalancer.Server;
     
     import java.util.List;
     import java.util.Random;
     
     
     public class MyLoadBalacedRule extends AbstractLoadBalancerRule {
     
     
         Random rand = new Random();
         @Override
         public void initWithNiwsConfig(IClientConfig iClientConfig) {
     
         }
     
         @SuppressWarnings({"RCN_REDUNDANT_NULLCHECK_OF_NULL_VALUE"})
         public Server choose(ILoadBalancer lb, Object key) {
             if (lb == null) {
                 return null;
             } else {
                 Server server = null;
     
                 while(server == null) {
                     if (Thread.interrupted()) {
                         return null;
                     }
                     //获取可用的服务列表
                     List<Server> upList = lb.getReachableServers();
                     //获取所有的服务列表
                     List<Server> allList = lb.getAllServers();
                     int serverCount = allList.size();
                     if (serverCount == 0) {
                         return null;
                     }
     
                     int index = this.rand.nextInt(serverCount);
                     server = (Server)upList.get(index);
                     //如果服务没有获取到，那就暂停一下，进入下一轮
                     if (server == null) {
                         Thread.yield();
                     } else {
                         //如果服务不是活的状态，也是暂停一下，进入下一轮
                         if (server.isAlive()) {
                             return server;
                         }
     
                         server = null;
                         Thread.yield();
                     }
                 }
     
                 return server;
             }
         }
     
         @Override
         public Server choose(Object key) {
             return this.choose(this.getLoadBalancer(), key);
         }
     
     }
     ```

  3. 启动项目就可以测试结果,效果略。

     * 切换负载均衡也可以使用如下方式

       springboot-h2.ribbon.NFLoadBalancerRuleClassName=com.netflix.loadbalancer.RandomRule
       其中springboot-h2为application的name，这样做是不是也行

     

# Feign 负载均衡

## Feign概述

* Feign是Netflix开发的声明式、模板化的HTTP客户端， Feign可以帮助我们更快捷、优雅地调用HTTP API。

  在Spring Cloud中，使用Feign非常简单——创建一个接口，并在接口上添加一些注解，代码就完成了。Feign支持多种注解，例如Feign自带的注解或者JAX-RS注解等。

  Spring Cloud对Feign进行了增强，使Feign支持了Spring MVC注解，并整合了Ribbon和Eureka，从而让Feign的使用更加方便。

  Spring Cloud Feign是基于Netflix feign实现，整合了Spring Cloud Ribbon和Spring Cloud Hystrix，除了提供这两者的强大功能外，还提供了一种声明式的Web服务客户端定义的方式。

  Spring Cloud Feign帮助我们定义和实现依赖服务接口的定义。在Spring Cloud feign的实现下，只需要创建一个接口并用注解方式配置它，即可完成服务提供方的接口绑定，简化了在使用Spring Cloud Ribbon时自行封装服务调用客户端的开发量。

  **Spring Cloud Feign具备可插拔的注解支持，支持Feign注解、JAX-RS注解和Spring MVC的注解。**

  Feign是一个声明式的web服务端，使得编写web服务客户端很容易，只需要创建一个接口，然后在上面添国注解即可。

  

## Feign 的基本使用

1. 在pom中添加Feign启动依赖 spring-cloud-starter-feign;

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-feign</artifactId>
   </dependency>
   ```

2. 在接口中添加@FeignClient(name="scloud-user")注解，并给接口添加请求映射.

   ```java
   package com.thr.scloud.service;
   
   import com.thr.scloud.entity.User;
   import org.springframework.cloud.netflix.feign.FeignClient;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   
   import java.util.List;
   
   @FeignClient(name="scloud-user")
   public interface UserService {
   
       @RequestMapping(value = "/user/get/id", method = RequestMethod.GET)
       User get(Long uid);
   
       @RequestMapping(value = "/user/add", method = RequestMethod.POST)
       boolean add(User user);
   
       @RequestMapping(value = "/user/list", method = RequestMethod.GET)
       List<User> findAll();
   
   }
   ```

3. 在控制层通过注入服务对象，可以直接调用服务。

   ```java
   package com.thr.scloud.controller.feign;
   
   import com.thr.scloud.entity.User;
   import com.thr.scloud.service.UserService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.PathVariable;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   
   import java.util.HashMap;
   import java.util.Map;
   @Controller
   public class ShoppingFeignController {
   
       @Autowired
       private UserService userService;
   
       @RequestMapping(value = "/consumer/get/{cid}", method = RequestMethod.GET)
       public Map<String, User> consumer(@PathVariable("cid") Long id) {
           Map<String, User> consumerMap = new HashMap<>(1);
           consumerMap.put("consumerInfo", userService.get(id));
           return consumerMap;
       }
   
   }
   ```

4. 需要在启动类上添加@EnableFeignClients(basePackages = {"com.thr.scloud.controller.feign"})注解来表明，哪些类需要通过feign来调用接口。

   ```java
   package com.thr.scloud;
   
   
   import com.thr.rule.MySelfRule;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
   import org.springframework.cloud.netflix.feign.EnableFeignClients;
   import org.springframework.cloud.netflix.ribbon.RibbonClient;
   
   @RibbonClient(value = "scloud-user",configuration = MySelfRule.class)
   @EnableEurekaClient
   @SpringBootApplication
   @EnableFeignClients(basePackages = {"com.thr.scloud.controller.feign"})
   public class ScloudShopingApp {
   
       public static void main(String[] args) {
           SpringApplication.run(ScloudShopingApp.class, args);
       }
   }
   ```

6. 基本操作，已完成，可以启动服务进行测试，效果略.

* Feign集成了Ribbon,所以使用Feign默认也是用的Ribbon的轮询规则来负载均衡。

  

# Netflix Hystrix断路器简介与工作原理

## 一、前言

### 1、Netflix Hystrix断路器是什么？

[Netflix Hystrix](https://github.com/Netflix/Hystrix)是SOA/微服务架构中提供服务隔离、熔断、降级机制的工具/框架。Netflix Hystrix是断路器的一种实现，用于高微服务架构的可用性，是防止服务出现雪崩的利器。

### 2、为什么需要断路器？

在分布式架构中，一个应用依赖多个服务是非常常见的，如果其中一个依赖由于延迟过高发生阻塞，调用该依赖服务的线程就会阻塞，如果相关业务的QPS较高，就可能产生大量阻塞，从而导致该应用/服务由于服务器资源被耗尽而拖垮。

另外，故障也会在应用之间传递，如果故障服务的上游依赖较多，可能会引起服务的雪崩效应。就跟数据瘫痪，会引起依赖该数据库的应用瘫痪是一样的道理。

------

当一个应用依赖多个外部服务，一切都正常的情况下，如下图：

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/lks15b9w7d.png?imageView2/2/w/1620)

如果其中一个依赖发生延迟，当前请求就会被阻塞

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/8pp4tfv21x.png?imageView2/2/w/1620)

出现这种情况后，如果没有应对措施，后续的请求也会被持续阻塞

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/r58toij7z6.png?imageView2/2/w/1620)

每个请求都占用了系统的CPU、内存、网络等资源，如果该应用的QPS较高，那么该应用所以的服务资源会被快速消耗完毕，直至应用死掉。如果这个出问题的依赖（Dependency I），不止这一个应用，亦或是受影响的应用上层也有更多的依赖，那就会带来我们前面所提到的服务雪崩效应。

所以，为了应对以上问题，就需要有支持服务隔离、熔断等操作的工具

## 二、Hystrix 简介

### 1、Hystrix具备哪些能力/优点？

- 在通过网络依赖服务出现高延迟或者失败时，为系统提供保护和控制
- 可以进行快速失败，缩短延迟等待时间和快速恢复：当异常的依赖回复正常后，失败的请求所占用的线程会被快速清理，不需要额外等待
- 提供失败回退（Fallback）和相对优雅的服务降级机制
- 提供有效的服务容错监控、报警和运维控制手段

### 2、Hystrix 如何解决级联故障/防止服务雪崩？

- Hystrix将请求的逻辑进行封装，相关逻辑会在独立的线程中执行  
- Hystrix有自动超时策略，如果外部请求超过阈值，Hystrix会以超时来处理  
- Hystrix会为每个依赖维护一个线程池，当线程满载，不会进行线程排队，会直接终止操作
- Hystrix有熔断机制： 在依赖服务失效比例超过阈值时，手动或者自动地切断服务一段时间

所以，当引入了Hystrix之后，当出现某个依赖高延迟的时候：

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/jm2z0red7w.png?imageView2/2/w/1620)

## 三、Hystrix 工作原理

### 1、Hystrix工作流

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/zyu4m37uqm.png?imageView2/2/w/1620)

- 1、创建HystrixCommand 或者 HystrixObservableCommand 对象
- 2、执行命令execute()、queue()、observe()、toObservable()
- 3、如果请求结果缓存这个特性被启用，并且缓存命中，则缓存的回应会立即通过一个Observable对象的形式返回
- 4、检查熔断器状态，确定请求线路是否是开路，如果请求线路是开路，Hystrix将不会执行这个命令，而是直接执行getFallback
- 5、如果和当前需要执行的命令相关联的线程池和请求队列，Hystrix将不会执行这个命令，而是直接执行getFallback
- 6、执行HystrixCommand.run()或HystrixObservableCommand.construct()，如果这两个方法执行超时或者执行失败，则执行getFallback()
- 7、Hystrix 会将请求成功，失败，被拒绝或超时信息报告给熔断器，熔断器维护一些用于统计数据用的计数器。

这些计数器产生的统计数据使得熔断器在特定的时刻，能短路某个依赖服务的后续请求，直到恢复期结束，若恢复期结束根据统计数据熔断器判定线路仍然未恢复健康，熔断器会再次关闭线路。

### 依赖隔离

Hystrix采用舱壁隔离模式隔离相互之间的依赖关系，并限制对其中任何一个的并发访问。

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/u6x0r9qbli.png?imageView2/2/w/1620)

线程&线程池

客户端（通常指Web应用）通过网络请求依赖时，Hystrix会将请求外部依赖的线程与会将App容器(Tomcat/Jetty/…)线程隔离开，以免请求依赖出现延迟时影响请求线程。

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/7uhkujhgsv.png?imageView2/2/w/1620)

Hystrix会为每个依赖维护一个线程池，当线程满载，不会进行线程排队，会Return fallback或者抛出异常

![img](https://ask.qcloudimg.com/http-save/yehe-1381082/dhc06clrv9.png?imageView2/2/w/1620)

可能会有人有疑问，为什么不依赖于HTTP Client去做容错保护（快速失败、熔断等），而是在访问依赖之外通过线程&线程池隔离的方式做这个断路器（Hystrix）。

主要是以下几个方面：

1. 不同的依赖执行的频率不同，需要分开来对待
2. 不同的依赖可能需要不同的Client的工具/协议来访问，比如我们可能用HTTP Client，可能用Thrift Client。
3. Client在执行的过程中也可能会出现非网络异常，这些都应该被隔离
4. Client的变化会引起断路器的变化

所以，Hystrix这样设计的好处是：

1. 断路器功能与不同的Client Library隔离
2. 不同依赖之间的访问互不影响
3. 当发生大量异常时，不会造成App Container的响应线程排队，并且当异常的依赖恢复正常后，失败的请求所占用的线程会被快速清理，不需要额外等待
4. 为不支持异步的依赖提供了异步的可能

这样做的成本是，多了一些线程上的资源消耗（排队，调度和上下文切换），不过从官方给到的数据上可能，这个消耗完全可以接受。目前Netflix每天有100亿+的Hystrix命令执行，平均每个应用实例都有40+个线程池。每个线程池有5-20个线程 依然运行良好（不过这里 ken.io 不得不吐槽下，官方没有透露单个实例硬件配置） 

官方给了一组测试数据，在单个应用实例60QPS，且每秒钟有350个Hystix子线程（350次Hystrix Command执行）的情况下。Hystrix的线程成本通常为0-3ms，如果CPU使用率超过90%，这个线程成本为有所上升约为9ms。相对于网络请求的时间消耗，这个成本完全可以接受。

## 服务熔断实现

在服务方添加spring-cloud-starter-hystrix 启动依赖。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```

在方法上添加＠HystrixCommand(fallbackMethod="xxxFallback")的注解，并添加xxxFallback注解用在程序异常时返回。

```
package com.thr.scloud.controller;

import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.thr.scloud.entity.User;
import com.thr.scloud.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @Autowired
    private UserService userService;

    @HystrixCommand(fallbackMethod = "getFallback")
    @RequestMapping(value = "/user/get/{uid}", method = RequestMethod.GET)
    public User get(@PathVariable("uid") Long uid) {
        return userService.get(uid);
    }

    public User getFallback() {
        return new User(0L, "unkonw", "unknowpwd", "unknow@xxx.com","unknowdb");
    }

}
```

在Spring启动类上添加＠EnableCircleBroker启动服务熔断。

```java
package com.thr.scloud;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@EnableCircuitBreaker
@SpringBootApplication
@EnableEurekaClient
@MapperScan(value = "com.thr.scloud.dao.**")
public class ScloudUserApp {

    public static void main(String[] args) {
        SpringApplication.run(ScloudUserApp.class, args);
    }
}
```

## 服务降级实现

1. 避免太多@HystrixCommand(fallbackMethod=""),可以使用@FeignClient(name="scloud-user",fallbackFactory =UserServiceFallbackFactory.class )

```java
package com.thr.scloud.service;

import com.thr.scloud.entity.User;
import org.springframework.cloud.netflix.feign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.util.List;

@FeignClient(name="scloud-user",fallbackFactory = UserServiceFallbackFactory.class )
public interface UserService {

    @RequestMapping(value = "/user/get/id", method = RequestMethod.GET)
    User get(Long uid);

    @RequestMapping(value = "/user/add", method = RequestMethod.POST)
    boolean add(User user);

    @RequestMapping(value = "/user/list", method = RequestMethod.GET)
    List<User> findAll();

}
```

2. UserServiceFallbackFactory 是一个实现了FallbackFactory的类.

```java
import com.thr.scloud.entity.User;
import feign.hystrix.FallbackFactory;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
public class UserServiceFallbackFactory implements FallbackFactory<UserService> {


    @Override
    public UserService create(Throwable throwable) {
        return new UserService() {
            @Override
            public User get(Long uid) {
                return null;
            }

            @Override
            public boolean add(User user) {
                return false;
            }

            @Override
            public List<User> findAll() {
                return null;
            }
        };
    }
}
```

## 

3. 在服务调用端使用hystrix进行服务降级，需要在调用端POM文件中添加

spring-cloud-starter-hystrix的启动依赖。

4. 需要在调用方的Yml文件中加入如下配置,开启feign的fallback:

```yml
feign:
  hystrix:
    enabled: true
```

## HystrixDashboard 的可视化监控

1. 使用hystrixDashboard首先需要在服务端添加spring-boot-actuator来监控反馈自己的状态。
2. 新建一个hystrixDashboard的项目，POM中添加spring-cloud-starter-hystrix和spring-cloud-starter-hystrix-dashboard启动类
3. 启动类中添加＠EnabledHystrixDashboard注解。
4. yml中设置dashboard端口
5.  访问http://hostname:dashboard端口/hystrix来测试 
6. 在dashboard界面添加 http//服务端域名:服务端口/hystrix_stream



# Zuul 服务网关

### ZUUL 的基本使用

1. POM中添加 spring-cloud-starter-eureka以及spring-cloud-starter-zuul的启动依赖。
2. yml文件配置和eureka客户端一样
3. 主启动类加入＠EnableZuulProxy的注解。
4.  启动后对服务的使用可以用。 http://网关/微服务/请求路径
5. 微服务名称映射

```yml
zuul:
  prefix: /aa
  routes:
    mydept.serviceId: dddd # 这两行是映射配置
    mydept.path: sss/**     
  ignored-services: dddd  # 值为*代表所有服务 忽略真实服务名称
```



### 分布式服务配置中心

新建github上的一个仓库

获得仓库的ssh协议的git地址

在本地硬盘目录上新建一个Git仓库，并clone到本地

在本地仓库中新建yml ,并推送到github上

新建模块作为配置中心，POM中添加cloud-starter-config-server org.eclipse.jgit

yml配置spring.cloud.config.server.git

```yaml
spring:
 cloud:
   config:
    server:
      git:
        url: git@github.com:zzzyybs/microservicercloud-config.git
```

启动类上添加@EnableConfigServer

访问http://映射域名:配置中心端口/application.yml

配置中心客户端 ,POM配置 clound-config

资源路径中添加bootstrap.yml, bootstrap.yml是系统级的，优先级更高

```yaml
spring:
     cloud:
       config:
         name: ddddd #需要从github上读取的资源名称，注意没有yml后缀名
         profile: dev
         label: master
         url: bbbb #配置中记访问路径
```



