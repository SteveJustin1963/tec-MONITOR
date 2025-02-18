# BMON Monitor Commands
===================

## Basic Navigation Commands
```
+ (0x10)      : Increment address
- (0x11)      : Decrement address
GO (0x12)     : Execute from current address
ADDR (0x13)   : Toggle between Address/Data mode
```
## Function Commands (SHIFT-0 or Function Mode)
```
0: SIO-IN     - Serial input from computer to TEC
1: SIO-OT     - Serial output from TEC to computer
2: INTEL      - Intel HEX file input
3: ALTKEY     - Toggle between keyboard layouts
4: BANNER     - ASCII banner display
5: MINT       - MINT programming language
6: BASIC      - TINY BASIC programming language
7: GAMES      - Games menu
```
## SHIFT + Number Commands
```
SHIFT-0: Enter menu system
SHIFT-1: Move back one location
SHIFT-2: Enter stepper mode
SHIFT-3: Enter expansion ROM menu
SHIFT-4: Move forward four locations
SHIFT-5: Perform soft reset
SHIFT-6: Move back four locations
SHIFT-7: Display registers
SHIFT-8: Move forward eight locations
SHIFT-9: Move forward one location
SHIFT-A: Move back eight locations
SHIFT-B: Enter BASIC
SHIFT-C: Enter games
SHIFT-D: Play tunes
SHIFT-E: ASCII scroll
SHIFT-F: Enter MINT
```


## Stepper Commands (in Stepper Mode)
```
+         : Next register
-         : Previous register
GO        : Execute next instruction
ADDR      : Exit stepper
Data Keys : Enable auto-step
```

## Monitor Control Commands

```
V_AUTO (0x3F27)    : Toggle auto increment
V_ALT_ADDF (0x3F23): Set alternate GO address
V_BEEP (0x3F22)    : Toggle sound
V_LCD (0x3F21)     : Toggle LCD
```

## Serial Communication

```
Baud Rate: 4800
Data Bits: 8
Stop Bits: 2
Parity: None
TX Pin: Port 1 Bit 6
RX Pin: Port 0 Bit 7
```

## Memory Commands

```
- View/Edit memory in Data mode
- Set address in Address mode
- Auto-increment after two nibbles (when enabled)
- Execute from any address using GO
```

## Keyboard Layouts

```
Standard Layout:
AD 3 7 B F
GO 2 6 A E
-  1 5 9 D
+  0 4 8 C
```

## Alternate Layout (Mark Jelic's):
```
AD 7 8 9 A
GO 4 5 6 B
-  1 2 3 C
+  0 F E D
```

```
+------------------[START 0x0000]-------------------+
|                                                  |
+-> Save HL (0x0000) ---> Get Alt Addr (0x0003) --+
                                |
                                v
+---------------[MONITOR START 0x006B]--------------+
|                                                  |
|  +-> Set IM1 -> Store CEL -> Save Registers      |
|  |                                               |
|  +-> Set Stack -> Check Reset -> Hard/Soft RST   |
|                                                  |
+--------------------------------------------------+
                                |
        +---------------------+ | +------------------+
        |                     v v v                  |
    [RST 08]              [RST 10]              [RST 18]
    0x0008                0x0010                0x0018
    Key Release           Key Press             LED Scan
        |                     |                     |
        +---------------------+---------------------+
                             |
                             v
+---------------[MAIN LOOP 0x00B2]------------------+
|                                                  |
|   Update Display -> Get Keys -> Process Input    |
|           ^                    |                 |
|           |                    v                 |
|   [Display Buffer]    [Key Processing 0x00C6]    |
|                              |                   |
|                     +--------+--------+          |
|                     v        v        v          |
|                  Data     Address   Function     |
|                  Mode      Mode      Mode        |
|                                                  |
+--------------------------------------------------+
                             |
             +---------------+---------------+
             v               v               v
    [Menu System]    [Serial I/O]     [Single Step]
     0x03D2           0x0426           0x02FB
         |               |                 |
    Initialize     Transmit/Recv      Save Regs
         |               |                 |
    Display Loop    Bit-bang I/O      Step Loop
         |               |                 |
    Process Keys    Intel HEX         Reg Display
                                          |
                   [LCD 0x023C]           |
                        |                 |
                   Initialize             |
                        |                 |
                   Line Display           |
                        |                 |
                   Prompt Update          |
                                          |
                                          v
+------------------[Memory Map]------------------+
|                                                |
| 0x0000-0x07FF: Monitor ROM                     |
| 0x3F00-0x3FFF: System Variables                |
| 0x4000-0x5FFF: Expansion ROM                   |
| 0x0800: MINT                                   |
| 0x1000: BASIC                                  |
| 0x18E0: Games                                  |
|                                                |
+------------------------------------------------+
                    |
            +-------+-------+
            v               v
     [I/O Ports]    [System Variables]
     Port 0: KB     0x3F00: Display Buff
     Port 1: Cath   0x3F20: Key Buffer
     Port 2: Seg    0x3F2B: MCB
     Port 4: LCD    0x3F2E: CEL

[Key Functions]
+-> Plus (0x00D3) ----> Inc Address
+-> Minus (0x00E3) ---> Dec Address
+-> Go (0x00EA) ------> Execute
+-> Addr (0x0102) ----> Toggle Mode

[Mode Control]
Data Mode:    Edit Memory
Address Mode: Edit Address
Function Mode: Special Ops

[Serial Protocol]
4800-8-N-2
Tx: Port 1 Bit 6
Rx: Port 0 Bit 7

[Keyboard Modes]
Standard Layout:    Alt Layout:
+---------------+  +---------------+
| AD 3 7 B F    |  | AD 7 8 9 A    |
| GO 2 6 A E    |  | GO 4 5 6 B    |
| -  1 5 9 D    |  | -  1 2 3 C    |
| +  0 4 8 C    |  | +  0 F E D    |
+---------------+  +---------------+

[Interrupt Vectors]
RST 08: 0x0008 Wait Key Release
RST 10: 0x0010 Wait Key Press
RST 18: 0x0018 LED Scan
RST 20: 0x0020 Read Keyboard
RST 28: 0x0028 Start Step
RST 30: 0x0030 LCD Busy
RST 38: 0x0038 Stepper
```
