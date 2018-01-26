## 关于TailBench，它即是基准套件，也是评估方法，适用于对延迟有严格要求的应用

**latency**：延迟一般包括单向延迟（One-way Latency）和往返延迟（Round Trip Latency），实际测量时一般取往返延迟。它是时间单位。我理解latency就是响应时间。

**throughput**：吞吐量一般指**相当一段时间内**测量出来的系统单位时间处理的任务数或事务数（TPS）。

**Concurrency**：并发度 = 吞吐量 * 延迟

**tail latency**：高并发系统中少量响应的延迟高于均值的现象。分布式操作系统的核心挑战就在于对付尾延迟。Heracles是Google的核心成果，其资源利用率可达到90%以上。Heracles把数据中心的任务分为两类：BE(Best Effort Batch)和LC(Latency Critical)，LC型任务依赖严格的SLO(Service Level Objectives,包含CPU、缓存、主存、IO、网络和功耗等物理资源)。Heracles将这些共享资源隔离起来，尽可能避免SLO冲突。

**TailBench**：一个基准套件和评价方法，使对延迟有严格要求的workload，和运行及表征传统的面向吞吐量的workload一样容易。包含8个应用程序，覆盖了广泛的延迟需求和领域。还包含了一个harness，实现了鲁棒的和统计良好的负载测试方法。

### 1. 介绍

对延迟有严格要求的应用程序在数据中心越来越普遍，尾延迟是这些应用的关键性能指标。低尾延迟的需求对系统设计者提供了新的挑战和机遇。

不幸的是，由于缺乏一套完整的latency-critical benchmarks，使得研究这个新兴类型的应用程序变的困难。这个困难导致两个关键问题。一是它阻碍了为latency-critical applications优化系统的研究。二是关于架构和系统的大部分新思想只是对面向吞吐量的应用程序的评估，而不包含latency-critical ones，它们构成了这些技术设计中的盲点。

为应对以上挑战，我们提出了TailBench。它包含了一组不同的latency-critical applications和一个健壮的经过验证的实验方法，但它可以容易地将那些基准运行在真实系统和模拟环境中。特别地，我们做出了如下关键贡献：

- 我们选择了8个典型的latency-critical applications，它们具有不同的特征。(详见第3节)
- 我们将所有workloads集成在一个共同的harness上，实现了一个健壮的、统计上合理的方法(详见第4节)。
- 我们在实际系统和仿真中验证了tailbench方法。(详见第6节)
- 我们通过一个案例研究说明了TailBench的优点。(详见第7节)

### 2. 背景

1. Latency-Critical Applications的剖析
2. TailBench和现存的基准套件

### 3. TailBench应用程序

详见原论文**Table 1**，MPKI意思是Missed Predictions per 1000 (=Kilo) Instructions，**不理解最后一行里20%,50%,70%的意思**

- **xapian**:搜索引擎
- **masstree**:内存中的key-value存储
- **moses**:当前技术条件下的统计机器翻译(SMT)系统
- **sphinx**:精确的语音识别系统
- **img-dnn**:手写识别程序，是图像识别程序里的一个边缘类型。
- **specjbb**:一个工业标准，Java中间件基准
- **silo**:一个快速的内存事物数据库。像silo这样的数据库被广泛应用于联机事物处理系统(OLTP)，silo是用工业标准的OLTP基准TPC-C来驱动的
- **shore**:一个事物型数据库，与silo不同的是它是硬盘上的数据库。

### 4. TailBench Harness

详见原论文**Fig 1**

Harness由3部分组成：Traffic Shaper, Request Queue, Stat Collector

1. 网络化的结构

2. 简化的Harness结构(loopback结构和集成结构)

3. 统计收集和延迟测量

   **HDR histogram**指高动态范围直方图

### 5. 应用程序的特性

### 6. 验证简化的Harness配置

1. 实验方法
2. 单线程应用程序
3. 多线程应用程序

### 7. 案例研究

### 8. 结论

### 9. 致谢与引用