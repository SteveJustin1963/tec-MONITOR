# BMON Monitor Command Reference

## Basic Navigation Commands
1. `+` (0x10)
   - In Data Mode: Increment current edit location
   - In Menu Mode: Move to next menu item
   - Address: 00D7h

2. `-` (0x11)
   - In Data Mode: Decrement current edit location
   - In Menu Mode: Move to previous menu item
   - Address: 00E7h

3. `GO` (0x12)
   - In Data Mode: Execute code at current address
   - In Menu Mode: Execute selected function
   - Uses alternate GO address if flag is set (0823h = AA)
   - Address: 00EEh

4. `ADDR` (0x13)
   - Toggle between Address and Data mode
   - In Function Mode: Clear function mode bits
   - Address: 0102h

## Function Commands (SHIFT + Number)
Function-1 (Shift Operations):
1. SHIFT-0: Main Menu (03D2h)
2. SHIFT-1: Move back one location (02E3h)
3. SHIFT-2: Enter Stepper mode (005Eh)
4. SHIFT-4: Move forward four locations (02D3h)
5. SHIFT-5: Soft Reset (00AEh)
6. SHIFT-6: Move back four locations (02DEh)
7. SHIFT-7: Display Registers (0341h)
8. SHIFT-8: Move forward eight locations (02EDh)
9. SHIFT-9: Move forward one location (02E8h)
10. SHIFT-A: Move back eight locations (02F2h)
11. SHIFT-E: ASCII Scroll routine (05BDh)

## Menu System Commands (SHIFT-0)
Menu Options:
1. SIO-IN
   - Serial Input mode
   - Select start/end address for data load
   - Address: 0426h

2. SIO-OT
   - Serial Output mode
   - Select start/end address for data transmission
   - Address: 0426h

3. INTEL
   - Intel HEX file input mode
   - Receives Intel HEX format data via serial
   - Address: 05FCh

4. ALTKEY
   - Toggle alternate keyboard layout
   - Switches between standard and Mark Jelic's layout
   - Address: 066Ah

5. BANNER
   - ASCII banner ticker display
   - Select start address for message
   - Address: 05BDh

## Stepper Commands (After SHIFT-2)
1. `+`: Move to next register
2. `-`: Move to previous register
3. `GO`: Continue execution
4. `ADDR`: Return to monitor
5. Data Keys: Enable auto-step

Available Registers:
- PC: Program Counter
- AF: Accumulator & Flags
- BC: BC Register Pair
- DE: DE Register Pair
- HL: HL Register Pair
- IX: Index Register X
- IY: Index Register Y
- AF': Alternate AF
- BC': Alternate BC
- DE': Alternate DE
- HL': Alternate HL
- SP: Stack Pointer

## Serial Communication Settings
- Default Baud Rate: 4800
- Data Bits: 8
- Stop Bits: 2
- Parity: None
- Uses bit-banged I/O:
  - Input: Keyboard buffer bit 7
  - Output: Seven segment active port bit 6

## Keyboard Layouts

### Standard Layout
```
|----|---|---|---|---|
| AD | 3 | 7 | B | F |
|----|---|---|---|---|
| GO | 2 | 6 | A | E |
|----|---|---|---|---|
| -  | 1 | 5 | 9 | D |
|----|---|---|---|---|
| +  | 0 | 4 | 8 | C |
|----|---|---|---|---|
```

### Alternate Layout (Mark Jelic)
```
|----|---|---|---|---|
| AD | 7 | 8 | 9 | A |
|----|---|---|---|---|
| GO | 4 | 5 | 6 | B |
|----|---|---|---|---|
| -  | 1 | 2 | 3 | C |
|----|---|---|---|---|
| +  | 0 | F | E | D |
|----|---|---|---|---|
```

## Monitor Control Byte (MCB) at 082Bh
Bit Functions:
- Bits 0-1: Number of nibbles entered
  - 00: No nibbles
  - 01: One nibble
  - 10: Two nibbles
- Bits 2-3: Function number
  - 00: No function/Function-1
  - 01: Function-2
  - 10: Function-3
- Bit 4: Address/Function mode
- Bit 5: Function mode enabled
- Bits 6-7: Unused

## Reset Types
1. Hard Reset
   - Activated by holding key during reset
   - Reinitializes all variables
   - Masks user patches
   - Initializes LCD
   - Address: 0085h

2. Soft Reset
   - Normal reset without key press
   - Maintains user settings
   - Retains patches
   - Address: 00A2h

