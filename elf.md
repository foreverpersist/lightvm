# ELF Format

```
	          ------------------------
	          |      ELF Header      |
	          ------------------------
	          | Program Header Table |
	        / ------------------------
	        | |      .text           | - section
	segment-| |----------------------|
	        | |     .rodata          |
	        \ ------------------------
	          |       ...            |
	          |----------------------|
	          |      .data           |
	          ------------------------
	          | Section Header Table |
	          ------------------------
```

## ELF Header

|          Field            | Purpose |
| :------------------------ | :------ |
| e_ident[EI_MAG0, EI_MAG3] | 魔术数   |
|                           |         |
| e_ident[EI_CLASS]         | 32/64位  |
|                           |         |
| e_ident[EI_DATA]          | 大小端对齐|
|                           |         |
| e_ident[EI_VERSION]       | ELF版本  |
|                           |         |
| e_ident[EI_OSABI]         | ABI架构  |
|                           |         |
| e_ident[EI_ABIVERSION]    | ABI版本  |
|                           |         |
| e_ident[EI_PAD]           | 暂未使用  |
|                           |         |
| e_type                    | ELF文件类型 |
|                           |         |
| e_machine                 | 指令集类型  |
|                           |         |
| e_version                 | 文件版本    |
|                           |         |
| e_entry                   | 进程入口地址 |
|                           |         |
| e_phoff                   | Program Header Table起始地址 |
|                           |         |
| e_shoff                   | Section Header Table起始地址 |
|                           |         |
| e_flags                   | 架构相关标记 |
|                           |         |
| e_ehsize                  | ELF Header大小(52/64B)       |
|                           |
| e_phentsize               | 一个Program Header条目大小    |
|                           |         |
| e_phnum                   | Program Header条目数量       |
|                           |         |
| e_shentsize               | 一个Section Header条目大小    |
|                           |         |
| e_shnum                   | Section Header条目数量        |
|                           |         |
| e_shstrndx                | Section Header Table中的String Table Section索引 |

---

ABI架构

> * System V
> * HP-UX
> * NetBSD
> * Linux
> * GNU Hurd
> * Solaris
> * AIX
> * IRIX
> * FreeBSD
> * Tru64
> * Novell Modesto
> * OpenBSD
> * OpenVMS
> * NonStop Kernel
> * AROS
> * Fenix OS
> * CloudABI

---

ELF文件类型

> * ET_NONE
> * ET_REL     - 可重定位文件
> * ET_EXEC    - 可执行文件
> * ET_DYN     - 共享文件
> * ET_CORE    - 核心文件?
> * ET_LOOS
> * ET_HIOS
> * ET_LOPROC
> * ET_HIPROC

---

指令集类型

> * No specific
> * SPARC
> * x86
> * MIPS
> * PowerPC
> * S390
> * ARM
> * SuperH
> * IA-64
> * x86-64
> * AArch64
> * RISC-V

## Program Header

	一个Program Header描述一个Segment,或者其系统准备程序执行需要的信息
	一个Segment包含一到多个Section
	Program Header仅对可执行文件和共享文件有意义

|  Field   | Purpose |
| :------- | :------ |
| p_type   | 段类型   |
|          |         |
| p_flags  | 段相关标记(32位)|
|          |         |
| p_offset | 段文件偏移      |      
|          |         |
| p_vaddr  | 段虚拟地址      |
|          |         |
| p_paddr  | 段物理地址(为物理地址相关系统保留) |
|          |         |
| p_filesz | 段文件大小      |
|          |         |
| p_memsz  | 段内存大小(对齐) |
|          |         |
| p_flags  | 段相关标记(64位) |
|          |         |
| p_align  | 对齐(2的冪次)   |
| (End)    |         |

---

段类型

