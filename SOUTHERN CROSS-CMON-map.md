more info below

```
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
```



# **RESTART (RST)** instructions 
in Z80 assembly are a form of software interrupt or jump to predefined locations in memory. 
These are effectively shorthand for `CALL` instructions to fixed addresses in the first 64 bytes of memory (from `0000H` to `0038H`).

### **Summary**
- **RST instructions are fixed-address software interrupts** used to handle system-level operations efficiently.
- **CMON uses RST vectors as a dispatch table**, allowing software to change function pointers dynamically.
- **RST 0 (`0000H`) is the reset entry point**, initializing the system.
- **RST 38 (`0038H`) handles normal interrupts**, while **RST 66 (`0066H`) is for NMIs**.


### **How RST Works**
Each **RST n** instruction acts as a **CALL** to a fixed memory location:
- `RST 0` → `0000H`
- `RST 8` → `0008H`
- `RST 10` → `0010H`
- `RST 18` → `0018H`
- `RST 20` → `0020H`
- `RST 28` → `0028H`
- `RST 30` → `0030H`
- `RST 38` → `0038H`

These locations are usually occupied by jump instructions to actual subroutines in the monitor (CMON in this case).

---

### **How RST is Used in CMON**
Your **CMON (Craig's Monitor)** implementation uses the RST instructions as a jump table for handling system functions. Each restart vector loads an address from a table and jumps to it. Let's break them down:

#### **Power-On / Reset Handling (`RST 0`)**
```assembly
ORG 0000H
RSTVEC      JP RESET
```
- When power is applied, the Z80 starts execution at `0000H`, jumping to the **RESET** routine.
- The `RESET` routine initializes the system.

---

#### **General Restart Handling (`RST 8H` to `RST 28H`)**
Each restart vector follows this format:
```assembly
ORG 0008H
PUSH HL
LD HL,(RST08)
JP (HL)
```
This pattern repeats for:
- `RST 08H`
- `RST 10H`
- `RST 18H`
- `RST 20H`
- `RST 28H`

These store addresses in RAM (`RST08`, `RST10`, etc.), allowing for **dynamic vectoring**—the actual function being called can change during runtime.

---

#### **System Call Handler (`RST 30H`)**
```assembly
ORG 0030H
RST30       JP SYSCALL
```
- Calls a **system call handler** at `SYSCALL`.
- The `SYSCALL` routine uses a jump table indexed by register `C`, allowing software to request different system functions.

---

#### **Interrupt Handling (`RST 38H`)**
```assembly
ORG 0038H
PUSH HL
LD HL,(RST38)
JP (HL)
```
- This is the **Interrupt Service Routine (ISR)**.
- If an interrupt occurs, the Z80 automatically jumps to `0038H`.
- The handler redirects to an address stored in `RST38`.

---

#### **Non-Maskable Interrupt (`RST 66H`)**
```assembly
ORG 0066H
PUSH HL
LD HL,(RST66)
JP (HL)
```
- The Z80 **always jumps to `0066H` when an NMI occurs**.
- Like the standard interrupts, this system allows for dynamic handling by using an indirect jump.



# Hex load

### **How the HEX Load Works in CMON**
The HEX load function in **CMON** is responsible for receiving **Intel HEX format** data over the **bit-banged serial port** and writing it to memory. The function responsible for this is **`INTELH`**.

### **Summary**
- **The HEX loader waits for `:`** to start reading a line.
- **It reads and decodes each part**: byte count, address, record type, data, and checksum.
- **Data is written to memory** at the specified address.
- **The checksum is verified** to ensure data integrity.
- **Errors or user interrupts cause an abort**.


---

## **Intel HEX Format Overview**
Intel HEX files are commonly used for firmware uploads. The format consists of ASCII-encoded hexadecimal data with a specific structure:

```
:10 1000 00 214601360121470136007EFE09D21901
|  |    |  |                              |  
|  |    |  |                              └── Checksum
|  |    |  └────────────────── Data bytes (Actual code/data)
|  |    └────────── Record type (00=Data, 01=EOF, etc.)
|  └──────── Start Address (16-bit, where data is written)
└────────── Byte Count (Number of data bytes)
```

### **Breakdown of a HEX Line**
1. **Start Code** (`:`) - Indicates the start of a line.
2. **Byte Count** (`10H`) - Number of data bytes in the line.
3. **Address** (`1000H`) - The 16-bit memory location for the first data byte.
4. **Record Type** (`00H`) - Defines the type of record.
   - `00` = Data
   - `01` = End of file
   - `02` = Extended segment address
   - `04` = Extended linear address
5. **Data Bytes** (`214601...`) - The actual data stored at the given address.
6. **Checksum** (`01H`) - The two's complement checksum of the entire line.

---

## **CMON's HEX Load Routine (`INTELH`)**
The function `INTELH` follows these steps:

1. **Wait for Start (`:`)**
   ```assembly
   INTEL1      XOR A
               LD (IX+3),A         ; CLEAR CHECKSUM
               CALL RXDATA         ; RECEIVE BYTE
               JR C,INTEL5         ; ERROR IF CARRY SET
               CP ':'              ; CHECK FOR START CHARACTER
               JR NZ,INTEL1        ; WAIT UNTIL `:` RECEIVED
   ```
   - Clears checksum storage (`IX+3`).
   - Calls `RXDATA` to receive a character from serial input.
   - If it’s not `':'`, it loops until it gets the proper start.

2. **Read Byte Count**
   ```assembly
   CALL GETBYT
   JR C,INTEL5
   LD (IX+0),A  ; STORE BYTE COUNT
   ```
   - Calls `GETBYT` to read the number of data bytes in this line.
   - Stores it in `(IX+0)`, which holds the count.

3. **Read Address**
   ```assembly
   CALL GETBYT
   JR C,INTEL5
   LD (IX+2),A  ; HIGH BYTE OF ADDRESS
   CALL GETBYT
   JR C,INTEL5
   LD (IX+1),A  ; LOW BYTE OF ADDRESS
   ```
   - Reads the **16-bit address** (high and low bytes).
   - Stores it in `(IX+2)` and `(IX+1)`.

4. **Read Record Type**
   ```assembly
   CALL GETBYT
   JR C,INTEL5
   JR NZ,INTEL4  ; IF NOT `00`, HANDLE SEPARATELY
   ```
   - Reads the **record type**.
   - If it's not a **data record (`00`)**, jumps to **INTEL4** (EOF check).

5. **Read and Store Data Bytes**
   ```assembly
   LD B,(IX+0)  ; BYTE COUNT
   LD H,(IX+2)  ; HIGH ADDRESS BYTE
   LD L,(IX+1)  ; LOW ADDRESS BYTE

   INTEL2      CALL GETBYT
               JR C,INTEL5
               LD (HL),A  ; STORE BYTE IN MEMORY
               INC HL
               DJNZ INTEL2  ; LOOP UNTIL ALL BYTES READ
   ```
   - Loads the **starting address**.
   - Reads **each byte** from the serial port using `GETBYT`.
   - Stores each byte into memory at `(HL)`.
   - Decrements the byte count and loops until all data is stored.

6. **Checksum Verification**
   ```assembly
   LD A,(IX+3)  ; COMPUTE CHECKSUM
   NEG
   LD (IX+4),A  ; STORE COMPUTED CHECKSUM
   CALL GETBYT
   JR C,INTEL5
   LD (IX+3),A  ; STORE RECEIVED CHECKSUM
   CP (IX+4)    ; COMPARE CHECKSUMS
   JR Z,INTEL1  ; IF MATCH, READ NEXT LINE
   ```
   - The checksum is calculated by **adding all received bytes** (excluding the start character `:`) and taking the **two's complement**.
   - Reads the **checksum byte** from the serial input.
   - If the computed checksum matches the received one, it proceeds to the next record.

7. **End of File Handling**
   ```assembly
   INTEL4      LD A,(IX+3)
               NEG
               LD (IX+4),A
               CALL GETBYT
               JR C,INTEL5
               LD (IX+3),A
               CP (IX+4)
               JR NZ,INTEL3  ; CHECKSUM ERROR
   ```
   - If the record type was `01` (EOF), it verifies the checksum and exits if correct.

---

## **Error Handling**
- If an error occurs (e.g., bad checksum, missing data), the monitor displays an error message:
  ```assembly
  INTEL3      JP BLKMV1  ; Show error
  ```

- If the user **interrupts the process**, it shows an **abort message** (`A` for abort).

---

## **Serial Input Handling (`RXDATA` & `GETBYT`)**
The actual **serial communication** occurs in:
- **`RXDATA`** → Receives bytes from the serial port.
- **`GETBYT`** → Converts received **ASCII** characters to **hex values**.

### **How `GETBYT` Works**
```assembly
GETBYT      PUSH BC
            CALL RXDATA
            JR C,GETBT3
            BIT 6,A
            JR Z,GETBT1
            ADD A,09H
GETBT1      AND 0FH
            SLA  A
            SLA A
            SLA A
            SLA A
            LD C,A
```
1. Calls `RXDATA` to get a **single ASCII character**.
2. Converts it to a **hex value** (`0-9`, `A-F`).
3. Shifts the **high nibble** into position.
4. Gets the **low nibble** and combines them.

---

## **Final Output**
If the HEX file is **successfully loaded**, the monitor **displays ‘C’** (`Complete`).

If an **error occurs**, it **displays ‘E’** (`Error`).

---



