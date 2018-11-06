OSv设计目标:

> * 支持已有的Linux应用
> * 运行应用更快
> * 启动快
> * 提供更高性能的API

不支持: fork, exec

OSv组件:

> * loader
> * dynamic linker
> * memory management
> * thread scheduler
> * synchronization mechanisms (mutex, RCU)
> * virtual-hardware drivers

OSv半虚拟化:

> * 半虚拟化时钟
> * 半虚拟化NIC (virtio, VMXNET3)
> * 半虚拟化块设备 (virtio, pvscsi)

OSv文件系统

> * zfs
> * modified Adaptative Replacement Cache
> * simple devfs
> * simple procfs

OSv内存管理

> * 虚拟内存 (x86_64长模式,POSIX API: map, unmap)
> * Huge Pages & Transparent Huge Pages-like
> * 不支持Page Eviction

OSv无自选锁

> * 使用普通线程(可sleep, 可被竞争)完成大多数内核工作(中断处理) 
> * 使用无锁算法实现互斥锁
> * 调度器使用per-cpu运行队列和无锁算法

OSv网络

> * 合并receive&send buffer锁
> * 使用等待队列取代交错防止锁
> * 合并Socket和TCP层锁

OSv调度器

> * Per-cpu运行队列
> * Per-cpu load balancer线程 (每秒唤醒10次)
> * N * N传入唤醒队列

> * 线程可抢占,没有应用线程和内核线程的差别
> * 线程可禁止/启动抢占

> * 无周期性心跳
> * 移动平均
> * 使用浮点数
> * O(lgN)
> * 无页表切换,无TLB刷新的上下文切换
> * 对自愿的上下文切换(mutex_wait, wake)不保存FPU
> * 不向空闲CPU发送IPI,而由其主动检查唤醒掩码

OSv API

> * Linux API - multi-process, multi-user
> * Explore virtio rings (unimplemented?)
> * Shrinker (lowmem register)
> * JVM Ballon


CFS

# Select & Update

v = min_v + ...

dv = dt * NICE / w

	Select minimal `v`

	Only update `v` of running thread

# Run

dv = v_next - v

dt = T * w / W     (dv = 0)
dt = dv * w / NICE (dv > 0)

	T = T' * n / 5
	dt >= tau        - avoid excessive context switch
	dt <= T'         - avoid starvation

# New thread

	v = min_v + sched_vslice(T)

# Long sleep thread

	v = min_v - k * T


OSv Network Channels

Background:

中断上下文和应用上下文都共享网络栈各层的数据 (锁和cache竞争)

Solution:

接收 - 使用classfier分发到channel
    - 一个channel对应一个socket
    - 以空间换时间,多channel自己解析网络包?

    * channel如何解析包(依然IP->TCP->socket)?

发送 - TCP层发送操作紧耦合到socket层发送操作,去除TCP锁

    * 假如channel高效,为何发送不使用类似方式?

socket buffer 
    - 全由应用上下文完成收发buffer处理(原来仅做发送处理)

    * 原中断直接操作buffer,现唤醒socket后再操作buffer,确实高效?

interleave writes 
    - 使用等待队列对不同来源包预先分类排队?
    
    * 交错写是什么网络场景?
    * 假设队列是用于分类重排,是否会影响公平性?