> * PT_NULL
> * PT_LOAD      - 可装载段
> * PT_DYNAMIC   - 动态链接信息
> * PT_INTERP    - 解释器的路径(仅对可执行文件有意义)
> * PT_NOTE      - 注释段
> * PT_SHLIB     - 保留段
> * PT_PHDR      - Program Header Table段
> * PT_LOOS      -
> * PT_HIOS      -
> * PT_LOPROC    -
> * PT_HIPROC    -
> * PT_GNU_STACK - 

## Section Header

	Section包含链接和重定位的重要数据

|     Field    | Purpose |
| :----------- | :------ |
| sh_name      | 节名称(一个String Table Section的索引) |
|              |         |
| sh_type      | 节类型   |
|              |         |
| sh_flags     | 节标志   |
|              |         |
| sh_addr      | 节虚拟地址 |
|              |         |
| sh_offset    | 节文件偏移 |
|              |         |
| sh_size      | 节文件大小 |
|              |         |
| sh_link      | Section Header Table的索引链接 |
|              |         |
| sh_info      | 节附加信息 |
|              |         |
| sh_addralign | 节对齐约束 |
|              |         |
| sh_entsize   | 含表项的节的每项大小 |

---

节类型

> * SHT_NULL
> * SHT_PROGBITS  - 程序定义的信息,由程序解释
> * SHT_SYMTAB    - 符号表,一般用于链接编辑/动态链接
> * SHT_STRTAB    - 字符串表
> * SHT_RELA      - 重定位表项,有补齐内容
> * SHT_HASH      - 符号哈希表,参与动态链接的目标必须包含一个
> * SHT_DYNAMIC   - 动态链接信息
> * SHT_NOTE      - 注释节
> * SHT_NOBITS    - 不占用文件空间
> * SHT_REL       - 重定位表项,无补齐内容
> * SHT_SHLIB     - 保留节
> * SHT_DYNSYM    - 动态链接符号表最小集合(节省空间)
> * SHT_LOOS      -
> * SHT_HIOS      -
> * SHT_LOPROC    -
> * SHT_HIPORC    -
> * SHT_LOUSER    - 应用程序索引下界
> * SHT_HIUSER    - 应用程序索引上界

---

节标志

> * SHF_WRITE
> * SHF_ALLOC
> * SHF_EXECINSTR
> * SHF_MERGE
> * SHF_STRINGS
> * SHF_INFO_LINK
> * SHF_LINK_ORDER
> * SHF_OS_NONCONFORMING
> * SHF_GROUP
> * SHF_TLS
> * SHF_MASKOS
> * SHF_MASKPROC
> * SHF_ORDERED
> * SHF_EXCLUDE

---

节名称

	以`.`开头的节名称是系统保留的

