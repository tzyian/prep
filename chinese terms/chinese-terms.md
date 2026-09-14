See also: https://github.com/zhuohongwei/chinese-technical-terms

## Git

| 代码      | code                 |
| ------- | -------------------- |
| 代码库     | repository           |
| 源码      | source code          |
| 分支      | git branch           |
| 冲突      | conflict             |
| 合(并)    | merge                |
| 提(交)    | commit               |
| 推(送)    | push to branch       |
| 拉(取)    | pull from branch     |
| 版本      | version              |
| 主干      | main / master branch |
| 暂存      | stash / stage        |
| 樱桃采摘    | cherry-pick          |
| 变基      | rebase               |


# Code fundamentals 

| 变量  | variables  |
| --- | ---------- |
| 参数  | parameter  |
| 字段  | field      |
| 常量  | constant   |
| 函数  | function   |
| 方式  | method     |
| 指针  | pointer    |
| 引用  | reference  |
| 可变  | mutable    |
| 不可变 | immutable  |
| 作用域 | scope      |
| 闭包  | closure    |
| 断言  | assertion  |
| 赋值  | assignment |
| 递归  | recursion  |

## containers

| chinese | english                  |
| ------- | ------------------------ |
| 镜像      | image                    |
| 容器      | container                |
| 主机      | host                     |
| 节点      | node                     |
| 端口      | port                     |
| 编排      | orchestration (e.g. k8s) |
| 挂载      | mount (volumes)          |

## software engineering & workflow

| chinese       | english                            |
| ------------- | ---------------------------------- |
| 需求            | requirement / feature              |
| 功能            | feature / functionality            |
| 排期            | schedule / timeline estimation     |
| 迭代 (dié dài)  | iteration / sprint                 |
| 脚本            | script                             |
| 编译            | compile                            |
| 修改配置          | configure                          |
| 配置            | configuration                      |
| 灰度发布          | canary / grayscale release         |
| 上线            | deploy / go live / online          |
| 下线            | take offline / decommission        |
| 掉线/挂线         | lost connection                    |
| 线上            | online / production environment    |
| 回滚            | rollback                           |
| 重构            | refactoring                        |
| 优化            | optimize                           |
| 解耦 (jiě ǒu)   | decoupling                         |
| 抓包            | packet sniffing / tracing          |
| 复现            | reproduce (bug/issue)              |
| 兜底            | fallback mechanism / safeguard     |
| 开发者           | developer                          |
| 文档 (wén dǎng) | documentation                      |
| 部署            | deploy                             |
| 环境            | environment                        |
| 环境变量          | environment variables              |
| 依赖（关系）        | dependencies                       |
| 组件            | component                          |
| 模块化           | modularization                     |
| 默认            | defaults                           |
| 国际化           | internationalization (i18n)        |
| 删除            | delete                             |
| 更新            | update                             |
| 流程            | procedure / process                |
| 链路<br>关键链路    | data flow                          |
| 提测            | submit for testing                 |
| 驳回 / 退回       | reject / send back (e.g. pr, task) |
| 冒烟测试          | smoke testing                      |
| 热修复           | hotfix                             |
| 挂掉/崩溃         | crash / system down                |
| 假死            | frozen / non-responsive            |
| 热更新           | hot reload / live update           |
| 废弃            | deprecate / marked as deprecated   |
| 降级            | degradation (system load shedding) |
| 限流            | rate limiting                      |
| 上游/下游         | upstream/downstream                |
| 黑名单、白名单       | blacklist，whitelist                |
| 加白            | whitelist                          |
| 命中率           | hitrate                            |
| 落（消息）         | logging                            |

## comms

