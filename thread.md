# Create New Thread

_func 构造赋值
_runtime 默认值priority_default
_detached_state 新实例
_attr 赋值
_migration_lock_counter 默认值0
_pinned 默认值false
_id 默认值0
_cleanup 赋值lamda
_app 构造赋值
_joiner 默认值nullptr

_app_runtime 从当前app赋值
_pt_root 从当前app赋值

setup_tcb
	分配tcb
	拷贝内核的tls与当前app的tls内容
	分配tcb的小系统调用栈
	
_tls
	记录tls各部分的地址(此时均在tcb内)

_id 分配未使用id

s_current 赋值this

init_stack
	确保分配栈空间,默认65536字节
	设置rbp为this
	设置rip为thread_main
	设置rsp为栈空间最高地址
	设置exception_stack为线程私有_arch.exception_stack

_detach_state 条件赋值

_attr.name 拷贝字符串赋值

# Start New Thread

_detached_state
	设置_cpu
	设置percpu_base为_cpu->percpu_base
	设置current_cpu为_cpu
	设置st为waiting

# Wake A Thread

原子设置_detached_state->st为waking
将线程加入当前_detached_state->_cpu的incoming_wakups队列
当前cpu非目标cpu,让其发送处理器间中断;否则置位need_reschedule
