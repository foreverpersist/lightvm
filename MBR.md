# MBR

	主引导记录(Master Boot Record) - 计算机开机后访问磁盘时所必须读取的首个扇区

## Structrue

| Addr | Length | Description |
| :--:  | :----: | :---------: |
| 0x 0000 | 440 | 代码区 |
| 0x 01b8 | 4 | 选用磁盘标志 | 
| 0x 01bc | 2 | 一般为空值 0x0000 |
| 0x 01be | 64 | 标准MBR分区规划 |
| 0x 01fe | 2 | 有效标志 |

## Startup Process

> * BIOS上电自检,对系统硬件进行检查
> * 加载启动磁盘的首个扇区MBR到0x7c00~0x7e00
> * 检查MBR有效标志,跳转到0x7c00将控制权交给MBR