|    Name   |     Type     |       Flags       | Purpose |
| :-------- | :----------- | :---------------- | :------ |
| .bss      | SHT_NOBITS   | ALLOC + WRITE     | 初始化全0   |
|           |              |                   |         |
| .comment  | SHT_PROGBITS |                   | 版本控制信息 |
|           |              |                   |         |
| .data     | SHT_PROGBITS | ALLOC + WRITE     | 初始化的数据 |
| .datal    | SHT_PROGBITS |                   |            |
|           |              |                   |         |
| .debug    | SHT_PROGBITS |                   | 调试信息    |
|           |              |                   |         |
| .dynamic  | SHT_DYNAMIC  |                   | 动态链接信息 |
|           |              |                   |         |
| .dynstr   | SHT_STRTAB   | ALLOC             | 动态链接字符串 |
|           |              |                   |         |
| .dynsym   | SHT_DYNSYM   | ALLOC             | 动态链接符号表 |
|           |              |                   |         |
| .fini     | SHT_PROGBITS | ALLOC + EXECINSTR | 进程中止代码一部分   |
|           |              |                   |         |
| .got      | SHT_PROGBITS |                   | 全局偏移表 |
|           |              |                   |         |
| .hash     | SHT_HASH     | ALLOC             | 符号哈希表 |
|           |              |                   |         |
| .init     | SHT_PROGBITS | ALLOC + EXECINSTR | 进程初始化代码一部分 |
|           |              |                   |         |
| .interp   | SHT_PROGBITS |                   | 解释器路径 |
|           |              |                   |         |
| .line     | SHT_PROGBITS |                   | 符号调试的行号信息   |
|           |              |                   |         |
| .note     | SHT_NOTE     |                   | 注释信息   |
|           |              |                   |         |
| .plt      | SHT_PROGBITS |                   | 过程链接表 |
|           |              |                   |         |
| .relname  | SHT_REL      |                   | 重定位信息,`name`根据重定位节区给定 |
|           |              |                   |         |
| .relaname | SHT_RELA     |                   |           |
|           |              |                   |         |
| .rodata   | SHT_PROGBITS | ALLOC             | 只读数据   |
| .rodatal  | SHT_PROGBITS |                   |           |
|           |              |                   |         |
| .shstrtab | SHT_STRTAB   |                   | 包含节名称 |
|           |              |                   |         |
| .strtab   | SHT_STRTAB   |                   | 字符串表   |
|           |              |                   |         |
| .symtab   | SHT_SYMTAB   |                   | 符号表    |
|           |              |                   |         |
| .text     | SHT_PROGBITS | ALLOC + EXECINSTR | 可执行指令 |

## String Table

	字符串表包含以NULL结尾的字符序列

## Symbol Table

	符号表包含用来定位,重定位程序中的符号定义和引用信息

|   Field  | Purpose | 
| :------- | :------ |
| st_name  | 符号名的字符串表索引    |
|          |         |
| st_value | 符号取值: 绝对值,地址等 |
|          |         |
| st_size  | 相关的尺寸大小         |
| st_other |         |
| st_info  | 符号的类型和绑定信息    |
|          |         |
| st_other |         |
|          |         |
| st_shndx | 相关的Section Header Table索引 |

---

符号类型和绑定信息

绑定类型

> * STB_LOCAL   - 局部符号
> * STB_GLOBAL  - 全局符号
> * STB_WEAK    - 弱符号,类似全局符号,定义优先级较低
> * STB_LOPROC
> * STB_HIPROC

符号类型st_type

> * STT_NOTYPE
> * STT_OBJECT
> * STT_FUNC     - 引用函数时,链接器会自动创建过程链接表项
> * STT_SECTION
> * STT_FILE
> * STT_LOPROC
> * STT_HIPROC

## Relocation Entries

	重定位即连接符号引用和符号定义的过程

|   Field  |               Purpose             |
| :------- | :-------------------------------- |
| r_offset | 重定位写入的位置(节区偏移或虚拟地址)     |
|          |                                   |
| r_info   | 重定位符号表索引及重定位类型            |
|          |                                   |
| r_addend | 常量补齐,用于计算被填充到可重定位字段数值 |

## Dynamic Tags

	.dynamic节包含一系列动态连接信息的结构

| Field |        Purpose       |
| :---- | :------------------- |
| d_tag | 标记,控制d_un的解释方式 |
|       |                      |
| d_un  | 联合体(d_val, d_ptr)  |

标记类型

