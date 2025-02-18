# BMON Monitor Commands and Functions

## Main Menu Commands (Shift-0)
Use PLUS/MINUS to scroll, GO to select:
1. SIO-IN - Serial input. Load data from computer to TEC
2. SIO-OT - Serial output. Send TEC data to computer
3. INTEL - Intel HEX file input from computer
4. ALTKEY - Toggle between standard/alternate keyboard layouts
5. BANNER - ASCII banner ticker display

## Function Keys (Shift + Number or ADDRESS + PLUS + Number)
| Key | Function                                  | Address |
|-----|------------------------------------------|---------|
| 0   | Main Menu Setup                          | 03D2h   |
| 1   | Move address back one location           | 02E3h   |
| 2   | Single Stepper                          | 005Eh   |
| 3   | N/A                                      | -       |
| 4   | Move address forward four locations      | 02D3h   |
| 5   | Soft Reset                              | 00AEh   |
| 6   | Move address back four locations        | 02DEh   |
| 7   | Display Registers                       | 0341h   |
| 8   | Move address forward eight locations    | 02EDh   |
| 9   | Move address forward one location       | 02E8h   |
| A   | Move address back eight locations       | 02F2h   |
| B   | N/A                                      | -       |
| C   | N/A                                      | -       |
| D   | N/A                                      | -       |
| E   | ASCII Scroll Routine                    | 05BDh   |
| F   | N/A                                      | -       |

## Standard Control Keys
| Key    | Function                              |
|--------|---------------------------------------|
| +      | Increment address                     |
| -      | Decrement address                     |
| GO     | Execute code at current address       |
| ADDR   | Toggle address/data mode              |

## RST (Restart) Commands
| Command | Address | Function                    |
|---------|---------|----------------------------|
| RST 08h | 0008h   | Wait for key release       |
| RST 10h | 0010h   | Wait for key press         |
| RST 18h | 0018h   | LED scan and key read      |
| RST 20h | 0020h   | Key read/validate          |
| RST 28h | 0028h   | Start stepping             |
| RST 30h | 0030h   | LCD busy test              |
| RST 38h | 0038h   | Interrupt handler          |

## Jump Table Functions
| Address | Function                               |
|---------|----------------------------------------|
| 0041h   | Menu Routine                           |
| 0044h   | Perimeter Handler Entry                |
| 0047h   | Soft Menu Entry                        |
| 004Ah   | Transmit Byte                          |
| 004Dh   | Receive Byte                           |
| 0050h   | Soft Perimeter Handler Entry           |
| 0053h   | Bit Time Delay                         |
| 0056h   | ASCII Scroll Routine                   |

## Serial Communication
- Default Baud Rate: 4800
- Settings: 8-N-2 (8 data bits, No parity, 2 stop bits)
- Uses: 
  - Bit 7 of keyboard buffer (input, port 0)
  - Bit 6 of seven segment active port (output, port 1)

## Key Memory Locations
| Address | Function                               |
|---------|----------------------------------------|
| 0800h   | Display Buffer                         |
| 0820h   | Key Buffer                            |
| 0821h   | LCD On/Off Flag                       |
| 0822h   | Sound On/Off                          |
| 0823h   | Alt GO Address Flag                   |
| 0824h   | Stepper Key Control/Timer             |
| 0825h   | Key Press Flag                        |
| 0826h   | Alternative Keyboard Layout Flag      |
| 0827h   | Auto Increment On/Off                 |
| 0828h   | Alt GO Address                        |
| 082Ah   | Auto Key Status                       |
| 082Bh   | Monitor Control Byte                  |
| 082Ch   | Display Buffer Address                |
| 082Eh   | Current Editing Location              |

## Reset Types
1. Hard Reset: 
   - Press any key while releasing RESET
   - Reinitializes all variables
   - Masks off user patches

2. Soft Reset:
   - Regular RESET button press
   - Maintains user setups and flags
   - Preserves alternate keyboard setting

## Notes
- All addresses shown are from the 2K TEC-1D version
- Serial communication routines can be called externally (004Ah and 004Dh)
- ASCII banner routine supports full ASCII character set (32-127)
- Alternative keyboard layout remains active through soft resets
- 


