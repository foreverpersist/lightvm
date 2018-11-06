# Experiment

	Test Gvisor and Kata


## Build

### Gvisor

	Update Kernel

```
$ # Config new kernel
$ make menuconfig
$ wget https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh
$ ./check-config $BUILD_PATH/.config
$ make -j9
$ sudo make modules_install install
```

	Clone

```
$ git clone https://gvisor.googlesource.com/gvisor
```

	Build

```
$ bazel build runsc
$ sudo cp ./bazel-bin/runsc/linux_amd64_pure_stripped/runsc /usr/local/bin
```

### Kata

	Clone & Build Components

	runtime:

```
$ go get -d -u github.com/kata-containers/runtime
$ cd $GOPATH/src/github.com/kata-containers/runtime
$ make && sudo -E PATH=$PATH make install
```
	proxy:

```
$ go get -d -u github.com/kata-containers/proxy
$ cd $GOPATH/src/github.com/kata-containers/proxy && make && sudo make install
```

	shim:

```
$ go get -d -u github.com/kata-containers/shim
$ cd $GOPATH/src/github.com/kata-containers/shim && make && sudo make install
```

	Build image and initrd

	agent:

```
$ go get -d -u github.com/kata-containers/agent
$ cd $GOPATH/src/github.com/kata-containers/agent && make
```

	osbuilder:

```
$ go get -d -u github.com/kata-containers/osbuilder
```

	image:

```
$ export ROOTFS_DIR=${GOPATH}/src/github.com/kata-containers/osbuilder/rootfs-builder/rootfs
$ sudo rm -rf ${ROOTFS_DIR}
$ cd $GOPATH/src/github.com/kata-containers/osbuilder/rootfs-builder
$ script -fec 'sudo -E GOPATH=$GOPATH USE_DOCKER=true ./rootfs.sh ${distro}'
$ # <option function="Add a custom agent to image">
$ sudo install -o root -g root -m 0550 -t ${ROOTFS_DIR}/bin ../../agent/kata-agent
$ sudo install -o root -g root -m 0440 ../../agent/kata-agent.service ${ROOTFS_DIR}/usr/lib/systemd/system/
$ sudo install -o root -g root -m 0440 ../../agent/kata-containers.target ${ROOTFS_DIR}/usr/lib/systemd/system/
$ # </option>
$ cd $GOPATH/src/github.com/kata-containers/osbuilder/image-builder
$ script -fec 'sudo -E USE_DOCKER=true ./image_builder.sh ${ROOTFS_DIR}'
$ commit=$(git log --format=%h -1 HEAD)
$ date=$(date +%Y-%m-%d-%T.%N%z)
$ image="kata-containers-${date}-${commit}"
$ sudo install -o root -g root -m 0640 -D kata-containers.img "/usr/share/kata-containers/${image}"
$ (cd /usr/share/kata-containers && sudo ln -sf "$image" kata-containers.img)
```

	initrd:

```
$ export ROOTFS_DIR="${GOPATH}/src/github.com/kata-containers/osbuilder/rootfs-builder/rootfs"
$ sudo rm -rf ${ROOTFS_DIR}
$ cd $GOPATH/src/github.com/kata-containers/osbuilder/rootfs-builder
$ script -fec 'sudo -E GOPATH=$GOPATH AGENT_INIT=yes USE_DOCKER=true ./rootfs.sh ${distro}'
$ # <option function="Add a custom agent to initrd">
$ sudo install -o root -g root -m 0550 -T ../../agent/kata-agent ${ROOTFS_DIR}/sbin/init
$ cd $GOPATH/src/github.com/kata-containers/osbuilder/initrd-builder
$ script -fec 'sudo -E AGENT_INIT=yes USE_DOCKER=true ./initrd_builder.sh ${ROOTFS_DIR}'
$ commit=$(git log --format=%h -1 HEAD)
$ date=$(date +%Y-%m-%d-%T.%N%z)
$ image="kata-containers-initrd-${date}-${commit}"
$ sudo install -o root -g root -m 0640 -D kata-containers-initrd.img "/usr/share/kata-containers/${image}"
$ (cd /usr/share/kata-containers && sudo ln -sf "$image" kata-containers-initrd.img)
```

	Install guest kernel image

