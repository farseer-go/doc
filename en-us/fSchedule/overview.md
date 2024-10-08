# FSchedule概述
调度中心，是一款支持分布式定时任务调度，支持几千个任务同时调度。有着高性能、高可用（集群）、低延迟（0ms延迟）、低内存（80m以内）等特点。

FSchedule运行的是调度逻辑，到达时间节点时，会通知你的应用程序执行任务。

它有分布式、高可用、解耦任务执行与调度逻辑、弹性伸缩、跨语言、一致性、数据分片执行、集群模式、广播模式、分布式日志等特性。

Server端依赖Redis、Mysql，通常使用Docker运行（支持集群HA），部署完后我们一般不需要再去维护。

客户端，就是我们写的应用程序，通过官方SDK集成到你的应用，比如我需要凌晨2点执行数据库清理操作，在接入后就能按计划执行任务了。

## 为什么需要分布式？
在传统的本地任务执行，无法做到高可用、一致性，并且调度与任务执行在同一个进程中。

我们的应用为了高可用，通常运行2个及以上的实例（进程），在传统的定时任务中，同一时刻这些实例都会同时运行。（但实际我们只需要运行一次就可以了）

如果在A应用程序执行失败或程序崩溃，我们还希望能自动切换到B应用程序重新执行。这些都是本地任务无法做到的。

FSchedule将调度、控制权放到服务端，由服务端统一调度。可以做到统一管理的效果，并且它是高性能、高可用的。

## 主从节点职责
在多个节点中选举出1个master节点，其余的为Slave节点，master节点也会参与任务调度。

当master节点下线后，其它节点会选举出新的master节点。

> 同一个任务组，只会在集群的一个节点中运行，节点下线后，会自动转移到其它节点，继续提供服务。

### master节点
1. 同步任务组、任务数据
2. 检查客户端状态
3. 检查集群各节点状态
4. 计算任务组的平均耗时
5. 自动清除历史任务记录

## 架构设计
![架构设计](https://farseer-go.github.io/doc/fSchedule/images/1.png)

FSchedule采用go语言设计，编译出来后就是一个二进制的可执行文件（约6.91mb），所以它非常轻巧，且占用资源非常低。

2.0是一个全新的架构设计，学习了1.x的经验后，总结而来。

2.0采用订阅发布的方式来代替1.x的轮循机制，用于共享集群节点之间的数据，同时延用1.x的事件驱动机制，让各个执行单元更加独立。

2.0的客户端改成http协议来代替轮循拉取的方式，以此让服务端可以直接与客户端通讯

2.0改为动态任务组模式，在1.x版本的时候需要到管理端配置。而在新的版本中，可直接在客户端做参数配置，动态创建任务组。

得益于go的协程能力，在2.0中100个并发同时调度，做到0延迟了，且内存控制到80m左右。

## 特性
1. [x] `动态任务组`：支持动态创建任务组，客户端上线时动态注册任务组。
2. [x] `集群部署`：服务端采用主从节点部署，保证集群可用性。
3. [x] `客户端HA`：客户端支持集群部署，保证所有任务的HA。
4. [x] `调度解耦`：服务端只部署一次，调度器与任务执行分离，调度器由服务端提供，任务的执行在客户端自行实现，相互之间没有耦合。
5. [x] `弹性伸缩`：客户端每次上下线时，调度器将重新计算分配任务。
6. [x] `少依赖`：服务端只依赖redis、数据库，不需要依赖zk、etcd。
7. [x] `时间轮算法`：得益于时间轮算法，为几千个任务同时运行时，极大降低使用内存。
8. [x] `官方SDK`：官方提供go、c#客户端，可快速接入服务端。
9. [x] `跨语言支持`：提供了http协议，其它语言客户端可自行接入。
10. [x] `故障转移`：客户端异常退出后，进行中的任务将转移到可用的客户端节点，重新运行。
11. [x] `一致性`：集群模式保证只会调度一次到客户端。广播模式保证每个客户端只会运行一次。
12. [x] `自定义参数`：支持对任务组配置参数，客户端运行期间也可以动态更新参数。
13. [x] `Docker支持`：官方提供Docker镜像部署。
14. [x] `高性能`：每个任务组将单独分配一个协程，并利用多个通道实时通知处理。 
15. [x] `任务版本号`：任务组有版本号属性，新版本号覆盖旧版本号。同时旧版本不再被调度。 
16. [x] `任务集群模式`：同一个任务执行将只调度到其中一个客户端执行。
17. [ ] `任务广播模式`：同一个任务将调度到所有可用的客户端上执行。
18. [ ] `数据分片`：任务可根据当前客户端数量自动分片到所有客户端，每个客户端只处理一部份数据，做到并行处理。
19. [ ] `任务依赖`：可以设置任务执行前、执行后时运行依赖的任务。
20. [x] `动态更新计划`：支持客户端更新下次执行计划时间。
21. [ ] `分布式日志`：支持日志上传到集群，统一查看。

> 未打勾的，在将来的版本中支持。

## 历史回顾
1. `2023-03-02` 发布2.0版本
2. `2023-01-24` github创建仓库