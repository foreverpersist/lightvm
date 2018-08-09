# Survey - Kata & Gvisor

## Kata

	Based on Qemu/KVM

### Architecture

```
	-----------------------------
	| ------------- ----------- |
	| | Container | | Kata    | |
	| | Workloads | | Daemons | |
	| ------------- ----------- |
	| ------------------------- |
	| |   Customized Kernel   | |
	| ------------------------- |
	|          Qemu VM          |
	-----------------------------
    -----------------------------
    |            KVM            |
    -----------------------------
```

### Components

#### Customized Kernel

	General linux kernel with specific config

#### Container Workloads

	Based on docker images with namespaces (NS[, UTS][, IPC][, PID]) isolation

#### Kata Daemons

	Used to monitor container applications


### Resources

#### CPU

	Native KVM vCPU with linux CPU Scheduling


#### Memory

	Native KVM Memory with linux Memory Cgroup

#### I/O

	9pfs, directly pass through host
	DAX (Directly Access Filesystem)

#### Network

	vhost, directly pass through host


### Features

#### Isolation

	Outer Resources:  NET, I/O, MEM
	Inner Namespaces: NS, UTS, IPC, PID

#### Performance

	Variable vCPU (hotplug)
	Fixed memory
	9pfs I/O
	vhost network

#### Overhead

	VM layer:
		CPU
	    	two-levels CPU Scheduling
	    Memory
	    	fixed memory & two-levels memory management
	    I/O
	    	event notifications for I/O
	    Network
	    	multiple-levels network



## Gvisor

	Based on User Space syscall filtering

### Architecure

```
	---------------
	|  Container  |
	|  Workloads  |
	--------------------------
	| User kernel | FS Proxy |
	--------------------------
	--------------------------
	|       Host Kernel      |
	--------------------------
```

### Components

#### User Kernel

	Intercept and handle syscalls them with customized primitives

#### Container Workloads

	Based on docker images with namespaces (NS[, UTS][, IPC][, PID]) isolation


### Resource

#### CPU

	Native KVM vCPU with Goroutine scheduling (M:N thread mapping)?

#### Memory

	Customized memory mapping like VM?
	No memory management policy (allocate & reclaim policy)

#### I/O

	Customized I/O proxy with 9pfs

#### Network

	Customized network stack


### Features

#### Isolation

	Outer: NET, I/O, MEM
	Innner: [None]?

#### Performance

	Flexbile memory and other physical resources

	Poor Compatibility

#### Overhead

	Syscall:
		Intercept & Emulate
	CPU:
		vCPU scheduling with goroutine scheduling?
	Memory:
		Customized memory mapping
	I/O:
		I/O proxy
	Network:
		Customized network stack