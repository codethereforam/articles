# Java系统和PHP系统相互调用

## 一、HTTP JSON方式的缺点
1. JSON序列化效率低
2. 多语言服务治理功能低

## 二、关于RPC框架
RPC 框架大致分为两类，一种是偏重服务治理，另一种侧重跨语言调用
### 2.1 服务治理型
#### 特点
功能丰富，提供高性能的远程调用、服务发现及服务治理能力，适用于大型服务的服务解耦及服务治理，对于特定语言(Java)的项目可以实现透明化接入。缺点是语言耦合度较高，跨语言支持难度较大。
#### 常用框架
- Dubbo
- Motan
### 2.2 跨语言调用型
#### 特点
侧重于服务的跨语言调用，能够支持大部分的语言进行语言无关的调用，非常适合多语言调用场景。但这类框架没有服务发现相关机制，实际使用时需要代理层进行请求转发和负载均衡策略控制。
#### 常用框架
- Thrift 
- gRPC

## 三、如何提供服务给PHP系统
### Dubbo(阿里开源)
#### 3.1 Dubbo + [dubbo-php-framework](https://github.com/dubbo/dubbo-php-framework/wiki)
##### 特点
可以实现php调用php，php调用java(dubbo)，java(dubbo)调用php，Java调Java，真正的跨语言
##### 缺点
开发活跃程度极低，社区小，资料少，没有release版本
#### 3.2 Dubbo Restful（合并自Dubbox）
##### 特点
显著简化企业内部的异构系统之间的（跨语言）调用。

服务消费场景：
1. 非dubbo的消费端调用dubbo的REST服务（non-dubbo --> dubbo）
2. dubbo消费端调用dubbo的REST服务 （dubbo --> dubbo）
3. dubbo的消费端调用非dubbo的REST服务 （dubbo --> non-dubbo）
##### 问题
1. Dubbo REST的服务能和Dubbo注册中心、监控中心集成吗？
    > 可以的，而且是自动集成的，也就是你在dubbo中开发的所有REST服务都会自动注册到服务册中心和监控中心，可以通过它们做管理。  
    但是，只有当REST的消费端也是基于dubbo的时候，注册中心中的许多服务治理操作才能完全起作用。而如果消费端是非dubbo的，自然不受注册中心管理，所以其中很多操作是不会对消费端起作用的。
1. Dubbo REST中如何实现负载均衡和容错（failover）？
    > 如果dubbo REST的消费端也是dubbo的，则Dubbo REST和其他dubbo远程调用协议基本完全一样，由dubbo框架透明的在消费端做load balance、failover等等。  
    > 如果dubbo REST的消费端是非dubbo的，甚至是非java的，则最好配置服务提供端的软负载均衡机制，目前可考虑用LVS、HAProxy、 Nginx等等对HTTP请求做负载均衡。
### montan（微博开源）
#### 3.3 motan + [motan-php](https://github.com/weibocom/motan-php)
Java系统使用motan提供服务，php系统使用Motan-PHP（PHP client）调用服务。
##### 特点
motan暂不支持以服务注入方式引用yar 服务（使用motan作为yar client）。
目前yar协议主要解决java与php互通的问题，当java作为服务端，php作为client端，php可以在不做任何修改的情况下通过yar 协议使用java的服务。
而使用php做server、java做client的场景下（一般这种场景比较少），php的yar server不支持注册服务等功能，所以java作为client 时也没有必要通过motan调用服务。yarclient是个单独的小模块，是个类似httpclient这样的简易客户端，只能通过域名或ip访问yar服务。
##### 缺点
只支持PHP系统调用Java系统，没有stable release版本，文档很少，社区小，开发活跃程度低。整体不如Dubbo+dubbo-php-framework方式 
#### 3.4 [motan Restful](https://github.com/weibocom/motan/wiki/zh_quickstart#%E4%BD%BF%E7%94%A8restful%E5%8D%8F%E8%AE%AE)
模仿Dubbo Restful，很类似，从文档上对比，不如Dubbo Restful
#### 3.5 Client + Agent(还未完全开源)
大概是使用motan + motan-go(agent)等实现。朝着weibo mesh的服务化方向，将 Motan Agent定位为统一服务管理的执行节点。
#### 实现方式
为不同语言提供非常轻量的 Client，只实现与 Agent 的交互和必要的请求序列化能力；提供一个 local 的 Agent 代理，能够提供不同 RPC 协议交互能力，以及统一的服务治理能力与标准，服务治理的绝大部分功能都在语言无关的 Agent 中实现。通过使用 Agent，可以为类似 PHP 这样的解释型语言提供长链接复用能力，提高请求性能。

