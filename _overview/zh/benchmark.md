---
permalink: /overview/benchmark/
---

## 测试环境

**机器配置：**

* CPU：Intel(R) Xeon(R) CPU E5-2620 v3 @ 2.40GHz （24 cores）
* 内存​：128GB
* 存储：480G RAID0 SSD *8
* 网卡：10Gb

**集群配置：**

* 节点数：5个replica server节点
* 测试表的Partition数：64个

**测试工具：**

* [YCSB](https://github.com/xiaomi/pegasus-ycsb) (使用Pegasus Java Client)
* 读写请求的数据分布特征：zipfian，可以理解为遵守80/20原则的数据分布，即80%的访问都集中在20%的内容上。

**备注：**

* 运行时长单位：小时。
* QPS单位：条/秒。
* 延迟单位：微秒。

## 测试结果

### 1.12.3

* 延迟单位: 微秒
* pegasu-client: 1.11.10-thrift-0.11.0-inlined-release
* 客户端节点数: 3
  
#### 单条数据大小：320B

| 测试case | RW Ratio | R-QPS  | R-AVG-Lat | R-P99-Lat | W-QPS | W-AVG-Lat | W-P99-Lat |
| --------------------- | -------- | ------ | --------- | --------- | ----- | --------- | --------- |
| 读写同时: 3客户端*15线程 | 0:1      |        |           |           | 41068 | 728       | 3439      |
| 读写同时: 3客户端*15线程 | 1:3      | 16011  | 242       | 686       | 48036 | 851       | 4027      |
| 读写同时: 3客户端*30线程 | 30:1     | 279818 | 295       | 873       | 9326  | 720       | 3355      |

#### 单条数据大小：1KB

| 测试case | RW Ratio | R-QPS  | R-AVG-Lat | R-P99-Lat | W-QPS | W-AVG-Lat | W-P99-Lat |
| --------------------- | -------- | ------ | ------ | --------- | --------- | ----- | --------- | --------- |
| 读写同时: 3客户端*15线程 | 0:1      |        |           |           | 40732 | 1102       | 4216      |
| 读写同时: 3客户端*15线程 | 1:3      | 14355  | 476       | 2855       | 38547 | 1016       | 4135      |
| 读写同时: 3客户端*20线程 | 3:1      | 87480  | 368       | 4170       | 29159 | 940       | 4170      |
| 读写同时: 3客户端*50线程 | 1:0      | 312244 | 479       | 1178       |     |        |       |

### 1.12.2

* 延迟单位: 微秒
* pegasu-client: 1.11.10-thrift-0.11.0-inlined-release
* 客户端节点数: 3
* 单条数据大小：320B

| 测试case | RW Ratio | R-QPS  | R-AVG-Lat | R-P99-Lat | W-QPS | W-AVG-Lat | W-P99-Lat |
| --------------------- | -------- | ------ | ------ | --------- | --------- | ----- | --------- | --------- |
| 读写同时: 3客户端*15线程 | 0:1      |        |           |           | 40439 | 739       | 2995      |
| 读写同时: 3客户端*15线程 | 1:3      | 16022  | 309       | 759       | 48078 | 830       | 3995      |
| 读写同时: 3客户端*30线程 | 30:1     | 244392 | 346       | 652       | 8137  | 731       | 2995      |

### 1.11.6(multi_get/batch_set)

特别注意：测试环境和参数以此为准

* 测试case：读使用multi_get，写使用batch_set，1QPS的batch_set等于3QPS的set操作
* 数据大小：单条数据3KB，sort_key数量为3
* 测试表的Partition数：128个
* rocksdb_block_cache_capacity = 40G
* pegasu-client = 1.11.7-thrift-0.11.0-inlined-release

| 测试Case       | 读写比 | 运行时长 | Max缓存命中率 | 读QPS | 读Avg延迟 | 读P99延迟 | 读P999延迟 | 写QPS | 写Avg延迟 | 写P99延迟 | 写P999延迟 |
| -------------- | ------ | -------- | ------------- | ----- | --------- | --------- | ---------- | ----- | --------- | --------- |
| 3客户端*15线程 | ​20:1 | 1h      | 10%          | 150K | 263      | 808      | 12615     | 8k   | 1474     | 7071     | 26342     |
| ​3客户端*7线程 | ​20:1  | 2h       | 17%           | 75K   | 226       | 641       | 5331       | 4K    | 1017      | 4583      | 14983      |

### 1.11.1

#### 单条数据大小：20KB

测试时间：2018/11/9

##### 2备份

| 测试Case                     | 读写比 | 运行时长 | 读QPS | 读Avg延迟 | 读P99延迟 | 写QPS | 写Avg延迟 | 写P99延迟 |
| ---------------------------- | ------ | -------- | ----- | --------- | --------- | ----- | --------- | --------- |
| (1)数据加载: 3客户端*10线程  | 0:1    | 0.98     | -     | -         | -         | 8439  | 3557      | 32223     |
| (2)​读写同时: 3客户端*15线程 | ​1:3   | 0.66     | 3159  | 4428      | 34495     | 9472  | 3251      | 25071     |
| ​(3)读写同时: 3客户端*30线程 | ​30:1  | 1.25     | 64358 | 1330      | 13975     | 2145  | 1699      | 6467      |
| (4)数据只读: 6客户端*100线程 | 1:0​   | ​0.91    | 30491 | 3274      | 12167     | -     | -         | -         |

##### 3备份

| 测试Case                     | 读写比 | 运行时长 | 读QPS | 读Avg延迟 | 读P99延迟 | 写QPS | 写Avg延迟 | 写P99延迟 |
| ---------------------------- | ------ | -------- | ----- | --------- | --------- | ----- | --------- | --------- |
| (1)数据加载: 3客户端*10线程  | 0:1    | 1.40     | -     | -         | -         | 5919  | 5063      | 40639     |
| (2)​读写同时: 3客户端*15线程 | ​1:3   | 1.11     | 1876  | 6927      | 44639     | 5632  | 5612      | 76095     |
| ​(3)读写同时: 3客户端*30线程 | ​30:1  | 1.63     | 49341 | 1751      | 21615     | 1644  | 1935      | 11159     |
| (4)数据只读: 6客户端*100线程 | 1:0​   | ​0.91    | 25456 | 3923      | 15679     | -     | -         | -         |


#### 单条数据大小：10KB

测试时间：2018/11/5

##### 2备份

| 测试Case                     | 读写比 | 运行时长 | 读QPS  | 读Avg延迟 | 读P99延迟 | 写QPS | 写Avg延迟 | 写P99延迟 |
| ---------------------------- | ------ | -------- | ------ | --------- | --------- | ----- | --------- | --------- |
| (1)数据加载: 3客户端*10线程  | 0:1    | 0.78     | -      | -         | -         | 14181 | 2110      | 15468     |
| (2)​读写同时: 3客户端*15线程 | ​1:3   | 0.52     | 4024   | 5209      | 41247     | 12069 | 1780      | 14495     |
| ​(3)读写同时: 3客户端*30线程 | ​30:1  | 0.76     | 105841 | 816       | 9613      | 3527  | 1107      | 4155      |
| (4)数据只读: 6客户端*100线程 | 1:0​   | ​​1.04   | 162150 | 1868      | 6733      | -     | -         | -         |

##### 3备份

| 测试Case                     | 读写比 | 运行时长 | 读QPS  | 读Avg延迟 | 读P99延迟 | 写QPS | 写Avg延迟 | 写P99延迟 |
| ---------------------------- | ------ | -------- | ------ | --------- | --------- | ----- | --------- | --------- |
| (1)数据加载: 3客户端*10线程  | 0:1    | 1.16     | -      | -         | -         | 9603  | 3115      | 20468     |
| (2)​读写同时: 3客户端*15线程 | ​1:3   | 0.69     | 3043   | 5657      | 38783     | 9126  | 3140      | 27956     |
| ​(3)读写同时: 3客户端*30线程 | ​30:1  | 0.89     | 90135  | 937       | 13324     | 3002  | 1185      | 4816      |
| (4)数据只读: 6客户端*100线程 | 1:0​   | 1.08     | 154869 | 1945      | 7757      | -     | -         | -         |

**RocsDB配置：**

``` ini
    rocksdb_abnormal_get_time_threshold_ns = 10000000
    rocksdb_abnormal_get_size_threshold = 1000000
    rocksdb_abnormal_multi_get_time_threshold_ns = 100000000
    rocksdb_abnormal_multi_get_size_threshold = 10000000
    rocksdb_abnormal_multi_get_iterate_count_threshold = 1000

    rocksdb_write_buffer_size = 67108864
    rocksdb_max_write_buffer_number = 6
    rocksdb_max_background_flushes = 4
    rocksdb_max_background_compactions = 12
    rocksdb_num_levels = 6
    rocksdb_target_file_size_base = 67108864
    rocksdb_target_file_size_multiplier = 1
    rocksdb_max_bytes_for_level_base = 671088640
    rocksdb_max_bytes_for_level_multiplier = 10
    rocksdb_level0_file_num_compaction_trigger = 4
    rocksdb_level0_slowdown_writes_trigger = 30
    rocksdb_level0_stop_writes_trigger = 60
    rocksdb_disable_table_block_cache = false
    rocksdb_compression_type = snappy none
```

### 1.11.0

* 单条数据大小：320字节
* 测试时间：2018/07/27

| 测试Case                      | 读写比 | 运行时长 | 读QPS    | 读Avg延迟 | 读P99延迟 | 写QPS | 写Avg延迟 | 写P99延迟 |
| ----------------------------- | ------ | -------- | -------- | --------- | --------- | ----- | --------- | --------- |
| (1)数据加载: 3客户端*10线程   | 0:1    | 1.89     | -        | -         | -         | 44039 | 679       | 3346      |
| (2)​读写同时: 3客户端*15线程  | ​1:3   | 1.24     | 16690    | 311       | 892       | 50076 | 791       | 4396      |
| ​(3)读写同时: 3客户端*30线程  | ​30:1  | 1.04     | 311633   | 264       | 511       | 10388 | 619       | 2468      |
| (4)数据只读: 6客户端*100线程  | 1:0​   | ​0.17    | ​978884  | 623       | ​1671     | -     | -         | -         |
| (5)数据只读: 12客户端*100线程 | 1:0​   | ​0.28    | ​1194394 | 1003      | ​2933     | -     | -         | -         |


## 不同参数下的性能

如无特殊说明：

* 延迟单位：微妙
* 单条数据大小：1KB
* 客户端节点数：3，版本号：1.11.10-thrift-0.11.0-inlined-release
* 服务端节点数：5，版本号：1.12.3，表分片数：64，开启rocksdb限速：max_rate=500MB, auto_tune=true

### 客户端线程数

该项测试旨在观察在默认配置下（注意：未开启RocksDB限速），不同线程（仅读和仅写）对QPS和延迟的影响。

![5-node-write](/assets/images/benchmark/5-node-write.png)

![5-node-read](/assets/images/benchmark/5-node-read.png)

由上图可以看到，在该测试场景下，写最大QPS≈43K，读最大QPS≈370K，你也可以根据QPS的大小合理估算对应的延迟。

### RocksDB限速

Pegasus底层采用RocksDB做存储引擎，当数据写入增多，会触发compaction操作，占用较多磁盘IO，出现较多的毛刺现象。该项测试展示了开启[RocksDB的限速](http://10.232.52.164:4000/overview/benchmark)后，可以降低compaction负载，从而显著的降低毛刺现象。

下图分别展示了无限速、500MB限速、500MB限速同时开启auto-tune功能，三种场景的IO使用率和写P99延迟(注意：测试场景为：3client*20thread，QPS≈44K)：

磁盘IO占用：
![io-no-limit](/assets/images/benchmark/io-no-limit.png)
![io-limit-500MB](/assets/images/benchmark/io-limit-500MB.png)
![io-limit-500MB-auto](/assets/images/benchmark/io-limit-500MB-auto.png)

P99延迟：
![no-limit-set](/assets/images/benchmark/no-limit-set.png)
![500-limit-set](/assets/images/benchmark/500-limit-set.png)
![500-limit-auto-set](/assets/images/benchmark/500-limit-auto-set.png)

可以发现，磁盘IO使用率被降低，相应的写延迟的毛刺现象也被大大缓解。

我们从YCSB的测试结果：
![limit](/assets/images/benchmark/limit.png)
也可以看到：

* 开启限速、开启限速并开启auto-tune后，QPS吞吐分别约提升了5%，20%
* 开启限速后，仅对极端情况下的延迟(P999/P9999)有显著改善作用，对于大部分请求来说，改善并不明显

但是**需要注意**的是：

auto-tune功能在单条数据较大的场景下可能会引发[write stall](https://github.com/facebook/rocksdb/wiki/Write-Stalls)（在我们的测试中，当单条value=10KB，QPS=10K时，发生write stall，关闭Auto-Tune后不会产生write stall），所以请合理评估是否开启auto-tune

### 服务端机器数
该项测试旨在观察，不同机器数量对读写性能(只读和只写场景)的影响。
![node-write](/assets/images/benchmark/node-qps-write.png)

![node-read](/assets/images/benchmark/node-qps-read.png)

由图中可以看到

* 扩容对写性能的提升要优于读性能的提升
* 扩容带来的性能提升并不是线性增加的

你可以根据该项测试合理估计不同机器下所能承载的请求负载

### 表分片数

该项测试旨在观察，表的不同分片对性能的影响。测试场景为：

仅读：3client*50thread

仅写：3client*40thread

![partition](/assets/images/benchmark/partition.png)

由图中可以看到，增加分片可以提高读性能，但是降低了写性能，所以请合理评估你的业务需求。除此之外，若分片数过小，可能会导致单分片过大，磁盘分布不均的问题，在实际的线上业务中，如无特别需求，我们建议单分片维持在10GB以内是合理的