```
$ go get github.com/kata-containers/tests
$ cd $GOPATH/src/github.com/kata-containers/tests/.ci
$ kernel_arch="$(./kata-arch.sh)"
$ kernel_dir="$(./kata-arch.sh --kernel)"
$ tmpdir="$(mktemp -d)"
$ pushd "$tmpdir"
$ curl -L https://raw.githubusercontent.com/kata-containers/packaging/master/kernel/configs/${kernel_dir}_kata_kvm_4.14.x -o .config
$ kernel_version=$(grep "Linux/[${kernel_arch}]*" .config | cut -d' ' -f3 | tail -1)
$ kernel_tar_file="linux-${kernel_version}.tar.xz"
$ kernel_url="https://cdn.kernel.org/pub/linux/kernel/v$(echo $kernel_version | cut -f1 -d.).x/${kernel_tar_file}"
$ curl -LOk ${kernel_url}
$ tar -xf ${kernel_tar_file}
$ mv .config "linux-${kernel_version}"
$ pushd "linux-${kernel_version}"
$ curl -L https://raw.githubusercontent.com/kata-containers/packaging/master/kernel/patches/0001-NO-UPSTREAM-9P-always-use-cached-inode-to-fill-in-v9.patch | patch -p1
$ make ARCH=${kernel_dir} -j$(nproc)
$ kata_kernel_dir="/usr/share/kata-containers"
$ kata_vmlinuz="${kata_kernel_dir}/kata-vmlinuz-${kernel_version}.container"
$ [ $kernel_arch = ppc64le ] && kernel_file="$(realpath ./vmlinux)" || kernel_file="$(realpath arch/${kernel_arch}/boot/bzImage)"
$ sudo install -o root -g root -m 0755 -D "${kernel_file}" "${kata_vmlinuz}"
$ sudo ln -sf "${kata_vmlinuz}" "${kata_kernel_dir}/vmlinuz.container"
$ kata_vmlinux="${kata_kernel_dir}/kata-vmlinux-${kernel_version}"
$ sudo install -o root -g root -m 0755 -D "$(realpath vmlinux)" "${kata_vmlinux}"
$ sudo ln -sf "${kata_vmlinux}" "${kata_kernel_dir}/vmlinux.container"
$ popd
$ popd
$ rm -rf "${tmpdir}"
```

	Update docker configuration

```
$ dir=/etc/systemd/system/docker.service.d
$ file="$dir/kata-containers.conf"
$ sudo mkdir -p "$dir"
$ sudo test -e "$file" || echo -e "[Service]\nType=simple\nExecStart=\nExecStart=/usr/bin/dockerd -D --default-runtime runc" | sudo tee "$file"
$ sudo grep -q "kata-runtime=" $file || sudo sed -i 's!^\(ExecStart=[^$].*$\)!\1 --add-runtime kata-runtime=/usr/local/bin/kata-runtime!g' "$file"
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

	Build qemu-lite

```
$ git clone https://github.com/kata-containers/qemu.git qemu-lite
$ cd qemu-lite
$ curl -OL https://raw.githubusercontent.com/kata-containers/packaging/master/scripts/configure-hypervisor.sh
$ chmod +x configure-hypervisor.sh
$ ./configure-hypervisor.sh qemu-lite | xargs ./configure --target-list=x86_64-softmm
$ make && sudo make install
$ sudo mv /usr/local/bin/qemu-system-x86_64 /usr/bin/qemu-lite-system-x86_64
```

## Test

	Run

```
	docker run --rm --runtime=kata-runtime/runsc/runc ubuntu:14.04 bash
