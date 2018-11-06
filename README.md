# lightvm

	Light VM customized for cloud containers which has only one application

	Run one VM instance for each container

## Targets

	lightvm is designed for cloud containers, only decicates to provide essential and isolated physical resources for application. 

	Isolation, Performance, Applicability are all design targets.

> * Isolation:     equal with tradition VM or Unikernel
> * Performance:   better than tradition VM, close to Unikernel
> * Applicability: equal with Host

## Features

	New OS based on Unikernel-like OS: OSv

### Native Linux Application Support

	Support unmodified applications in Linux

	There is still a limit: following libraries can't be statically linked to applications because they have been compiled to kernel (x64)

> *	libc
> * libm
> * libpthread
> * libdl
> * librt
> * libstdc++
> * libaio
> * libxenstore
> * libcrypt

### Multi-processes Support

	Support multi-processes

	`fork`, `exec` are supported

### Pass-through I/O Support

	Support file sharing between VM and host with VirtFS

## Works

	Optimizing OS for container based on OSv

### Multi-Address Spaces

### IPC

### VirtFS

### Performance Optimization
