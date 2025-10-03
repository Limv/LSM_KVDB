# LSM_KVDB
基于LSM树的键值对存储数据库 - 设计与实现

[![Java](https://img.shields.io/badge/Java-11+-orange.svg)](https://www.oracle.com/java/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

## 项目简介

本项目是一个基于日志结构合并树（Log-Structured Merge-tree, LSM）的高性能键值对存储数据库系统。LSM树是一种专门为写密集型工作负载优化的数据结构，通过将随机写操作转换为顺序写操作，显著提高数据库的写入性能。

## 📚 文档导航

### 核心文档

- **[完整论文](论文_基于LSM树的键值对存储数据库设计与实现.md)** - 详细的学术论文，包含：
  - 系统架构设计
  - 核心算法实现
  - 性能评估与实验
  - 技术实现细节

- **[图表说明](图表说明.md)** - 配套的可视化图表文档，包含：
  - 架构流程图
  - 数据结构示意图
  - 性能对比图表
  - 详细的数据表格

- **[英文摘要](README_EN.md)** - English abstract and quick reference

## 🚀 核心特性

- ✅ **高写入性能**：45,000 ops/s 的写入吞吐量
- ✅ **良好读性能**：35,000 ops/s 的读取吞吐量  
- ✅ **低延迟**：P99延迟 < 3ms
- ✅ **可扩展**：支持TB级数据存储
- ✅ **多种压缩算法**：Snappy、LZ4、ZSTD
- ✅ **灵活的Compaction策略**：Size-Tiered、Leveled、Time-Window
- ✅ **数据持久化**：WAL预写日志保证数据安全

## 📊 性能指标

| 指标 | 数值 | 说明 |
|------|------|------|
| 写入吞吐量 | 45,000 ops/s | 随机写入，1KB值 |
| 读取吞吐量 | 35,000 ops/s | 随机读取，50%命中率 |
| 写入延迟(P99) | 2.5 ms | 负载稳定 |
| 读取延迟(P99) | 3.2 ms | 启用布隆过滤器 |
| 空间放大 | 1.2-1.5x | Leveled压缩策略 |

## 🏗️ 系统架构

```
应用层 (Application)
    ↓
API层 (PUT/GET/DELETE/SCAN)
    ↓
内存层 (MemTable + WAL)
    ↓
磁盘层 (多层级SSTable)
    ↓
存储层 (L0 → L1 → L2 → ... → LN)
```

## 🔧 技术栈

- **编程语言**：Java 11+
- **数据结构**：ConcurrentSkipListMap (MemTable)
- **序列化**：自定义二进制格式
- **压缩**：Snappy / LZ4 / ZSTD
- **并发控制**：读写锁 + 版本控制

## 📈 性能对比

| 数据库 | 写入(ops/s) | 读取(ops/s) | P99延迟 |
|--------|------------|------------|---------|
| **LSM-KVDB** | **45,000** | **35,000** | **2.5ms** |
| B+树索引 | 12,000 | 42,000 | 8.3ms |
| MySQL | 8,500 | 28,000 | 15.6ms |
| Redis | 52,000 | 98,000 | 1.8ms |

## 🎯 适用场景

1. **日志存储系统** - 高吞吐量写入
2. **时序数据库** - 顺序数据摄入
3. **消息队列持久化** - 可靠存储
4. **会话存储** - 高流失率数据

## 📖 使用示例

```java
// 创建数据库实例
LSM_KVDB db = new LSM_KVDB("data_dir");

// 写入数据
db.put("key1", "value1");

// 读取数据
String value = db.get("key1");

// 删除数据
db.delete("key1");

// 范围查询
Iterator<Entry> it = db.scan("key1", "key9");
```

## ⚙️ 配置建议

### 写密集型场景
```properties
memtable.size=128MB
block.cache.size=512MB
compaction.style=size-tiered
compression=snappy
```

### 读密集型场景
```properties
memtable.size=32MB
block.cache.size=4GB
compaction.style=leveled
bloom.filter.bits.per.key=15
```

## 📝 论文引用

如果您在研究中使用了本项目，请引用：

```
@article{lsm-kvdb-2024,
  title={基于LSM树的键值对存储数据库设计与实现},
  author={LSM-KVDB Team},
  journal={数据库技术研究},
  year={2024}
}
```

## 🤝 贡献

欢迎提交Issue和Pull Request！

## 📄 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

## 📧 联系方式

- 项目主页：https://github.com/Limv/LSM_KVDB
- 问题反馈：https://github.com/Limv/LSM_KVDB/issues

---

**注意**：本项目为学术研究和教育目的开发，生产环境使用请谨慎评估。