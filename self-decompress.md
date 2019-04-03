# Self-decompress

	自解压 - 在压缩数据前附带解压代码

## Implementation

	实现方式可能有多种
	
### objcopy

	objcopy将文件从二进制binary转换为ELF目标文件时,会自动产生一些特殊的符号:

> * _binary_<objfile>_start - 目标文件链接时的起始处
> * _binary_<objfile>_end   - 目标文件链接时的结束处
> * _binary_<objfile>_size  - 目标文件链接时的大小

	<objfile>是转换时输入的文件名,文件名中的`-`, `_`, `.`均会统一转换成`_`

	使用这些特殊符号的虚存地址(使用时取地址&)可以定位目标文件载入内存后的虚拟地址区间

	Example:

```
$ echo "Hello World" > arbitrary_file
$ objcopy -B i386:x86-64 -I binary -O elf64-x86-64 arbitrary_file arbitrary_file.o
$
$ cat -> main.c
#include <stdio.h>

extern char _binary_arbitrary_file_start;
extern char _binary_arbitrary_file_end;
extern char _binary_arbitrary_file_size;

int main(int argc, char *argv[])
{
	size_t sz = (size_t) &_binary_arbitrary_file_size;
	char *str = &_binary_arbitrary_file_start;
	str[sz-1] = 0;
	printf("String: %s\n", str);

	return 0;
}
$
$ gcc main.c arbitrary_file.o -o main
$ ./main
String: Hello world
```

	利用objcopy产生的特殊符号,即可将压缩文件与解压代码一起链接,且为解压代码提供压缩文件的内存地址及文件大小等信息