## Memory-Related Commands
- Display current location: Auto-displayed in address field
- Modify memory: Enter data in data mode
- View memory: Use +/- to navigate
- Execute code: Use GO at desired address
- Single step: Enter stepper mode (SHIFT-2)

## Special Features
1. Auto Increment
   - Automatically moves to next address after byte entry
   - Controlled by flag at 0827h

2. LCD Display (if present)
   - Shows current address and data
   - Displays menu items
   - Shows register contents in stepper mode

3. Breakpoint System
   - Uses RST 38h (FF)
   - Saves register state
   - Allows program examination

4. Key Plant System
   - Allows software-controlled key input
   - Uses bit 7 of key buffer (0820h)

5. User Patches
   - Before scan (084Bh)
   - During loop (084Eh)
   - After key (0851h)
   - Keyboard (0854h)

## Error Handling
1. Serial Operations
   - Checksum verification for Intel HEX
   - Timeout handling for receive
   - Error indication on display

2. LCD Operations
   - Busy flag checking
   - Presence detection
   - Initialization verification

3. Key Input
   - Debounce handling
   - Invalid key filtering
   - Auto-repeat control

```
+----------------------+
                              |       RESET          |
                              | Save HL (0000h)      |
                              | Store Alt Addr       |
                              +----------+-----------+
                                        |
                    +-------------------+-------------------+
                    |                   |                  |
            +-------v--------+   +------v-------+  +------v-------+
            |   RST 08/10    |   |   RST 18     |  |   RST 20     |
            | Key Detection  |   | LED Display   |  | Key Reading  |
            | Wait Release   |   | Scan Once     |  | Validation   |
            | (0008h/0010h)  |   | (0018h)       |  | (0020h)      |
            +-------+--------+   +------+-------+  +------+-------+
                    |                   |                  |
            +-------v--------+   +------v-------+  +------v-------+
            |   RST 28       |   |   RST 30     |  |   RST 38     |
            | Start Stepping |   | LCD Busy     |  | Break/Step   |
            | Save Next PC   |   | Check Loop   |  | Handler      |
            | (0028h)        |   | (0030h)      |  | (0038h)      |
            +-------+--------+   +------+-------+  +------+-------+
                    |                   |                  |
                    +-------------------+------------------+
                                       |
                           +----------v-----------+
                           |    Monitor Start     |
                           | Set IM1 (006Bh)      |
                           | Save CEL             |
                           | Save Regs (0318h)    |
                           +----------+-----------+
                                     |
                     +---------------+---------------+
                     |                              |
             +------v-------+                +------v-------+
             | Hard Reset   |                | Soft Reset   |
             | (0085h)      |                | (00A2h)      |
             | - Vars Reset |                | - Keep Vars  |
             | - LCD Init   |                | - Test JMON  |
             | - Mask Patch |                | - Beep       |
             +------+-------+                +------+-------+
                    |                               |
                    +---------------+---------------+
                                   |
                        +----------v-----------+
                        |    Display Update    |
                        | Get CEL (00B2h)      |
                        | Convert HL           |
                        | Set Dots             |
                        | Scan I/O             |
                        +----------+-----------+
                                  |
                   +--------------+--------------+
                   |              |              |
           +------v------+  +-----v------+  +---v----------+
           | Key Manager |  |  Menu/Func |  | Data Handler |
           | (00C6h)     |  |  Mode      |  | (011Ch)      |
           | Test MCB    |  |  (0111h)    |  | Auto Inc    |
           +------+------+  +-----+------+  +---+---------+
                  |               |              |
        +---------+---------------+--------------+
        |         |         |           |            |
  +-----v---+ +---v----+ +-v--------+ +v--------+ +-v-------+
  |   '+'   | |  '-'   | |   GO    | | Address | |Function |
  | (00D7h) | |(00E7h) | | (00EEh) | | Toggle  | | Keys    |
  | Inc CEL | |Dec CEL | |Execute  | | (0102h) | |(0150h)  |
  +---------+ +--------+ +---------+ +---------+ +---------+
        |         |         |           |            |
        +-------------------+-----------+------------+
                           |
                   +------v------+
                   |  Functions  |
                   +-------------+
                   |
    +-------------+-------------+-------------+-------------+
    |             |             |             |             |
+---v----+   +---v----+   +----v---+   +----v----+   +----v----+
|SIO In  |   |SIO Out |   |Intel   |   |Alt Key  |   |Banner   |
|(0426h) |   |(0426h) |   |HEX In  |   |Layout   |   |Scroll   |
|Transfer|   |Transfer|   |(05FCh) |   |(066Ah)  |   |(05BDh)  |
|Routine |   |Routine |   |Loader  |   |Toggle   |   |Display  |
+--------+   +--------+   +--------+   +---------+   +---------+

LCD Functions (023Ch):
- Initialize (06D8h)
- Set Line 1 (0253h)
- Set Line 2 (025Ah)
- Convert HL (026Ch)
- Convert A (0271h)
- Set Prompt (0286h)

LED Functions:
- Scan Routine (01BAh)
- HL to Display (01D5h)
- A to Display (01DAh)
- Set Dots (01EEh)

Serial Functions:
- TX Char (04F0h)
- RX Char (051Bh)
- Bit Time (0548h)

Keyboard Functions:
- Read Key (06ADh)
- Get Key (06CAh)
- Alt Layout (0682h)

Stepper Functions:
- Save Regs (0318h)
- Step Control (0344h)
- Register Display (0341h)

Display Tables:
- Segment Table (07D0h)
- ASCII Table (0555h)
- Register Table (0794h)
- Menu Display (073Ah)
- LCD Function (07ADh)
- LCD Prompt (07BDh)

Menu/Function Tables:
- Menu Jump Table (0700h)
- Main Menu Driver (0789h)
- Function 1 Jump Table (07E0h)

Memory Locations:
Variables (0820h-082Fh):
- Key Buffer (0820h)
- LCD Flag (0821h)
- Sound Flag (0822h)
- Alt Address Flag (0823h)
- Step Timer (0824h)
- Key Press Flag (0825h)
- Alt Keyboard Flag (0826h)
- Auto Inc Flag (0827h)
- Alt Address (0828h)
- Auto Key Status (082Ah)
- Monitor Control Byte (082Bh)
- Display Buffer Addr (082Ch)
- Current Edit Location (082Eh)

Jump Addresses (0830h-0848h):
- HL to Display (0830h)
- A to Display (0833h)
- LED Scan (0836h)
- Set Dots (0839h)
- Beep x2 (083Ch)
- Beep (083Fh)
- Scan I/O (0842h)
- Display Update (0845h)
- LCD Display (0848h)

Patch Addresses (084Bh-0854h):
- Before Scan (084Bh)
- During Loop (084Eh)
- After Key (0851h)
- Keyboard (0854h)

Stepper Memory (0858h-087Eh):
- Next Instruction (0858h)
- Current Register (085Ah)
- Re-entry Address (0860h)
- Previous PC (0868h)
- Register Display (086Ah)
- HL Save (086Eh)
- Initial HL (0870h)
- Stack Pointer (087Eh)

Menu/Perimeter Setup (0880h-0897h):
- Perimeter Setup (0880h)
- Data Table (0882h)
- Window Start (0884h)
- Current Window (0886h)
- Max Windows (0887h)
- Jump Address (0888h)
- Active Window (088Ch)
- Menu Setup (088Dh)
- Menu Number (088Fh)
- Jump Table (0891h)
- Address Table (0893h)
- Data Table (0895h)
- Key Return (0897h)

SIO/Intel Setup (0898h-08B7h):
- SIO Start (0898h)
- SIO End (089Ah)
- Baud Rate (08A4h)
- HEX Address (08A6h)
- HEX Checksum (08A8h)
- Reset Patch Req (08B0h)
- Patch Start (08B1h)
- Patch Variable (08B3h)
- Patch Checksum (08B5h)
- Patch Address (08B6h)

Control Flow Details:
1. On Reset:
   - Save HL
   - Check for forced reset
   - Initialize if hard reset
   - Preserve state if soft reset

2. Main Loop:
   - Update display
   - Scan keyboard
   - Process input
   - Handle functions

3. Key Processing:
   - Check MCB
   - Handle address/data modes
   - Process function keys
   - Auto increment control

4. Menu System:
   - Display menu items
   - Handle navigation
   - Execute functions
   - Return to main loop

5. Serial Operations:
   - Configure baud rate
   - Handle transmission
   - Receive data
   - Process Intel HEX

6. Display Management:
   - Convert values
   - Update segments
   - Handle LCD
   - Set indicators

7. Stepper Operation:
   - Save registers
   - Single step
   - Display state
   - Handle breakpoints
```
