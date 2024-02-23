# 如何评估系统资源 

这篇文档描述了在系统资源，包括系统性能的指标、系统测试的类别等。

## 系统的性能指标 {id="system-performance-metrics"}

<code-block lang="mermaid">
  mindmap
    系统指标
      性能指标
        并发用户数
        响应时间RT
        QPS
        TPS
      资源指标
        CPU
        内存
        磁盘吞吐量
      数据库指标
        SQL耗时
        QPS
        TPS
        连接数
        缓存命中率
</code-block>

衡量系统性能的指标很多，最常关注的有：并发用户数、响应时间RT、QPS、TPS 等。

* **并发用户数**：指的是实际同时在操作系统的用户数量、注册用户数 > 现在线用户数 > 并发用户数 
  * **并发用户数的评估**：如果没有翔实的历史数据做参考的话，业内同行的估算方法是一定时间段内，在线人数的10%左右，也有放宽到 8% 到 15% 的。但是要注意，不同应用，不同时段的并发数是不一样的。一般选择两个小时。
* **响应时间**：从用户角度说，响应事件就是从用户发出指令，到系统处理完成，然后返回结果给到用户，这个过程的耗时。响应时间=网络传输时间（请求）+服务器处理事件+网络传输事件（响应）+页面解析渲染时间。
* **系统吞吐量（QPS/TPS）**：指的是单位事件内系统处理用户的请求数：从业务角度看，吞吐量可以用：请求数/秒、页面数/秒、人数/天等单位来衡量，从网络角度看，吞吐量可以用：字节/秒来衡量。常用的性能指标如下：
  * HPS（Hits Per Second），每秒点击数、单位是次/秒
  * TPS（Transaction Per Second），每秒处理的事务数量

<img src="http://file-linker.oss-cn-hangzhou.aliyuncs.com/zxfTbSNbbJ8Rpunso5qs.png" alt="ecs network"/>

衡量系统资源的性能指标有 CPU 、内存、磁盘吞吐量、网络吞吐量等：f

* **CPU**： 一般来说，CPU 利用率应该低于 70%，多核情况下，是按照每核心来计算的，也就是说会超过 100%
* **内存**：注意 SWAP （与虚拟内存交换）交换空间利用率，一般情况下，SWAP 交换空间利用率要低于 70%，太多的交换会引起系统性能低下
* **磁盘吞吐量**：磁盘指标主要有每秒读写多少MB、磁盘繁忙率、磁盘队列数、平均服务时间、平均等待时间、空间利用率等。其中磁盘繁忙率是直接反应磁盘是否有瓶颈的重要依据，一般情况下，磁盘繁忙率要低于 70%
* **网络吞吐量**：网络吞吐量指标主要有每秒有多少MB流量进出，一般情况下超过设备或者链路最大传输能力的 70%

<img src="http://file-linker.oss-cn-hangzhou.aliyuncs.com/ARA48ysWb3U2E55UNJmv.png" alt="cpu disk usage"/>

衡量数据库的性能指标：SQL 耗时、QPS、TPS、缓存命中率、连接数、磁盘 IO 吞吐等。

* **SQL 耗时**，执行一条 SQL 消耗多少时间
* **QPS**：每秒查询次数
* **TPS**：每秒事务次数
* **缓存命中率**：Key Buffer 命中率，指的是索引缓冲区命中率。Cache 命中率，Table Cache 表缓存命中率。Thread Cache 线程缓存命中率。
* **锁**：锁等待次数越少越好，等待时间越短越好。

<img src="http://file-linker.oss-cn-hangzhou.aliyuncs.com/ckDYNXN9YjI8j7APLA83.png" alt="sql exec time"/>

## 服务端性能测试 {id="api-service"}

根据测试目的不同可以划分如下类别:

<code-block lang="mermaid">
  mindmap
    系统性能测试类别
      基准测试
      负载测试
      压力测试
      疲劳强度测试
      稳定性测试
      容量测试
</code-block>

* **基准测试**：模拟单个用户执行业务场景时，获取系统的性能指标。作用是将基准测试采集的系统性能指标作为基准测试结果，为后续的并发压力测试的性能分析提供参考依据
* **负载测试**： 模拟系统在正常负载压力场景下，考察系统的性能指标。正常负载压力指的是，按照用户最高期望值进行压测。通常负载测试是最典型的性能测试类型，通过实施负载测试来获得性能拐点，也叫做最佳性能点。
* **压力测试**： 压力测试会对被测系统逐步施压，在加压过程中考察系统性能指标的走势情况，最终找出系统在性能拐点的并发用户数，也就是系统支持的最大并发用户数。
* **疲劳强度测试**：疲劳强度测试是对系统模拟出系统能够承受的最大业务负载量，然后观察一段时间，疲劳强度测试更关注系统在长时间运行情况下系统性能指标的变化情况。
* **稳定性测试**：把用户真实会发生的场景放大3－5倍，然后在线上运行一段事件，以此来测试系统是否会出现问题。
* **容量测试**：为了测试系统最大的容量，为系统扩容、性能优化提供参考，节省成本投入，提升资源利用率。