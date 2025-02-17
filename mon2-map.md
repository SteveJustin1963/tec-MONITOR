TEC Monitor 2.10 Memory Layout & Flow
===================================

Memory Map:
+---------------------------+
| 0000H-007FH: Vector Table|
| 0080H-00FFH: 7Seg Data   |
| 0100H-016FH: Freq Tables |
| 0170H-01FFH: Sound Code  |
| 0200H-02FFH: Main Code   |
| 0300H-04FFH: Key Handling|
| 08C0H-08FFH: System Vars |
| 0900H-3FFFH: User Code   |
+---------------------------+

System Variables (08xxH):
+---------------------------+
| 08C0H: VECTOR0-VECTOR5   |
| 08D0H: X0 (Work vars)    |
| 08D2H: SHIFTINS          |
| 08D4H: SHIFTDEL          |
| 08D6H: TUNEADDR          |
| 08D8H: ADDRESS (4 bytes) |
| 08DFH: MODE              |
| 08E0H: KEYDATA           |
| 08E8H: STACKSTART        |
+---------------------------+

Restart Vectors:
+---------------------------+
| 0000H: RESTART00 -> STARTMON
| 0008H: RESTART08 -> VECTOR0
| 0010H: RESTART10 -> VECTOR1
| 0018H: RESTART18 -> VECTOR2
| 0020H: RESTART20 -> VECTOR3
| 0028H: RESTART28 -> VECTOR4
| 0030H: RESTART30 -> VECTOR5
| 0038H: RESTART38 -> VECTOR6
| 0066H: NMI -> KEYDATA    |
+---------------------------+

Main Program Flow:
+---------------------------+
|      RESET/STARTUP       |
|     STARTMON (0200H)     |
+---------------------------+
            |
            v
+---------------------------+
|  Save State:             |
| - Save SP @ STACKSTART   |
| - Set SP = RAMSTART      |
| - Save all registers     |
| - Clear VECTOR6          |
| - Set KEYDATA = FF       |
+---------------------------+
            |
            v
+---------------------------+
|    STARTMON2 (0240H)     |
| - SP = VECTOR0           |
| - Clear display ports    |
| - Init ADDRESS buffer    |
| - Play startup beeps     |
| - Set MODE = 1           |
+---------------------------+
            |
            v
+---------------------------+
|     Main Loop:           |
| - Display current addr   |
| - Get key input         |
| - Process command       |
+---------------------------+

Key Functions:
+---------------------------+
| GETKEY    (0360H)        |
| - Read from KEYDATA      |
| - Process shift state    |
| - Return key value       |
+---------------------------+

Address Handling:
+---------------------------+
| GETEDITADDR (0289H)      |
| - Convert 4-byte display |
|   format to BC pair     |
| - Load data from addr   |
+---------------------------+
            |
            v
+---------------------------+
| SETEDITADDR (0490H)      |
| - Convert BC pair to     |
|   4-byte display format |
+---------------------------+

Display Functions:
+---------------------------+
| DISPLAY    (02A0H)       |
| - Show 6 digits         |
| - Handle decimal points |
| - Multiplex digits     |
+---------------------------+
            |
            v
+---------------------------+
| HEX2SEG    (0350H)      |
| - Convert hex to segs   |
| - Handle mode bits      |
+---------------------------+

Special Features:
+---------------------------+
| PLAYTONE   (0170H)       |
| - Sound generation      |
+---------------------------+
            |
            v
+---------------------------+
| PLAYTUNE   (01A0H)       |
| - Music playback        |
+---------------------------+

Memory Range Functions:
+---------------------------+
| Insert (Shift+)          |
| - Range: 0900H-4000H    |
+---------------------------+
| Delete (Shift-)          |
| - Range: 0900H-03FFH    |
+---------------------------+
