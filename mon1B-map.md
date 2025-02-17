```
TEC-1B Monitor ROM v1A Memory Map & Flow
======================================

Memory Layout:
0000H-00FFH: Vector Table & RSTs
0100H-02FFH: Core Monitor Code
0300H-04FFH: Games & Applications
0500H-05FFH: Monitor & Sequencer
0800H-0FFFH: RAM & Variables

System Constants:
+---------------------------+
| STARTRAM:   0800H        |
| DISPLAY:    0FF1H-0FF7H  |
| MODE:       0FF9H        |
| KEYFLAG:    0FFAH        |
| I/O PORTS:  00H-04H     |
+---------------------------+

Main Program Flow:
+---------------------------+
|     RESET VECTOR (0000H) |
|     JP STARTMON          |
+---------------------------+
            |
            v
+---------------------------+
|   STARTMON (05F0H)       |
| - Save SP at 0FD8H       |
| - Set SP to 0FF0H        |
| - Save all registers     |
| - Jump to STARTMON2      |
+---------------------------+
            |
            v
+---------------------------+
|   STARTMON2 (057DH)      |
| - Init system:           |
|   - Set HL = STARTRAM    |
|   - Set SP = 0FD0H       |
|   - Set IX = DISPLAY     |
|   - Clear MODE & KEYFLAG |
|   - Play startup beeps   |
+---------------------------+
            |
            v
+-----------+-------------+
|   Main Display Loop     |
+-------------------------+
       |
    +--+----------------+
    |                   |
+---v----+        +----v---+
|ADDRDISP|        |DATADISP|
| (00DAH)|        | (00E3H)|
+--------+        +--------+

Key Functions (00xxH):
GETKEY     (0131H) - Wait for key input
WRITEDISP  (00EAH) - Update display
SCANDISP   (0140H) - Scan LED display
HEX2SEGS   (0126H) - Convert to segments

Game Entry Points:
+---------------------------+
| INVADERS    (0320H)      |
| NIM         (03E0H)      |
| LUNALANDER  (0490H)      |
+---------------------------+

Features:
SHOWTEXT    (0270H) - Scrolling text
PLAYTUNE    (01B0H) - Music playback
SEQUENCER   (05B0H) - NEW! Two-speed sequencer

Sequencer Flow (NEW!):
+---------------------------+
|     SEQUENCER (05B0H)    |
| - SLOWSEQ at 0800H       |
| - FASTSEQ at 0B00H       |
+---------------------------+
            |
            v
+---------------------------+
|     Main Loop:           |
| 1. Read slow sequence    |
| 2. Output to PORTSLOW    |
| 3. Read fast sequence    |
| 4. Output to PORTFAST    |
| 5. Delay                 |
| 6. Loop or restart      |
+---------------------------+

Interrupt Handling:
+---------------------------+
| NMINT (0066H)            |
| - Read keyboard          |
| - Mask to 5 bits        |
| - Store in I register   |
| - Return (BUG: no RETN) |
+---------------------------+

Display Buffer Layout:
+---------------------------+
| DISPLAY   0FF1H  Data    |
| DISPLAY2  0FF3H  Addr Hi |
| DISPLAY3  0FF4H  Addr Lo |
| DISPLAY4  0FF5H  Digit 4 |
| DISPLAY5  0FF6H  Digit 5 |
| DISPLAY6  0FF7H  Digit 6 |
+---------------------------+
```
