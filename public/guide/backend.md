# 心动网络游戏后端架构 Guide Line
#####_2015-3版_

本文是心动对于网络游戏后端设计的要求和 Guide Line 意见，供心动内和心动关联项目参考。

本 Guide Line 中的意见分为“推荐”、“强烈推荐”、“要求”、“强烈要求”、“不推荐”、“强烈不推荐” 六个优先级别，大部分不是强制性的，但是非常建议开发侧尽量多的考虑，以便保证业务上线过程稳定高效。

注：本意见内容会不定期更新，建议随时关注最新版本


## 一、强烈要求

* 使用Docker作为开发和部署方案的基础。基于Docker方案可以大幅降低成本。心动会推动2015年底前所有现存项目和新项目都使用基于Docker的方案进行部署。

## 二、要求

* 客户端与服务端之间的通讯内容有加密
* 客户端与服务端之间的通讯协议能够防止重发（例如加入类似nonce参数）
* 整体架构对公网暴露的IP控制在2-5个（可以自己开发网关，也可以使用负载均衡通用件例如HAProxy）
* 后端游戏逻辑处理进程如果自建内存数据库，数据库事务要能保证[ACID](https://zh.wikipedia.org/wiki/ACID)即原子性、一致性、隔离性、持久性
* 使用64位编译
* 日志系统同时使用fluentd（接入心动日志平台）
* 默认服支持在多组服中随机或轮询


## 三、强烈推荐

* 整个架构中无单点故障源，或单点故障不影响全局用户
* SQL语句有缓写机制（先写入本地存储落地，后写入数据库）
* 使用MySQL或MariaDB做数据固化存储的主要方案
* 使用Redis做为高IO内存数据库方案（如有需求）


## 四、推荐

* 推荐使用Golang
* 推荐使用数据模型驱动的开发模式（Model-driven），根据通讯协议Schema同步生成前端和后端代码，保证前端和后端协议的高一致性
* 通讯协议推荐使用 Cap'n Proto （高性能） 或 Protocol Buffers（高通用性）
* 有Profile机制可以跟踪性能瓶颈
* 有Dump机制可以分析Crash故障点
* 有机制可以发现Racing Condition
* 游戏进程可平行扩展，支持运维根据压力自动部署扩张游戏服最好
* 区服配置平台化中心化，对多区多服的架构，开服仅需修改平台参数，而不需要逐服配置
* 有提供中心化接口在被调用时会回报各模块的健康状态


## 五、不推荐

* 不推荐使用memcache （该类需求建议使用redis）
* 不推荐过于复杂的架构方案（例如超过2种数据库引擎，或超过3种进程提供服务）

## 六、强烈不推荐

* 强烈不推荐使用memcache和redis中的全局锁例如cas的指令或类似功能（d）
