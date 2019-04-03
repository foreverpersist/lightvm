# OSv Startup Process

## Boot

> * 执行bootloader读取磁盘扇区,加载压缩内核,探测物理内存,设置cmdline,mmap_addr, mmap_len
> * 跳转到 OSV_LZKERNEL_BASE + elf_point_header_offset 所写内容的地址处,执行内核解压
> * 跳转到 OSV_KERNEL_BASE + elf_point_header_offset 所写内容的地址出,进入解压后内核入口
> * 设置 elf_header, osv_multiboot_info, __loader_argc, __loader_argv等参数 (此处启用分页)

## Premain

> * 跳转到 premain
> * 初始化early console (写硬件端口)
> * 禁用pic (写硬件端口)
> * 解析 elf_header, 设置TLS数据 (memcpy), 执行 .init.array
> ** dtb - 
> ** console - arch_early_console [variable], mux [varaible]
> ** sort - sort_fault_fixup, sort_memcpy_decoder
> ** cpus - cpus [variable]
> ** fpranges - free_page_ranges [variable]
> ** pt_root - page_table_root [variable]
> ** mempool - mempool::setup -> arch_setup_free_memory
> ** routecache - route_cache::cache [variable], route_cache::cache_mutex [variable]
> ** pagecache - pagecache::setup
> ** threadlist - thread_map [variable]
> ** pthread - tsd_key_mutex [variable], tsd_used_keys [variable], tsd_dtor [variable]
> ** notifiers - cpu::notifier::_notifiers [variable], exit_notifiers [variable], exit_notifiers_lock [variable]
> ** psci - 
> ** acpi - acpi_init_early
> ** vma_list - vma_list [variable]
> ** reclaimer - reclaimer_thread [variable]
> ** sched - smp_init
> ** clock - hyperv_init, setup_kvmclock, setup_xenclock
> ** hpet - hpet_init
> ** tracepoint_base - tracepoint_base::tp_list [variable]
> ** malloc_pools - malloc_pools [variable], s_mark_smp_alllocator_initialized [variable]
> ** idt - idt [variable]

## Main

> * 跳转到main
> * 初始化apic指定的当前CPU
> * 设置GDT(Global Descriptor Table)
> * 设置IDT(Interrupt Descriptor Table)
> * 设置cr4
> * 设置xcr0
> * 初始化FPU
> * 初始化syscall (设置syscall_entry)
> * 创建特殊线程执行 main_cont
> ** 探测firmware,建立对应的线性映射
> ** 创建 s_program 实例
> ** 解析__loader_argc, __loader_argv
> ** 初始化irq
> ** 在CPU上启动idle线程和load_balance线程
> ** 初始化BSD
> ** 初始化VFS
> ** 初始化网络
> ** 创建线程 do_main_thread
> *** 初始化设备驱动
> *** 初始化console设备
> *** 初始化null设备
> *** 初始化randomdev设备
> *** 挂载文件系统
> *** 启动DHCP
> *** 解析 /init 下所有应用命令,通过 application::run() 逐一执行