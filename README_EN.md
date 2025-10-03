# Design and Implementation of LSM-based Key-Value Database

## Abstract

This paper presents the design and implementation of a high-performance key-value storage database system based on Log-Structured Merge-tree (LSM-tree). LSM-tree is a data structure specifically optimized for write-intensive workloads, which significantly improves database write performance by converting random writes into sequential writes. This paper elaborates on the fundamental principles of LSM-tree, system architecture design, core algorithm implementation, and performance optimization strategies. Experimental results demonstrate that compared to traditional B+ tree index structures, our system achieves 3-5x performance improvement in write-intensive scenarios while maintaining good read performance. The system is implemented in Java with excellent scalability and stability, suitable for large-scale data storage scenarios.

**Keywords:** LSM-tree; Key-Value Storage; NoSQL Database; Write Optimization; Java Implementation

---

## Quick Reference

### System Performance Metrics

| Metric | Value | Notes |
|--------|-------|-------|
| Write Throughput | 45,000 ops/s | Random writes, 1KB values |
| Read Throughput | 35,000 ops/s | Random reads, 50% hit rate |
| Write Latency (P99) | 2.5 ms | Stable under load |
| Read Latency (P99) | 3.2 ms | With bloom filters |
| Space Amplification | 1.2-1.5x | Leveled compaction |
| Max Data Size | 1TB+ | Tested configuration |

### Architecture Overview

```
Application Layer
       ↓
   API Layer (PUT/GET/DELETE/SCAN)
       ↓
Memory Layer (MemTable + WAL)
       ↓
Disk Layer (Multi-level SSTables)
       ↓
Storage (L0 → L1 → L2 → ... → LN)
```

### Key Components

1. **MemTable**: In-memory sorted structure (Skip List)
2. **SSTable**: Immutable on-disk sorted files
3. **Compaction**: Background merge process
4. **Bloom Filter**: Fast negative lookup
5. **WAL**: Write-Ahead Log for durability

### Performance Comparison

| Database | Write (ops/s) | Read (ops/s) | Latency P99 |
|----------|--------------|-------------|-------------|
| LSM-KVDB | 45,000 | 35,000 | 2.5ms |
| B+Tree DB | 12,000 | 42,000 | 8.3ms |
| MySQL | 8,500 | 28,000 | 15.6ms |
| Redis | 52,000 | 98,000 | 1.8ms |

---

## Core Algorithms

### Write Operation
1. Append to WAL (durability)
2. Insert into MemTable (in-memory)
3. Trigger flush when size threshold reached
4. Convert to SSTable on disk
5. Trigger compaction if needed

### Read Operation
1. Check active MemTable
2. Check immutable MemTable
3. Search SSTables level by level
4. Use bloom filters to skip files
5. Return value or NOT_FOUND

### Compaction Strategy
- **Size-Tiered**: Low write amplification
- **Leveled**: Low space amplification (recommended)
- **Time-Window**: For time-series data

---

## Configuration Guidelines

### Recommended Settings

```properties
# For Write-Heavy Workloads
memtable.size=128MB
block.cache.size=512MB
compaction.style=size-tiered
compression=snappy

# For Read-Heavy Workloads
memtable.size=32MB
block.cache.size=4GB
compaction.style=leveled
bloom.filter.bits.per.key=15

# Balanced Configuration
memtable.size=64MB
block.cache.size=2GB
compaction.style=leveled
bloom.filter.bits.per.key=10
```

---

## Use Cases

1. **Log Storage Systems**: High write throughput
2. **Time-Series Databases**: Sequential data ingestion
3. **Message Queue Persistence**: Reliable storage
4. **Session Stores**: High churn rate data

---

## References

[1] O'Neil, P., et al. (1996). The log-structured merge-tree (LSM-tree). *Acta Informatica*, 33(4), 351-385.

[2] Chang, F., et al. (2008). Bigtable: A distributed storage system for structured data. *ACM TOCS*, 26(2), 1-26.

[3] Lakshman, A., & Malik, P. (2010). Cassandra: a decentralized structured storage system. *ACM SIGOPS*, 44(2), 35-40.

---

For the complete paper with detailed diagrams and experimental data, please refer to the Chinese version: **论文_基于LSM树的键值对存储数据库设计与实现.md**

For detailed diagrams and visualizations, please refer to: **图表说明.md**
