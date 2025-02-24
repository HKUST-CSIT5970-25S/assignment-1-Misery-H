[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)

# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: HU,Shuoyu
### Student Id: 21075503
### Email: shuoyu.hu@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    > I use the command `phoronix-test-suite run pts/compress-7zip` to test the CPU . The parameters are default, which mean **Dictionary Size** is 64 MB and the **Threads** is auto-detected by the number of cores.
    >
    > It will output MIPS. Higher values indicate better CPU performance in compression tasks. Reflects both single-threaded and multi-threaded efficiency.

    > I use `phoronix-test-suite run pts/ramspeed`, and use the options below: Copy,Scale,Add,Triad , Average with  Floating Point. **Buffer Size** is default, and each test run 3 times
    >
    > It will output **MB/s (Megabytes per Second)** for each operation (Copy, Scale, Add, Triad, Average).Higher MB/s  means faster memory subsystem (RAM/cache performance).

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size        | CPU performance | Memory performance |
    | ----------- | --------------- | ------------------ |
    | `t2.micro` | Intel Xeon E5-2686 v4 (1 Core), Motherboard: Xen HVM domU (4.11.amazon BIOS), Chipset: Intel 440FX 82441FX PMC, Memory: 1024MB, Disk: 8GB, Graphics: Cirrus Logic GD 5446 <br />Compression Rating:3597<br />Decompression Rating:3094<br /> | Type: Add - Benchmark: Floating Point: 11097.16 <br />Type: Copy - Benchmark: Floating Point:11317.55 <br />Type: Scale - Benchmark: Floating Point:9991.64 <br />Type: Triad - Benchmark: Floating Point:11317.55 <br />Type: Average - Benchmark: Floating Point:1275.04 |
    | `t2.medium`  | Intel Xeon E5-2686 v4 (2 Cores), Motherboard: Xen HVM domU (4.11.amazon BIOS), Chipset: Intel 440FX 82441FX PMC, Memory: 4096MB, Disk: 8GB, Graphics: Cirrus Logic GD 5446 <br />Compression Rating:9674<br />Decompression Rating:5718 | Type: Add - Benchmark: Floating Point: 19518.21 <br />Type: Copy - Benchmark: Floating Point:19663.41 <br />Type: Scale - Benchmark: Floating Point:17341.36 <br />Type: Triad - Benchmark: Floating Point:20544.01 <br />Type: Average - Benchmark: Floating Point:19117.02 |
    | `c5d.large` | Intel Xeon Platinum 8275CL (1 Core / 2 Threads), Motherboard: Amazon EC2 c5d.large (1.0 BIOS), Chipset: Intel 440FX 82441FX PMC, Memory: 4096MB, Disk: 9GB Amazon Elastic Block Store + 50GB Amazon EC2 NVMe Instance Storage, Graphics: simpledrmdrmfb, Network: Amazon Elastic  <br />Compression Rating:7922<br />Decompression Rating:5196 | Type: Add - Benchmark: Floating Point: 14221.97 <br />Type: Copy - Benchmark: Floating Point:14458.44 <br />Type: Scale - Benchmark: Floating Point:14177.57 <br />Type: Triad - Benchmark: Floating Point:14249.28 <br />Type: Average - Benchmark: Floating Point:14290.89 |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.
    
    **CPU Performance:**
    
    **`t2.micro` → `t2.medium`**: Doubling vCPUs (1→2 cores) doubles compression performance (3597→9674) and nearly doubles decompression (3094→5718), showing strong scaling with vCPU count.
    
    **`t2.medium` vs. `c5d.large`**: Despite `c5d.large` having newer hardware (Intel Xeon Platinum), its single core/2 threads underperforms `t2.medium`’s 2 cores (7922 vs. 9674 for compression). 
    
    **Memory Performance:**
    
    **`t2.micro` → `t2.medium`**: Quadrupling memory (1GB→4GB) doubles memory bandwidth (e.g., Copy: 11317→19663 MB/s), indicating memory resources directly impact performance.
    
    **`t2.medium` vs. `c5d.large`**: Both have 4GB RAM, but `t2.medium`’s 2 cores achieve almost 35% higher memory bandwidth than `c5d.large` (e.g., Triad: 20544 vs. 14249 MB/s). 

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps)     | RTT (ms) |
    | ------------------------- | ------------------ | -------- |
    | `t3.medium` - `t3.medium` | 4667.9415006081435 | 0.212    |
    | `m5.large` - `m5.large`   | 9948.786534941191  | 0.155    |
    | `c5n.large` - `c5n.large` | 23781.741074493093 | 0.119    |
    | `t3.medium` - `c5n.large` | 4848.531994319033  | 0.730    |
    | `m5.large` - `c5n.large`  | 9950.191641263234  | 0.711    |
    | `m5.large` - `t3.medium`  | 4841.544636796586  | 0.307    |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

    1. Same-Type Instances:
       - Network performance scales with instance tier: `c5n.large` (25 Gbps-capable) > `m5.large` > `t3.medium`.
       - Minimal RTT (0.1–0.2 ms) due to colocation within the same AWS data center.
    2. Cross-Type Instances:
       - **TCP bandwidth** is bottlenecked by the weaker instance (e.g., `t3.medium` limits `c5n.large` to ~5 Gbps).
       - **RTT increases** (0.3–0.7 ms) due to network path complexity between differently optimized instances.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps)    | RTT (ms) |
    | ------------------------- | ----------------- | -------- |
    | N. Virginia - Oregon      | 1974.820789227    | 61.468   |
    | N. Virginia - N. Virginia | 4788.108205866184 | 0.121    |
    | Oregon - Oregon           | 4787.747440336746 | 0.133    |

    > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.
    
    1. Same-Region Tests:
       - **TCP bandwidth**: about 4.7 Gbps (baseline for `c5.large` instances, close to single-flow TCP limits).
       - **RTT**: <0.15 ms, typical for intra-data-center communication.
    2. Cross-Region Tests:
       - **TCP bandwidth drops by ~60%** (4.7 Gbps → 1.97 Gbps): Limited by public internet backbone and AWS inter-region links.
       - **RTT spikes to ~61 ms**: Reflects physical distance (~4,000 km between N. Virginia and Oregon).
