---
title: '[Docker] Linux CGroup'
date: 2019-02-28 10:52:39
categories:
  - LINUX
tags:
  - Docker
  - CGroup
---

### 基本概念

Linux CGroup 全称 Linux Control Group， 是 Linux 内核的一个功能，用来限制，控制与分离一个进程组群的资源（如CPU、内存、磁盘输入输出等）。  

主要提供了如下功能：  

- Resource limitation: 限制资源使用，比如内存使用上限以及文件系统的缓存限制。
- Prioritization: 优先级控制，比如：CPU 利用和磁盘 IO 吞吐。
- Accounting: 一些审计或一些统计，主要目的是为了计费。
- Control: 挂起进程，恢复执行进程。  

一些基本的资源子系统：

- Block IO（blkio)：限制块设备（磁盘、SSD、USB 等）的 IO 速率
- CPU Set(cpuset)：限制任务能运行在哪些 CPU 核上
- CPU Accounting(cpuacct)：生成 cgroup 中任务使用 CPU 的报告
- CPU (CPU)：限制调度器分配的 CPU 时间
- Devices (devices)：允许或者拒绝 cgroup 中任务对设备的访问
- Freezer (freezer)：挂起或者重启 cgroup 中的任务
- Memory (memory)：限制 cgroup 中任务使用内存的量，并生成任务当前内存的使用情况报告
- Network Classifier(net_cls)：为 cgroup 中的报文设置上特定的 classid 标志，这样 tc 等工具就能根据标记对网络进行配置
- Network Priority (net_prio)：对每个网络接口设置报文的优先级
- perf_event：识别任务的 cgroup 成员，可以用来做性能分析


### CPU 限制

#### 创建 cgroup

1. 直接在 cgroup 对应的子资源目录下, 用 `mkdir` 创建一个目录

```
[zqq@ /sys/fs/cgroup/cpu]# mkdir test
[zqq@ /sys/fs/cgroup/cpu]# ls test/
cgroup.clone_children  cpuacct.usage         cpuacct.usage_percpu_sys   cpuacct.usage_user  cpu.shares         tasks
cgroup.procs           cpuacct.usage_all     cpuacct.usage_percpu_user  cpu.cfs_period_us   cpu.stat
cpuacct.stat           cpuacct.usage_percpu  cpuacct.usage_sys          cpu.cfs_quota_us    notify_on_release
```

2. 限制 CPU 暂用率, 20000 表示限制到 20%.

```
[zqq@ /sys/fs/cgroup/cpu]# cat test/cpu.cfs_quota_us
-1
[zqq@ /sys/fs/cgroup/cpu]# echo 20000 > cat test/cpu.cfs_quota_us
```

#### 运行测试

1. 首先, 创建一个无限循环的程序:  

```
int main(void)
{
	int i = 0;
	for(;;)
		i++;
	return 0;
}
```

2. 然后, 我们运行这个程序, 可以看到 CPU 已经占用到 100% 了:  

```
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND            
29637 zqq       20   0    4220    632    560 R 100.0  0.0   0:07.58 a.out  
```

3. 现在, 将 a.out 的 PID 写入 `/sys/fs/cgroup/cpu/test/tasks`  

```
[zqq@ /sys/fs/cgroup/cpu]# echo 29637 >> test/tasks
```

4. 现在可以看到 CPU 徘徊在 20% 左右

```
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND            
29637 zqq       20   0    4220    632    560 R  19.9  0.0  11:57.64 a.out 
```

5. 结束 a.out 进程后, test 目录下的 tasks 的 PID 就会被清空.  

#### 移除限制

有两个方法移出限制:  

1. 最简单的, 结束进程即可.

2. 比较友好的方法, 就是将把 pid 写入到根 cgroup 的 tasks 文件即可(`/sys/fs/cgroup/cpu/tasks`).   
   因为每个进程都属于且只属于一个 cgroup, 加入到新的 cgroup 后, 原有关系也就解除了.  
   ```shell  
   [zqq@ /sys/fs/cgroup/cpu]# echo 29637 >> tasks
   ```

3. 删除创建的 cgroup, 需要先将 tasks 都移除:
   ```shell
   [zqq@ /sys/fs/cgroup/cpu]# rmdir test
   ```

**************

### REFERENCES

[1] 左耳朵耗子. DOCKER基础技术：LINUX CGROUP[EB/OL]. (2015-04-17). https://coolshell.cn/articles/17049.html.  
[2] 神仙的仙居. linux cgroups 概述[EB/OL]. (2013-10-23). https://xiezhenye.com/2013/10/linux-cgroups-%E6%A6%82%E8%BF%B0.html.  


