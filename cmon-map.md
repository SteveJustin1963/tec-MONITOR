System Constants:
RAMSRT:  0800H    RAMEND: 0FFFH
SYSTEM:  0F00H    KEYBUF: 00H
SCAN:    01H      DISPLY: 02H

+---------------------------+
|     RESET (0200H)        |
|   Power-up/Manual Reset  |
+---------------------------+
            |
            v
+---------------------------+
|  Check RAM Test Location |
|    DALLAS (0FFFH)        |
+---------------------------+
            |
            +-----> [Not Ready] ----+
            |                       |
      [RAM Ready]                   |
            |                       |
            v                       |
+---------------------------+       |
|    RESET1                 |      |
|    - Setup Stack         |       |
|    - Init RST Vectors:   |       |
|      RST08: 0FF0H        |       |
|      RST10: 0FF2H        |       |
|      RST18: 0FF4H        |       |
|      RST20: 0FF6H        |       |
|      RST28: 0FF8H        |       |
|      RST38: 0FFAH        |       |
|      RST66: 0FFCH        |       |
+---------------------------+       |
            |                      |
            v                      |
+---------------------------+      |
|      MAIN                 | <----+
|    - Stack = 0F00H       |
|    - ADRESS = 0800H      |
+---------------------------+
            |
            v
+---------------------------+
|         MAIN1            |
|    Update Display Buffer  |
|    DISBUF: 0FD0H         |
+---------------------------+
            |
            v
+---------------------------+
|         MAIN2            |
|      Wait for Key Input  |
|    Check KEYBUF (00H)    |
+---------------------------+
            |
     +------+------+-------+
     |             |       |
     v             v       v
[Hex Keys]    [Function] [Control]
0-F         Keys at     Keys
            FUNTBL      
            |             |       |
     +------+------+     |       |
     |      |      |     |       |
     v      v      v     v       v
GOEXEC   INTELH  TRACE  +/-    MODE
(Jump)   (Load)  (0FF0H) INC    Toggle
                         DEC     (0FD8H)

Display Functions:
SCAND:   Scan Display (01H)
DISADD:  Convert Address to 7-Seg
DISBYT:  Convert Byte to 7-Seg

Register Storage:
PC_REG:  0FE0H    AF_REG: 0FE2H
BC_REG:  0FE4H    DE_REG: 0FE6H
HL_REG:  0FE8H    IX_REG: 0FEAH
IY_REG:  0FECH    SP_REG: 0FEEH

Memory Map:
+---------------------------+
| RST Vectors: 0000H-0066H |
+---------------------------+
| System Jump:  0100H      |
+---------------------------+
| Monitor Code: 0200H+     |
+---------------------------+
| User RAM:     0800H-0FFFH|
+---------------------------+
| System Vars:  0F00H-0FFFH|
|   FUNTBL:     0FB4H      |
|   BAUD:       0FC0H      |
|   KEYTIM:     0FC2H      |
|   DISBUF:     0FD0H      |
|   MODE:       0FD8H      |
|   ADRESS:     0FDAH      |
|   KEYDEL:     0FDCH      |
+---------------------------+
