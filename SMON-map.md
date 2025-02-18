[fc.pdf](https://github.com/user-attachments/files/18838493/fc.pdf)


# Key points about the addresses:
- Program starts at 0x0000
- Initialization occurs in the first few bytes
- Key processing routines are spread across different memory locations
- NMI (Non-Maskable Interrupt) handler is at 0x0066


# Hardware Keys/Commands:
1. '+' (Increment Address) - 0x0032-0x0037
2. '-' (Decrement Address) - 0x003C-0x0041
3. 'GO' (Execute at Current Address) - 0x0043-0x0047
4. 'AD' (Toggle Address/Data Mode) - 0x0048-0x004D

# Modes:
1. Address Mode - Controlled by bit 4 of the Control Byte at 0x0F08
2. Data Mode - Controlled by bit 4 of the Control Byte at 0x0F08

# Functional Operations:
1. Address Navigation - 0x0032-0x0041
   - Increment current address - 0x0036
   - Decrement current address - 0x0040
   - Display current address - 0x008B-0x00AA

2. Memory Manipulation - 0x004F-0x0064
   - View memory contents - 0x008B-0x0096
   - Modify memory contents (hex digit input) - 0x0058-0x0064

3. Execution - 0x0043-0x0047
   - Run program at current address - 0x0047

# System Routines:
1. NMI (Non-Maskable Interrupt) Handler - 0x0066-0x006E
2. Display Multiplexing - 0x0070-0x008A
3. Hex to Seven-Segment Conversion - 0x008B-0x00AA
4. Tone Generation - 0x00C1-0x00D2
   - Reset Tone - 0x00C1-0x00C4
   - Key Tone - 0x00C4-0x00D2

# Key Processing:
1. Hexadecimal Digit Input (0-9, A-F) - 0x0058-0x0064
2. Mode Switching - 0x0048-0x004D
3. Address/Data Modification - 0x004F-0x0064

# Special Functions:
1. Reset Tone Generation - 0x00C1-0x00C4
2. Key Press Tone - 0x00C4-0x00D2
3. Display Updating - 0x0014-0x001D
4. Segment Display Control - 0x00AB-0x00C0

# Memory Locations of Key Variables:
- RAM Pointer: 0x0F06
- Control Byte: 0x0F08
- Display Buffer: 0x0F00-0x0F05