| chinese | english                                  |
| ------- | ---------------------------------------- |
| 对齐      | align / sync up                          |
| 拉通      | cross-team alignment                     |
| 拉会      | set up a meeting                         |
| 拉群      | set up chat group                        |
| 复盘      | post-mortem / retrospective              |
| 落地      | land / execute / implement               |
| 赋能      | empower / enablement                     |
| 闭环      | closed loop                              |
| 颗粒度     | granularity                              |
| 踢       | ping / mention (ims)                     |
| 取捨      | trade-off                                |
| 甩锅      | pass the blame / shifting responsibility |
| 背锅      | take the blame for a bug/incident        |
| 认领      | claim (a task / issue)                   |
## networking/web

| chinese    | english                              |
| ---------- | ------------------------------------ |
| 浏览器        | browser                              |
| api 接口     | api endpoint                         |
| api 网关     | api gateway                          |
| 调用         | call / use (e.g., 调 api)             |
| 请求         | requests                             |
| 请求头<br>    | request headers                      |
| 请求方法       | http verb                            |
| 返回（数据）     |                                      |
| 报错<br>错误   | error (verb)<br>error (noun)         |
| 路劲         | path                                 |
| 链接         | link                                 |
| 上传/下载      | upload / download                    |
| 分页         | paging / pagination                  |
| 域名         | domain                               |
| 路径         | path                                 |
| 领域资源       | cross-origin resource sharing (cors) |
| 筛选功能<br>过滤 | filter feature                       |
| 认证         | authentication                       |
| 授权         | authorization                        |
| （审）批       | approval                             |
| 权限         | permission                           |
| 鉴权         | permission verification / auth check |
| 埋点         | event tracking / analytics telemetry |
| 抓包         | packet capture / traffic analysis    |
| 超时         | timeout                              |
| 跨域         | cross-origin (cors)                  |

## infra

| chinese  | english                                 |
| -------- | --------------------------------------- |
| 进程       | process                                 |
| 线程       | thread                                  |
| 多线程      | multithreaded                           |
| 并发       | concurrent                              |
| 并发数      | concurrent users / connections          |
| 序列化      | serialization                           |
| 反序列化     | deserialization                         |
| 同步/异步    | sync / async                            |
| 微服务      | microservice                            |
| 框架       | framework                               |
| 吞吐量      | throughput                              |
| 吞吐量（tps） | transactions per second                 |
| QPS      | queries per second                      |
| 响应时间（rt） | response time                           |
| 流量       | traffic                                 |
| 压测       | stress testing                          |
| 联调       | integration testing                     |
| 内存       | memory                                  |
| 内核       | kernel                                  |
| 缓存       | cache                                   |
| 服务器      | server / service                        |
| 服务端      | server side                             |
| 崩溃       | crash                                   |
| 监控       | monitoring                              |
| 日志       | logs                                    |
| 代理       | proxy                                   |
| 反向代理     | reverse proxy                           |
| 防火墙      | firewall                                |
| 动态/静态    | active (dynamic) / static               |
| 打包       | build / package (e.g., java jar)        |
| 离线       | offline                                 |
| 解析       | parsing                                 |
| 解压       | unzip                                   |
| 压缩包      | zip file                                |
| 用户       | user                                    |
| 负载均衡     | load balancing                          |
| 高可用      | high availability (HA)                  |
| 容灾       | disaster recovery                       |
| 止血       | disaster mitigation (in the process of) |
| 熔断       | circuit breaking                        |
| 轮询       | polling                                 |
| 长轮询      | long polling                            |
| 心跳包      | heartbeat packet                        |

## observability & telemetry

| chinese     | english                                    |
| ----------- | ------------------------------------------ |
| 值班          | on-call                                    |
| 故障 / 事故     | incident                                   |
| 可观测性        | observability                              |
| 指标          | metric / metrics                           |
| 日志          | logs / logging                             |
| 追踪 / 链路追踪   | tracing / distributed tracing              |
| 告警/报警       | alert / alerting                           |
| 采样          | sampling / log sampling                    |
| 时序数据 / 时间序列 | time series data                           |
| 聚合          | aggregation                                |
| 分位数         | percentile (e.g. p95, p99)                 |
| 同步 / 异步采集   | sync / async collection                    |
| 埋点          | telemetry instrumentation / event tracking |

