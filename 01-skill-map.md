# 中级 Go 技术地图

掌握程度分为四级：

- 了解：知道用途和基本概念。
- 会用：能在项目中正确使用。
- 能解释：能说明原理、边界和替代方案。
- 能排障：出现异常时能定位原因并修复。

中级要求通常是：核心项达到“能解释”，生产关键项至少有部分达到“能排障”。

## 1. Go 语言核心

### 语法与类型

- 变量、常量、基本类型、类型转换
- 数组、slice、map、string
- struct、方法、接口、组合
- 指针和值语义
- 函数、闭包、可变参数
- `defer`、`panic`、`recover`
- 错误创建、包装、判断与传播：`errors.Is`、`errors.As`、`%w`
- 泛型的类型参数和约束

掌握目标：能解释 slice 的长度与容量、扩容造成的底层数组变化、map 并发读写风险、接口值为何可能“看起来不为 nil”。

### 包与依赖

- 包的可见性、初始化顺序和循环依赖
- `go mod init`、`go get`、`go mod tidy`
- 语义化版本、最小版本选择的基本概念
- `internal`、`cmd`、`pkg` 的使用边界
- 配置与代码分离

### 常用标准库

至少熟悉：

- `context`
- `errors`
- `fmt`
- `io`、`os`、`bufio`
- `encoding/json`
- `time`
- `net/http`、`net/url`
- `database/sql`
- `sync`、`sync/atomic`
- `log/slog`
- `testing`

## 2. 并发编程

- goroutine 的创建、调度基本概念和生命周期
- 无缓冲与有缓冲 channel
- channel 的发送、接收、关闭和阻塞规则
- `select`、超时和非阻塞操作
- `context` 的取消、截止时间和跨层传递
- `Mutex`、`RWMutex`、`WaitGroup`、`Once`、`Cond`
- 原子操作的适用边界
- worker pool、扇入扇出、信号量和限流
- 数据竞争、死锁、活锁、goroutine 泄漏
- 优雅关闭：停止接收请求、等待任务、释放资源

必须会用：

```bash
go test -race ./...
go test -run TestName ./path/to/package
```

常见错误：复制包含锁的 struct、忘记取消 context、生产者不退出、重复关闭 channel、用无限 goroutine 代替容量控制。

## 3. Web 与 API

- HTTP 方法、状态码、Header、Cookie、连接与超时
- 使用 `net/http` 创建服务和客户端
- REST 资源建模、分页、过滤和版本管理
- JSON 编解码与字段校验
- 中间件：日志、鉴权、恢复、请求 ID、限流
- JWT、Session、RBAC 基础
- CORS、TLS、常见 Web 安全问题
- 幂等键、防重复提交
- gRPC、Protobuf、拦截器和错误码
- API 文档：OpenAPI/Swagger

框架建议：先使用 `net/http` 写一个小服务，再选 Gin；需要微服务脚手架时再了解 Go-Zero、Kratos 或同类框架。

## 4. 数据库与缓存

### MySQL/PostgreSQL

- 表、主键、外键、约束和范式的实际取舍
- B+Tree 索引、联合索引和最左前缀
- 覆盖索引、回表、索引失效
- `EXPLAIN` 和慢查询分析
- 事务 ACID、隔离级别和 MVCC
- 脏读、不可重复读、幻读
- 乐观锁、悲观锁和死锁
- 连接池：最大连接、空闲连接和生命周期
- 分页、批量写入、迁移和数据回滚
- ORM 与原生 SQL 的边界

Go 侧重点：正确传递 `context`、及时关闭 rows、处理事务提交与回滚、区分“无记录”和真正错误。

### Redis

- String、Hash、List、Set、ZSet 的选型
- TTL、淘汰策略和持久化基本概念
- Cache Aside 模式
- 缓存穿透、击穿、雪崩
- 热点 Key、大 Key
- 缓存与数据库一致性
- 分布式锁的所有权、超时和续期问题
- Lua 脚本保证原子操作

## 5. 消息队列

Kafka、RabbitMQ、RocketMQ 或 NATS 至少深入一种：

- 生产、存储、消费模型
- 消息确认和消费位点
- 至少一次、至多一次；“恰好一次”的条件与边界
- 重复消费和业务幂等
- 顺序消息
- 重试、退避和死信队列
- 消息积压、消费扩容和流量削峰
- 事务消息与最终一致性

## 6. 微服务与分布式系统

- 单体与微服务的成本边界
- 服务注册发现、配置中心
- RPC、负载均衡、连接池
- 超时预算、重试风暴、熔断、降级、限流
- 分布式 ID、分布式锁
- CAP、最终一致性、补偿和 Saga 基础
- API 网关
- 灰度发布、回滚和向后兼容
- 高可用、无状态服务和故障隔离

中级不要求发明分布式算法，但要理解组件失效后系统会怎样，并为超时、重复、乱序和部分失败编程。

## 7. 测试与代码质量

- 表驱动单元测试和子测试
- Mock、Stub、Fake 的区别
- HTTP Handler 测试
- 数据库集成测试
- Benchmark 和性能回归
- Fuzz 测试
- 测试覆盖率的意义与局限
- `go vet`、格式化、静态检查
- 代码评审、重构和技术债管理

常用命令：

```bash
go test ./...
go test -cover ./...
go test -bench=. -benchmem ./...
go vet ./...
gofmt -w .
```

## 8. 性能与可观测性

- CPU、内存、阻塞、锁竞争和 goroutine profile
- `pprof`、trace、Benchmark
- 栈与堆、逃逸分析和 GC 基本原理
- 减少无意义分配，但避免脱离测量的“优化”
- 结构化日志、日志级别和敏感信息处理
- RED 指标：请求率、错误率、耗时
- Prometheus、Grafana
- OpenTelemetry 和分布式链路追踪
- 健康检查和告警

诊断顺序：确认现象 → 收集指标与 profile → 提出假设 → 小范围验证 → 修复 → 压测和回归。

## 9. Linux、容器与交付

- 进程、信号、端口、文件权限、环境变量
- `curl`、`ps`、`top`、`ss`、`lsof`、`journalctl`
- Dockerfile、多阶段构建、镜像体积和非 root 用户
- Docker Compose 本地编排
- CI：构建、测试、静态检查和制品生成
- Kubernetes：Pod、Deployment、Service、ConfigMap、Secret
- readiness、liveness、资源限制和滚动发布
- Nginx/网关的反向代理基础

## 10. 计算机基础与软技能

- 数据结构与复杂度：数组、链表、栈、队列、哈希表、树、堆
- TCP/IP、DNS、HTTP/1.1、HTTP/2、TLS
- 进程、线程、协程、虚拟内存和文件系统基础
- 能拆解需求、估算风险、写技术方案和 API 文档
- 能参加代码评审、复盘故障并清楚沟通阻塞项

## 高频速查：问题该用什么工具

| 问题 | 首选检查方向 |
|---|---|
| CPU 很高 | 指标、CPU profile、热点函数 |
| 内存上涨 | heap profile、对象生命周期、缓存与 goroutine |
| 接口变慢 | trace、下游耗时、数据库慢查询、连接池 |
| 偶发数据错误 | race detector、事务、幂等、并发读写 |
| goroutine 持续增加 | goroutine profile、阻塞调用、未取消的 context |
| 数据库连接耗尽 | 未关闭 rows、慢事务、连接池设置、下游延迟 |
| 消息重复 | 消费语义、幂等键、确认时机、重试逻辑 |
| 发布后大量失败 | 配置、依赖兼容、健康检查、指标对比和回滚 |