```

### Start Time

```
$ cat hello.sh
echo "Hello World!"
```

```
$ time docker run --rm --runtime=kata-runtime/runsc/runc treeder/hello:sh
$ time ./hello.sh
```

|            | real(ms) | user(ms) | sys(ms) |
| :--------- | :------: | :------: | :-----: |
| Kata       | 2240      | 11      | 5       |
| Gvisor     |
| Gvisor-KVM | 1086      | 16      | 6       |
| Docker     | 1044      | 16      | 6       |
| Host       | 2         | 1       | 1       |

### Memory Usage

```
$ free -m
```

|            | Used(M)  | Total(M) |
| :--------- | :----:   | :------: |
| Kata       | 229      | 1995     |
| Gvisor     |          |          |
| Gvisor-KVM | 37       | 2048     |
| Docker     | < 59     | 7856     |

### I/O Test

#### dd

write

```
$ dd if=/dev/zero of=swapfile bs=256 count=1M
```

read

```
$ dd if=swapfile of=/dev/null bs=256 count=1M
```

|            | Status   | Write(MB/s) | Read(MB/s) |
| :--------- | :----:   | :-----:     | :--------: |
| Kata       | Stable   | 12.6        | 15.3       | 
| Gvisor     | Unstable | [13.7]      |            |
| Gvisor-KVM | Stable?  | [23.1] 22.3 | 29.7       |
| Docker     | Stable   | [129] 126   | 260(C)     |
| Host       | Stable   | [144] 138   | 305(C) 150 |


#### fio

read
	
```
$ fio -filename=swapfile -direct=1 -iodepth 1 -thread -rw=read -ioengine=psync -bs=16k -size=256M -numjobs=8 -runtime=60 -group_reporting -name=mytest
```

write

```
$ fio -filename=swapfile -direct=1 -iodepth 1 -thread -rw=write -ioengine=psync -bs=16k -size=256M -numjobs=8 -runtime=60 -group_reporting -name=mytest
```

randread

```
$ fio -filename=swapfile -direct=1 -iodepth 1 -thread -rw=randread -ioengine=psync -bs=16k -size=256M -numjobs=8 -runtime=60 -group_reporting -name=mytest
```

randwrite

```
$ fio -filename=swapfile -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=16k -size=256M -numjobs=8 -runtime=60 -group_reporting -name=mytest
```

randrw
```
$ fio -filename=swapfile -direct=1 -iodepth 1 -thread -rw=randrw -rwmixread=70 -ioengine=psync -bs=16k -size=256M -numjobs=8 -runtime=60 -group_reporting -name=mytest
```

|            | Status      | R(MB/s) | W(MB/s) | RR(MB/s) | RW(MB/s) | RRW(MB/s)     |
| :--------- | :----:      | :-----: | :-----: | :------: | :------: | :-------:     |
| Kata       |             | 908.2(C)| 871.1(C)| 886.2(C) | 858.8(C) | 634.0,271.2(C)|
| Gvisor     | Unsupported |         |         |          |          |               |
| Gvisor-KVM | Unsupported |         |         |          |          |               |
| Docker     | Stable      | 112.7(E)| 30.3    | 2.86     | 5.04     | 2.03,0.83     |
| Host       | Stable      |  44.1   | 28.3    | 2.94     | 5.07     | 2.05,0.83     |
[64.5] 
[108.1]


### Network Test

```
$ wget 202.114.10.146:9999/android-4.4.tar.gz

```

```
$ curl -OL 202.114.6.210/go1.9.2.linux-amd64.tar.gz
```

```
$ iperf -c 202.114.10.146 -p 9999 -t 60 -l 8k
```

#### To Containers


| :--------- | wget(MB/s) | curl(MB/s) | iperf(MB/s) |
| Kata       | 10.9       | 11.0       | 92.6        |
| Gvisor     |
| Gvisor-KVM | 9.11       | 9.48       | 93.7        |
| Docker     | 11.0       | 10.8       | 93.9        |
| Host       | 10.8       | 11.0       | 92.9        |

#### From Containers

| :--------- | wget(MB/s) | curl(MB/s) | iperf(MB/s) |
| Kata       | 10.9       | 11.1       | 91.4  91.0  |
| Gvisor     |
| Gvisor-KVM | 11.2       | 11.1       | 68.3  65.0  |
| Docker     | 11.2       | 11.1       | 92.1  92.6  |
| Host       | 11.2       | 11.1       | 93.9  90.6  |


## Initial Conclusion

Storage:
	Kata > Gvisor >= Docker

Start Time:
	Kata > Gvisor >= Docker

Memory Usage:
	Kata > Gvisor >= Docker

I/O Performance:
	Kata < Gvisor < Docker

Network Performance:
	Docker ?=? Kata > Gvisor(host)