## databases

| chinese | english |
| --- | --- |
| 数据库 | database |
| 原子性 | atomicity (acid) |
| 一致性 | consistency (acid / cap) |
| 隔离性 | isolation (acid) |
| 持久性 | durability (acid) |
| 可用性 | availability (cap) |
| 分区容错性 | partition tolerance (cap) |
| cap 三选二 | cap theorem (pick 2 of 3) |
| 取舍 | tradeoff |
| 链接池 | connection pool |
| 索引 | indexing |
| 表 | table (量词：个）|
| 访问 | access (o(1)) |
| 插入 | insert |
| 删除 | delete |
| 遍历 | traverse |
| 查找 | search |
| 事务 | transaction |
| 锁 | lock (pessimistic / optimistic) |
| 主从同步 | master-slave replication |
| 分库分表 | database sharding and partitioning |
| 慢查询 | slow query |

## data structures & algorithms

| chinese      | english              |
| ------------ | -------------------- |
| 字（符）串        | string               |
| 数组           | array                |
| 链表           | linked list          |
| 单链表          | singly linked list   |
| 双向链表         | doubly linked list   |
| 环形链表         | circular linked list |
| 链表节点         | list node            |
| 栈            | stack                |
| 队列           | queue                |
| 双端队列         | deque                |
| 堆            | heap                 |
| 优先队列         | priority queue       |
| 哈希表          | hash table           |
| 集合           | set                  |
| 映射           | map                  |
| 图            | graph                |
| 树            | tree                 |
| 二叉树          | binary tree          |
| 二叉搜索树        | binary search tree   |
| 平衡二叉树        | balanced binary tree |
| 红黑树          | red-black tree       |
| b树           | b-tree               |
| 前缀树          | trie                 |
| 排序           | sort                 |
| 二分查找         | binary search        |
| 深度优先搜索 (dfs) | depth-first search   |
| 广度优先搜索 (bfs) | breadth-first search |
| 引用计数         | reference counting   |
| 垃圾回收         | garbage collection   |
| 十六比特         | 16-bit               |
| 动态规划         | dynamic programming  |
| 贪心算法         | greedy algorithm     |
| 回溯           | backtracking         |
| 拓扑排序         | topological sort     |
## c++ technical terms

| chinese | english |
| --- | --- |
| 智能指针 | smart pointers (std::unique_ptr, std::shared_ptr) |
| 移动语义 | move semantics (std::move) |
| 右值引用 | rvalue reference (t&&) |
| 完美转发 | perfect forwarding (std::forward) |
| raii (资源获取即初始化) | resource acquisition is initialization |
| 内存泄漏 | memory leak |
| 野指针 | dangling pointer |
| 段错误 / 崩溃 | segmentation fault / crash |
| 模板特化 | template specialization |
| 万能引用 | universal / forwarding reference |
| 未定义行为 | undefined behavior (ub) |
| 虚函数表 | vtable (virtual method table) |
| 深拷贝 / 浅拷贝 | deep copy / shallow copy |
| 头文件污染 | header pollution |
| 内存对齐 | memory alignment |
| 字节对齐 | byte alignment |
| 编译时 | compile time |
| 运行时 | run time |
| 内联函数 | inline function |
| 强制类型转换 | type casting |
| 虚重载 / 覆盖 | override |
| 隐藏 | name hiding |

---

## complexity & analysis

| chinese | english |
| --- | --- |
| 时间复杂度 | time complexity |
| 空间复杂度 | space complexity |
| 最坏情况 | worst case |
| 平均情况 | average case |
| 最好情况 | best case |
| 常数时间 | constant time |
| 对数时间 | logarithmic time |
| 线性时间 | linear time |
| 平方时间 | quadratic time |
| 指数时间 | exponential time |