## 四、跨语言服务化
阿里Dubbo和微博motan都称自己框架有多语言服务化功能，都在开发中，都还不够成熟
### 4.1 挑战
#### HTTP RESTful API
HTTP 本身就是语言无关的协议，一般由服务提供方与使用方通过文档来进行服务约定。这也是目前使用非常广泛的跨语言交互方式，HTTP服务的服务治理能力一般依赖于代理层和服务调用方自己实现，一般代理服务需要单独部署，所有请求流量经由代理服务进行转发。例如使用 nginx 提供基础的负载均衡与流量控制等治理能力就是最简单的跨语言服务治理方案。

HTTP的跨语言服务化，有些方案选择了为特定语言实现强大服务治理能力，对其他语言提供兼容能力，例如Spring Cloud为Java 服务提供完整的服务治理能力，包括服务发现、路由器、过滤器、配置管理、熔断器等等。但很难为不同语言提供统一的服务治理能力，特别是在 Client 侧的一些服务治理能力，不同语言都需要做相同功能的开发。

还有一些方案使用本机代理的方式，例如envoy、linkerd等可以部署到单机的 HTTP 代理服务，在代理服务中集成统一服务发现与治理能力。这种方案能够做到不同语言的Client和 Server 都提供相同的服务治理能力，但服务治理的功能很难做到前一种方案那么强大。
#### RPC
使用 RPC 作为服务调用方式时，跨语言与服务治理也很难同时满足，对于跨语言型的 RPC 协议，例如 gRPC 等，都能够提供不同语言交互的能力，但是相关的服务治理功能还没有那么完善，做到了’交互’，但还无法做到’治理’。使用跨语言型 RPC 框架时一般会选择使用 HTTP（HTTP2）作为通信协议，这样可以直接应用 HTTP 方式的服务治理功能；

对于服务治理型RPC，由于选择了在 Client 端进行服务发现与治理，如果要实现不同语言统一的服务治理能力，需要在不同语言的一侧都实现相同的功能。实现起来也相当的困难。因此大部分服务治理型 RPC 专注与为特定语言提供完善的治理能力。
### 4.2 方式
#### 1. 兼容HTTP协议，增加HTTP RESTful协议
例如Dubbo Restful和motan Restful。这种方案将 Java 语言的 RPC 调用方式与其他语言独立开来，只针对 Java Server 端提供，没有为其他语言提供服务治理能力。由于只需要做 Java 协议的开发，这种方案研发和维护成本很低，不过能够提供的统一服务治理能力就很低了。
#### 2. 为不同语言提供RPC模块
例如Dubbo + dubbo-php-framework方式。这种方案能做到最贴近不同语言的使用习惯，可以为不同语言提供完全一致的服务治理能力，不论是作为 Server 端还是 Client 端。但这种方式也是成本最高的，包括开发不同语言的版本，以及后续的功能升级与版本维护代价都非常高。
#### 3. 开发一个 Agent 模块，实现绝大部分的服务治理能力
例如motan Client + Agent方式。Agent在不同服务的Server或Client侧运行。然后为不同语言开发一个很轻量的 Client 与 Agent 进行交互，由于 Client 只实现必要的交互功能，降低了不同语言版本的开发量与维护的成本。

## 五、可考虑的方案
1. （如果是JSON效率低）考虑使用Google Protocol Buffers, thrift, Hessian等更高效序列化方式。
2. （如果是服务治理功能低）保持原来HTTP+JSON方式不变，优化服务治理功能
3. 自己基于已有开源框架二次开发(参考motan Client + Agent思路)
4. 使用Dubbo Restful

## 六、参考资料
1. [微博轻量级RPC框架Motan正式开源：支撑千亿调用](http://tech.sina.com.cn/i/2016-05-10/doc-ifxryhhh1869879.shtml)
2. [微博开源的Motan RPC最新进展：新增跨语言及服务治理支持](http://www.sohu.com/a/161928428_268033)
3. [跨语言统一治理、Golang，谈谈另辟蹊径的开源RPC框架Motan](http://www.voidcn.com/article/p-okyatytj-brh.html)
4. [在Dubbo中开发REST风格的远程调用（RESTful Remoting）](https://dangdangdotcom.github.io/dubbox/rest.html)
5. https://github.com/weibocom/motan/issues/186#issuecomment-243055131
6. [跨语言统一治理、Golang，谈谈另辟蹊径的开源RPC框架Motan](http://www.voidcn.com/article/p-okyatytj-brh.html)

## 七、其他相关资料
1. [贝聊系统架构服务化之路](https://juejin.im/post/5a42035cf265da430406df49)
2. [逆天：蘑菇街下单平台演进，从PHP到Java](https://yq.aliyun.com/articles/55696)
3. [服务化框架技术选型实践](https://www.ctolib.com/topics-108902.html)
4. [饿了么分布式服务治理及优化经验](http://www.yunweipai.com/archives/9018.html)
5. [微博平台的RPC服务化实践](http://elf8848.iteye.com/blog/2066581)
6. [多语言支持](https://github.com/apache/incubator-dubbo/issues/848)
