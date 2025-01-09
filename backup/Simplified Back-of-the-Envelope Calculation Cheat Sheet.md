### **Simplified Back-of-the-Envelope Calculation Cheat Sheet**

| **Category**         | **Metric**                          | **Value**                     | **Notes**                                                                 |
|-----------------------|-------------------------------------|-------------------------------|---------------------------------------------------------------------------|
| **Time**             | 1 second                           | 1,000 milliseconds (ms)       | Useful for latency calculations.                                         |
|                       | 1 day                              | ~100,000 seconds              | Rounded up for easier estimation.                                        |
| **Data Size**        | 1 kilobyte (KB)                    | 10<sup>3</sup> bytes          | ~1,000 bytes (thousand).                                                 |
|                       | 1 megabyte (MB)                    | 10<sup>6</sup> bytes          | ~1,000 KB (million).                                                     |
|                       | 1 gigabyte (GB)                    | 10<sup>9</sup> bytes          | ~1,000 MB (billion).                                                     |
|                       | 1 terabyte (TB)                    | 10<sup>12</sup> bytes         | ~1,000 GB (trillion).                                                    |
| **Network**          | Bandwidth of 1 Gbps                | 125 MB/s                      | 1 Gbps = 1,000 Mbps = 125 MB/s (divide by 8 to convert bits to bytes).   |
|                       | Round-trip time (RTT)              | ~100 ms (within a region)     | Assumes low latency within a data center or region.                      |
| **Storage**          | SSD latency                        | ~0.1 ms (100 μs)              | Fast read/write times for SSDs.                                          |
|                       | HDD latency                        | ~10 ms                        | Slower than SSDs but cheaper for bulk storage.                           |
| **Throughput**       | Requests per second (RPS)          | ~1,000 RPS per server         | Depends on server capacity and workload.                                 |
|                       | Queries per second (QPS)           | ~10,000 QPS per database      | Depends on database type and optimization.                               |
| **Memory**           | RAM access time                    | ~100 ns                       | Much faster than disk access.                                            |
|                       | Cache access time (L1)             | ~1 ns                         | Extremely fast access for frequently used data.                          |
| **Users**            | Daily Active Users (DAU)           | ~10% of total users           | Assumes 10% of users are active daily.                                   |
|                       | Monthly Active Users (MAU)         | ~30% of total users           | Assumes 30% of users are active monthly.                                 |
| **Traffic**          | Reads vs. Writes                   | ~90% reads, 10% writes        | Common for read-heavy systems (e.g., social media).                      |
|                       | Peak traffic multiplier            | ~2x to 10x average traffic    | Plan for peak traffic spikes (e.g., Black Friday).                       |
| **Miscellaneous**    | UUID size                          | 128 bits (16 bytes)           | Unique identifier size.                                                  |
|                       | Compression ratio                  | ~2x to 10x                    | Depends on data type (e.g., text compresses better than images).         |

---

### **How to Use This Table**
1. **Estimate Traffic**: Use DAU/MAU and peak traffic multipliers to estimate requests per second.
2. **Calculate Bandwidth**: Convert between bits and bytes to estimate network throughput.
3. **Compare Latencies**: Use SSD/HDD/RAM latencies to decide storage and caching strategies.
4. **Size Data**: Use data size conversions to estimate storage requirements.
5. **Plan for Scale**: Use RPS/QPS estimates to determine the number of servers or databases needed.

---

### **Example Calculation**
- **Scenario**: You’re designing a system with 1 million DAU, and each user makes 10 requests per day.
  - Total requests per day = 1,000,000 × 10 = 10,000,000 requests/day.
  - Requests per second (RPS) = 10,000,000 / 100,000 ≈ 100 RPS.
  - Peak traffic = 100 × 5 (assume 5x multiplier) ≈ 500 RPS.
  - If each server handles 1,000 RPS, you’ll need ~1 server (with room for growth).