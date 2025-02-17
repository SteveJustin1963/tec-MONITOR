```
TEC-1 Monitor ROM Version Comparison
===================================

Common System Constants:
STARTRAM:   0800H    STARTSTACK: 0FF0H
DISPLAY:    0FF1H    ADDRESS:    0FF7H 
MODE:       0FF9H    KEYFLAG:    0FFAH
PORTKEYB:   00H      PORTDIGIT:  01H
PORTSEGS:   02H

Version v1:                      Version v1A:
+---------------------------+   +---------------------------+
|    RESET (START)         |   |    RESET (START)         |
|    JP STARTMON (0000H)   |   |    JP STARTMON (0000H)   |
+---------------------------+   +---------------------------+
            |                             |
            v                             v
+---------------------------+   +---------------------------+
|     STARTMON (0580H)     |   |     STARTMON (05F0H)     |
|  - Set up system         |   |  - Save SP at 0FD8H      |
|  - Init stack & regs     |   |  - Set SP to 0FF0H       |
+---------------------------+   |  - Save all registers     |
                               +---------------------------+
                                           |
                                           v
                               +---------------------------+
                               |    STARTMON2 (057DH)     |
                               |  - Init system same as v1 |
                               +---------------------------+

Interrupt Handlers:
v1:                           v1A:
NMINT (0066H)                NMINT (0066H)
- Read key to i reg           - Same functionality
- RET (bug)                   - RET (bug noted)

Main Program Flow:
+---------------------------+
|   Main Display Loop      |
|   WRITEDISP2 (006DH)     |
+---------------------------+
            |
    +-------+--------+
    |                |
+----------+  +-----------+
|ADDRDISP  |  |DATADISP   |
| (00DAH)  |  | (00E3H)   |
+----------+  +-----------+

Key Functions (Same in both):
ADDRKEY    (0088H)  - Handle numeric input in addr mode
DATAKEY    (00B7H)  - Handle numeric input in data mode
GOADDR     (00C2H)  - Execute at current address
DECADDR    (00C6H)  - Decrement address
INCADDR    (00D0H)  - Increment address

Display Functions (Same in both):
WRITEDISP  (00EAH)  - Update display buffer
WRITEADDR  (0102H)  - Write address to display
WRITEDATA  (010EH)  - Write data to display
WRITEHEX   (0115H)  - Convert hex to segments
HEX2SEGS   (0126H)  - Get segment pattern

Built-in Games/Features:
INVADERS    (0320H)  - Space invaders
NIM         (03E0H)  - Match game
LUNALANDER  (0490H)  - Landing simulation
SHOWTEXT    (0270H)  - Scrolling text
PLAYTUNE    (01B0H)  - Music playback

Key Differences:
1. STARTMON location:
   v1:  0580H
   v1A: 05F0H with STARTMON2 at 057DH

2. Stack Handling:
   v1:  Direct stack init
   v1A: Saves old SP and all registers

3. New in v1A:
   - SEQUENCER code at 05B0H
   - Register save on startup
   - More thorough initialization

Memory Map:
+---------------------------+
| 0000H: Reset Vector      |
| 0066H: NMI Vector       |
| 0100H-02FFH: Main Code  |
| 0300H-04FFH: Games      |
| 0500H-05FFH: Monitor    |
| 0800H: User RAM Start   |
| 0FF0H: Stack            |
| 0FF1H-0FFFH: Variables  |
+---------------------------+
```
