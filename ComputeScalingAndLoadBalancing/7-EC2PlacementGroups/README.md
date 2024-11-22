## EC2 Placement Groups
When you launch a new EC2 instance, the EC2 service attempts to place the instance in such a way that all of your instances are spread out across underlying hardware to minimize correlated failures. You can use placement groups to influence the placement of a group of interdependent instances to meet the needs of your workload. Depending on the type of workload, you can create a placement group using one of the following placement strategies:

Cluster – packs instances close together inside an Availability Zone. This strategy enables workloads to achieve the low-latency network performance necessary for tightly-coupled node-to-node communication that is typical of HPC applications.
Partition – spreads your instances across logical partitions such that groups of instances in one partition do not share the underlying hardware with groups of instances in different partitions. This strategy is typically used by large distributed and replicated workloads, such as Hadoop, Cassandra, and Kafka.
Spread – strictly places a small group of instances across distinct underlying hardware to reduce correlated failures.

### Cluster Placement Group
- Pack Instances close together physically
- Wants to achieve the best performance possible within EC2 (highest throughput and lowest latencies )
- Optimally to launch ALL EC2 all at the same time and use the same type of EC2, allowing AWS to allocate enough capacity
- Eg. When you have 9 instances in a 12 capacity host and you want to double the capacity from 9 to 18 (Problem)
- Have to be launched into the same availability zone, when you pick an AZ when launching EC2 in a placement group, the placement group will be locked to that AZ
- Generally uses the same rack, or even same host
- Allows fast, direct bandwidth to all other instances, allowing to reached 10Gbps instead of 5Gbps, Lowest latency and maximum packets per second is achieved (Assuming Enhanced networking is used)
- tradeoff - If all instances is in one hardware, if the hardware failed, all instances is dead
- Cant's span AZs - ONE AZ ONLY - Locked when launching first instance
- Can span VPC peers - but impacts performance
- Requires a supported instance type
- Use the same type of instance (not mandatory)
- Launch at the same time (not mandatory ... RECOMMENDED)

### Spread 
- Instances are using different underlying hardware, Provides infrastructre isolated
- Ensures the maximum amount of availability and resilience for an application
- Instances are Span multiple AZ and located on separate isolated infrastructure racks
- Separate networking and power supply and rack from any of the instances
- 7 Instances per AZ - Isolated infrastructure Limit - Because each instances is in separated rack
- Not supported for dedicated instances or hosts
- USE CASE: Small number of critical instances that need to be kept separated from each other Eg. Mirror of File Servers, or Domain Controllers

### Partition
- Groups of instances spread apart
- Each group instances is on different hardware
- Designed when you have infrastructure where you have more than 7 instances per AZ but still need the ability to separate those instances
- Now in Partition placement group, you can have max of 7 "partitions" per AZ where each partition has it own rack 
- Each of this partition group, you can launch as many instances inside the group
- If you launch 10 instances into one partition and it fails, you will lose all 10 instances
- If you launch 7 instances and put each instance in each partition, it behaves like a spread placement group
- Designed for huge scale parallel processing system, suitable for topology aware applications such as HDFS, Hbase and cassandra
- You or the topology-aware applications needs to administer the partition placement in order to have high resilience whereas spread placement group will help administer the placement but with the limitation of 7 per AZs.