> * DT_NULL
> * DT_NEEDED    - 一个需要的动态库名称在字符串表的偏移
> * DT_PLTRELSZ  - 过程链接表对应重定位项的大小
> * DT_PLTGOT    - 过程链接表或处理器相关的全局表的地址
> * DT_HASH      - 符号哈希表地址
> * DT_STRTAB    - 字符串表地址
> * DT_SYMTAB    - 符号表地址
> * DT_RELA      - 可重定位表地址
> * DT_RELASZ    - 可重定位表大小
> * DT_RELAENT   - 可重定位表项大小
> * DT_STRSZ     - 字符串表大小
> * DT_SYMENT    - 一个符号表项大小
> * DT_INIT      - 初始函数地址
> * DT_FINI      - 中止函数地址
> * DT_SONAME    - 此共享对象名在字符串表的偏移
> * DT_RPATH     - 共享库搜索路径在字符串表的偏移
> * DT_SYMBOLIC  - 警告链接器执行前先搜索此共享对象
> * DT_REL       - 可重定位表地址(无补齐)
> * DT_RELSZ     - 可重定位表大小(无补齐)
> * DT_RELENT    - 可重定位表项大小(无补齐)
> * DT_PLTREL    - 重定位类型
> * DT_DEBUG
> * DT_TEXTREL   - 指示重定位表包含不可写段
> * DT_JMPREL    - 只与过程连接表相关的重定位表地址
> * DT_BIND_NOW  - 通知链接器移交控制权给可执行程序前先处理重定位
> * DT_RUNPATH   - 共享库搜索路径在字符串表的偏移
> * DT_LOPROC
> * DT_HIPROC

## GOT & PLT

	GOT(Global Offset Table) - 全局偏移表

```
	-----------------------
	|                     |  - <特殊项>
	-----------------------
	|  shared_libs_link   |  - 加载的共享库形成的链表地址
	-----------------------
	| _dl_runtime_resolve |  - _dl_runtime_resolve地址,用于找到某个符号的地址写入GOT项中
	-----------------------
	|     function1       |  - 某个符号的地址
	-----------------------
	|     function2       |
	-----------------------
	|     .........       |
	-----------------------
```

	PLT(Procedure Link Table) - 过程链接表

```
	---------------
	| push GOT[1] |
	| jmp  GOT[2] |
	|             |
	---------------
	|             |
	|     ...     |
	|             |
	---------------
	| jmp  GOT[x] |
	| push n      |
	|     ...     |
	---------------
	|             |
	|     ...     |
	|             |
	---------------
```

## Notes

	注释信息大量应用于核心文件(ET_CORE),许多项目也会定义自己的扩展
	Note节每一项后紧跟一个由n_namesz定义的name域和一个有n_descsz定义的descriptor域

|   Field  |    Purpose    |
| :------- | :------------ |
| n_namesz | name大小       |
|          |               |
| n_descsz | descriptor大小 |
|          |               |
| n_type   | 类型           |
|

类型
(e_type = ET_CORE)
> * NT_PRSTATUS
> * NT_FRREGSET
> * NT_PRXREG
> * NT_TASKSTRUCT
> * NT_PLATFORM
> * NT_AUXV
> * NT_GWINDOWS
> * NT_ASRS
> * NT_PSTATUS
> * NT_PSINFO
> * NT_PRCRED
> * NT_UTSNAME
> * NT_LWPSTATUS
> * NT_LWPSINFO
> * NT_PRFPXREG
> * NT_SIGINFO
> * NT_FILE
> * NT_PRXFPREG
> * NT_PPC_VMX
> * NT_PPC_SPE
> * NT_PPC_VSX
> * NT_386_TLS
> * NT_386_IOPERM
> * NT_X86_XSTATE
> * NT_S390_HIGH_GRPS
> * NT_S390_TIMER
> * NT_S390_TODCMP
> * NT_S390_TODPREG
> * NT_390_CTRS
> * NT_390_PREFIX
> * NT_390_LAST_BREAK
> * NT_S390_SYSTEM_CALL
> * NT_S390_TDB
> * NT_S390_TDB
> * NT_ARM_VFP
> * NT_ARM_TLS
> * NT_ARM_HW_BREAK
> * NT_ARM_HW_WATCH
> * NT_ARM_SYSTEM_CALL
(n_name = GNU)
> * NT_GNU_ABI_TAG
> * NT_GNU_HWCAP
> * NT_GNU_BUILD_ID
> * NT_GNU_GOLD_VERSION
(e_type != ET_CORE)
> * NT_VERSION
> * NT_ARCH