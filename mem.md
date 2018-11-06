# Memory Layout

	Linear Address (Virtual Address)

```
	---------  0x 0000 0000 0000 0000
	|       |
	---------  0x 0000 0000 0020 0000  kernel_base    --\
	|       |                                           |- Kernel (Core ELF) - 22M
	---------  0x 0000 0000 0180 0000  lzkernel_base  --/
	|       |
	|--------  0x 0000 1000 0000 0000  program_base  --\
	|       |                                          |- s_program - 8G
	|-------|  0x 0000 1002 0000 0000  --\           --X
	|       |                             |- Program   |
	|-------|  0x 0000 1004 0000 0000  --/             |
	|       |                                          |- ELF Namespaces(max: 32) - 256G
	|       |  ......................                  |
	|       |                                          |
	|-------|  0x 0000 1040 0200 0000                --/
	|       |
	---------  0x ffff 8000 0000 0000  phys_mem  --\
	|       |                                      |- Main Area - 16T
	---------  0x ffff 9000 0000 0000            --X
	|       |                                      |- Page Area - 16T
	---------  0x ffff a000 0000 0000            --X
	|       |                                      |- Mempool Area - 16T
	---------  0x ffff b000 0000 0000            --X
	|       |                                      |- Debug Area - 80T
	---------  0x ffff ffff ffff ffff            --/
```

---

	Linux Linear Address (Virtual Address)

```
	---------  0x 0000 0000 0000 0000  --\
	|       |                            |- Reserved (Catch Exceptions of NULL/Little Pointers)
	---------                          --X
	|       |                            |- Text Segment
	---------                          --X
	|       |                            |- Data Segment
	---------                          --X
	|       |                            |- [Random]
	---------                          --X
	|       |                            |- Heap
	---------                          --/   \/
	|       |
	---------                          --\
	|       |                            |- Memory Mapping Segment
	---------                          --/
	|       |                            |- [Random]
	---------                          --/
	|       |
	---------                          --\   /\
	|       |                            |- Stack
	---------                          --X
	|       |                            |- [Random]
	---------                          --X
	|       |                            |- Command-line Arguments
	---------                          --X
	|       |                            |- Environment Variables
	|       |                          --/
	---------
	|       |
	---------  0x 0000 7fff ffff ffff
	|       |
	|       |  -- Unused Space
	|       |
	---------  0x ffff 8000 0000 0000  --\
	|       |                            |
	|       |                            |- Kernel Space - 128T
	|       |                            |
	---------  0x ffff ffff ffff ffff  --/
```

## Memory Mapping

```
	        0       phys_mem

	        |          |   Main   |   Page   |  Mempool |  Debug   |
	        --------------------------------------------------------
	OSv VA  | | Core | |.|      |.|.|      |.|.|      |.|.|      |.|
	        --------------------------------------------------------
	             |      |________|_|________|_|________|_|        |
	             |      |        |__________|__________|__________|
	             V      V        V
	        --------------------------------------------------------
	OSv PA  | | Core | |.|      |.|
	        --------------------------------------------------------
	         ____|______|        |
	         |   |    ___________|
	         |   |    |
	         V   V    V
	        --------------------------------------------------------
	HOST VA |.|......|.|
	        --------------------------------------------------------
```