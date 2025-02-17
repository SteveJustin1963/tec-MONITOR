```
System Constants:
STARTRAM:   0800H    STARTSTACK: 0FF0H
DISPLAY:    0FF1H    ADDRESS:    0FF7H
MODE:       0FF9H    KEYFLAG:    0FFAH
PORTKEYB:   00H      PORTDIGIT:  01H
PORTSEGS:   02H      PORTSPKR:   01H

+---------------------------+
|      RESET/STARTUP       |
|     STARTMON (0580H)     |
+---------------------------+
            |
            v
+---------------------------+
|  Initialize               |
|  - HL = STARTRAM (0800H) |
|  - SP = STARTSTACK       |
|  - IX = DISPLAY          |
|  - (ADDRESS) = HL        |
|  - MODE = ADDR           |
|  - KEYFLAG = false       |
+---------------------------+
            |
            v
+---------------------------+
|  Initial Display Setup    |
|     DATADISP (00E3H)     |
+---------------------------+
            |
            v
    +-------+--------+
    |                |
+----------+  +-----------+
|ADDRDISP  |  |DATADISP   |
| (00DAH)  |  | (00E3H)   |
+----------+  +-----------+
    |                |
    +-------+--------+
            |
            v
+---------------------------+
|    WRITEDISP (00EAH)     |
|  - Update MODE           |
|  - Show ADDRESS/DATA     |
|  - Set decimal points    |
+---------------------------+
            |
            v
+---------------------------+
|   WRITEDISP2 (006DH)     |
|  - Get key & beep        |
+---------------------------+
            |
     +------+------+
     |             |
     v             v
+----------+ +-----------+
|ADDR Mode | |DATA Mode  |
| Key Hand.| |Key Hand.  |
+----------+ +-----------+
     |             |
     v             v
+---------------------------+
| Key Functions:            |
| ADDRKEY    (0088H)       |
| DATAKEY    (00B7H)       |
| GOADDR     (00C2H)       |
| DECADDR    (00C6H)       |
| INCADDR    (00D0H)       |
+---------------------------+

Display Helper Functions:
GETKEY      (0131H)  - Wait for keypress
SCANDISP    (0140H)  - Update display
HEX2SEGS    (0126H)  - Convert hex to segments
WRITEHEX    (0115H)  - Write hex to display
BEEP        (018EH)  - Sound feedback

Games & Features:
INVADERS    (0320H)  - Space invaders game
NIM         (03E0H)  - Match game
LUNALANDER  (0490H)  - Lunar landing sim
PLAYTUNE    (01B0H)  - Music playback
SHOWTEXT    (0270H)  - Text display
WRITEDISP   (00EAH)  - Display update

Interrupt Vectors:
NMINT       (0066H)  - Non-maskable interrupt
RESTART     (0000H)  - Power-on/Reset vector

Memory Map:
+---------------------------+
| ROM                      |
| 0000H: Reset Vector      |
| 0066H: NMI Vector        |
| 0100H: Main Code         |
| 0320H: Games            |
+---------------------------+
| RAM                      |
| 0800H: User RAM Start    |
| 0FF0H: Stack            |
| 0FF1H: Display Buffer    |
| 0FF7H: ADDRESS          |
| 0FF9H: MODE             |
| 0FFAH: KEYFLAG/Variables|
+---------------------------+
```