```
                +---------------------------+
                |      Start Banner        |
                |    J_MSG_SCRL (05B4h)    |<---------+
                +---------------------------+          |
                           |                          |
                           v                          |
                +---------------------------+         |
                | Set Perimeter Handler     |         |
                | Jump Addr to ASCII_SCRL   |         |
                | Store at PERI_J_ADR(0888h)|         |
                +---------------------------+         |
                           |                          |
                           v                          |
                +---------------------------+         |
                | Call Perimeter (0044h)    |         |
                | Get Start/End Address      |         |
                | Store at SIO_START(0898h) |         |
                +---------------------------+         |
                           |                          |
                           v                          |
            +--------------------------------+       |
            |    ASCII_SCRL (05BDh)          |       |
            | Initialize Display at 0800h     |       |
            | Clear all 6 displays            |       |
            | Set DISP_BUFF pointer          |       |
            +--------------------------------+       |
                           |                          |
                           v                          |
                    +-------------+                   |
            +------>| Shift Loop  |                  |
            |       | ASCII_SHIFT |                  |
            |       | (05CDh)     |                  |
            |       +-------------+                   |
            |              |                          |
            |              v                          |
            |    +-------------------+                |
            |    | LED_SCAN (0036h)  |                |
            |    | - Output segments  |                |
            |    | - Scan commons    |                |
            |    | - Handle timing   |                |
            |    +-------------------+                |
            |              |                          |
            |              v                          |
            |    +-------------------+                |
            |    | Get Next ASCII    |                |
            |    | From HL address   |                |
            |    +-------------------+                |
            |              |                          |
            |              v                          |
            |         +----------+                    |
            |         | Is Char  |                    |
            +---------|  CR?     |--------+           |
            |         | (0Dh)    |        |           |
            |         +----------+        |           |
            |              |             |           |
            |              v             v           |
            |    +-------------------+   |           |
            |    | Convert via       |   |           |
            |    | ASCII_SEG_TBL     |   |           |
            |    | (0555h-05B4h)     |   |           |
            |    | Full char set:    |   |           |
            |    | Space to ~        |   |           |
            |    +-------------------+   |           |
            |              |            |           |
            |              v            |           |
            |    +-------------------+  |           |
            |    | Store at          |  |           |
            |    | DISP_BUFF + 5     |  |           |
            |    | (0805h)           |  |           |
            |    +-------------------+  |           |
            |              |            |           |
            |              v            |           |
            |    +-------------------+  |           |
            |    | Shift Display     |  |           |
            |    | - Move 5 chars    |  |           |
            |    | - Update buffer   |  |           |
            |    +-------------------+  |           |
            |              |            |           |
            |              v            |           |
            |         +----------+      |           |
            |         |  Delay   |      |           |
            |         | Counter  |      |           |
            |         | C = FFh  |      |           |
            |         +----------+      |           |
            |              |            |           |
            |              v            |           |
            |    +-------------------+  |           |
            |    | RST 18h Scan      |  |           |
            |    | - Check keyboard  |  |           |
            |    | - Update display  |  |           |
            |    | - Handle buffer   |  |           |
            |    | V_KEY_PRES(0825h)|  |           |
            |    +-------------------+  |           |
            |         |        |       |           |
            |      No |        | Yes   |           |
            +---------+        v       |           |
                         +-----------+ |           |
                         |   Exit    | |           |
                         | Return    | |           |
                         +-----------+ |           |
                               ^       |           |
                               |       |           |
                               +-------+-----------+

I/O Ports:
+--------+-------------------------+
| 00h    | Keyboard Input         |
| 01h    | Display Scan (DSCAN)   |
| 02h    | Segments (DSEGMENT)    |
+--------+-------------------------+

Memory Map of Key Addresses:
+-----------------+------------------+
| 0036h          | LED_SCAN         |
| 0555h-05B4h    | ASCII_SEG_TBL    |
| 05B4h          | J_MSG_SCRL       |
| 05BDh          | ASCII_SCRL       |
| 05CDh          | ASCII_SHIFT      |
| 0800h-0805h    | Display Buffer   |
| 0825h          | Key Press Flag   |
| 0888h          | PERI_J_ADR       |
| 0898h          | SIO_START        |
+-----------------+------------------+

Display Buffer Structure (0800h):
+------+------+------+------+------+------+
| 0800 | 0801 | 0802 | 0803 | 0804 | 0805 |
| LSB  |  ..  |  ..  |  ..  |  ..  | MSB  |
+------+------+------+------+------+------+

7-Segment Display Encoding:
     a
    ---
 f |   | b
    -g-
 e |   | c
    ---
     d  dp

Bit Layout: [a b c d e f g dp]
Example 'A' = 6Fh = 0110 1111b

Full ASCII Table (96 chars from 20h to 7Fh):
- Space to / (20h-2Fh)
- Numbers 0-9 (30h-39h)
- Symbols : to @ (3Ah-40h)
- Letters A-Z (41h-5Ah)
- Symbols [ to ` (5Bh-60h)
- Letters a-z (61h-7Ah)
- Symbols { to ~ (7Bh-7Eh)
```



``` 
