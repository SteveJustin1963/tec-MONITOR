# Complete MINT Command List

## Single Character Commands

### Symbol Commands
```
| Symbol | Address | Name | Description |
|--------|---------|------|-------------|
| `!` | 0CA8 | STORE_ | Store value at address |
| `"` | 0C5B | DUP_ | Duplicate top stack value |
| `#` | 0F5F | HEX_ | Process hex number |
| `$` | 0CAF | SWAP_ | Swap top two stack items |
| `%` | 0C86 | OVER_ | Copy second item to top |
| `&` | 0C00 | AND_ | Bitwise AND |
| `'` | 0C58 | DROP_ | Remove top stack item |
| `(` | 0D72 | BEGIN_ | Begin a loop |
| `)` | 0C28 | AGAIN_ | End a loop |
| `*` | 0F21 | MUL_ | Multiply |
| `+` | 0C22 | ADD_ | Addition |
| `,` | 0C52 | HDOT_ | Print hex number |
| `-` | 0CB4 | SUB_ | Subtraction |
| `.` | 0C47 | DOT_ | Print decimal number |
| `/` | 0D50 | DIV_ | Division |
| `:` | 0D2F | DEF_ | Begin definition |
| `;` | 0C8D | RET_ | End definition/return |
| `<` | 0CD2 | LT_ | Less than comparison |
| `=` | 0CC2 | EQ_ | Equal comparison |
| `>` | 0CCE | GT_ | Greater than comparison |
| `?` | 0C77 | KEY_ | Read input character |
| `@` | 0C6D | FETCH_ | Fetch value from address |
| `[` | 0C2B | ARRDEF_ | Begin array definition |
| `\` | 0CF7 | ALT_ | Alternate command prefix |
| `]` | 0D13 | ARREND_ | End array definition |
| `^` | 0C14 | XOR_ | Bitwise XOR |
| `_` | 0CBC | NEG_ | Negate value |
| ``` ` ``` | 0D04 | STR_ | Begin string |
| `{` | 0C9B | SHL_ | Shift left |
| `\|` | 0C0B | OR_ | Bitwise OR |
| `}` | 0CA0 | SHR_ | Shift right |
| `~` | 0C94 | ROT_ | Rotate top three items |
```
### Letter Commands
```
| Range | Address | Name | Description |
|-------|---------|------|-------------|
| A-Z | 0C3D | CALL_ | Execute user command |
| a-z | 0CE3 | VAR_ | Access variable |
```

## Backslash Commands
```
| Command | Address | Name | Description |
|---------|---------|------|-------------|
| `\!` | 0E37 | CSTORE_ | Store byte to memory |
| `\#0` | 0E43 | EXEC_ | Execute code at address |
| `\#1` | 0EB6 | ERET_ | Conditional early return |
| `\#2` | 0EC0 | UNLOOP_ | Pop loop frames |
| `\#3` | 0E4B | DEPTH_ | Get stack depth |
| `\#4` | 0EE3 | PRINTSTK_ | Print stack contents |
| `\#5` | 0E5E | PROMPT_ | Print MINT prompt |
| `\#6` | 0E59 | EDITDEF_ | Edit command |
| `\#7` | 0EB3 | ADUP_ | Duplicate address |
| `\@` | 0E27 | CFETCH_ | Fetch byte from memory |
| `\[` | 0E22 | CARRDEF_ | Char array definition |
| `\:` | 0E0A | ANONDEF_ | Anonymous definition |
| `\\` | 0E2E | COMMENT_ | Begin comment |
| `\^` | 0E63 | GO_ | Execute code |
| `\_` | 0EB6 | ERET_ | Early return |
| `\`` | 0EBE | STRDEF_ | Define string |
| `\,` | 0E3C | EMIT_ | Output character |
| `\.` | 0EA1 | PRNSTR_ | Print string |
| `\<` | 0E78 | INPORT_ | Input from port |
| `\>` | 0E98 | OUTPORT_ | Output to port |
| `\$` | 0E93 | NEWLN_ | Print newline |
| `{` | 0EAD | RPOP_ | Pop from return stack |
| `}` | 0EA7 | RPUSH_ | Push to return stack |
| `~` | 0E13 | BREAK_ | Break from loop |
```

## System Variables (\a-\h)
```
| Variable | Address | Description |
|----------|---------|-------------|
| `\a` | 2480 | Stack start (VS0) |
| `\b` | 2482 | Number base (VBASE16) |
| `\c` | 2484 | TIB pointer (VTIBPTR) |
| `\d` | 2486 | Unused (VNS) |
| `\e` | 2488 | Last definition (VLASTDEF) |
| `\f` | 248A | Unused |
| `\g` | 248C | Page limit (VALTPAGE) |
| `\h` | 248E | Heap pointer (VHEAPPTR) |
```

## Control Characters
```
| Character | Address | Name | Description |
|-----------|---------|------|-------------|
| ^B | 09D0 | TOGGLEBASE_ | Toggle base (hex/decimal) |
| ^E | 099F | EDIT_ | Edit command |
| ^H | 0980 | BACKSP_ | Backspace |
| ^J | 0997 | REEDIT_ | Re-edit last line |
| ^L | 09AB | LIST_ | List command |
| ^P | 09C8 | PRINTSTACK_ | Print stack |
```

## Loop Control
```
| Command | Address | Name | Description |
|---------|---------|------|-------------|
| `i` | 0E83 | I_ | Get current loop index |
| `j` | 0E89 | J_ | Get outer loop index |
```

## Number Processing
```
| Range | Address | Name | Description |
|-------|---------|------|-------------|
| 0-9 | 0F3D | NUM_ | Process decimal numbers |
```

## Special Operators
```
| Range | Address | Description |
|-------|---------|-------------|
| i | 0E83 | Inner loop index |
| j | 0E89 | Outer loop index |
| k-z | 0E00 | Alt variables |
```


## Memory Organization
```
| Name | Address | Size | Description |
|------|---------|------|-------------|
| TIB | 2000 | 256 | Text Input Buffer |
| RSTACK | 2180 | 128 | Return Stack |
| DSTACK | 2200 | 128 | Data Stack |
| LSTACK | 2280 | 128 | Loop Stack |
| OPCODES | 2300 | 128 | Operation Codes |
| MINTVARS | 2400 | 104 | MINT Variables |
| HEAP | 2528 | - | Free Memory |
```
## System Vectors
```
| Name | Address | Description |
|------|---------|-------------|
| VLOOPSP | 2430 | Loop stack pointer |
| VBYTEMODE | 2432 | Byte mode flag |
| GETCVEC | 2448 | Get char vector |
| PUTCVEC | 244A | Put char vector |
```

## Serial Configuration
```
| Rate | Value | Description |
|------|-------|-------------|
| B300 | 0220h | 300 baud |
| B1200 | 0080h | 1200 baud |
| B2400 | 003Fh | 2400 baud |
| B4800 | 001Bh | 4800 baud |
| B9600 | 000Bh | 9600 baud |
```

```
                              START (0A6D)
                                    |
                                    v
                            Initialize Stack & Variables
                            Setup Serial (0800-0811)
                                    |
                                    v
                            Print "MINT V1.1" (0A73)
                                    |
                                    v
                         +-----> INTERPRET (0A82) <-----------------+
                         |          Print Prompt                    |
                         |              |                           |
                         |              v                           |
                         |     Wait for Character (0A9F)            |
                         |              |                           |
                         |              v                           |
                         |     Process Character:                   |
                         |     < Space (< 0x20)?                    |
                         |     +--> Yes ---> Control/Macro (0AAE)   |
                         |     |              |                     |
                         |     |              +-------------------->+
                         |     |                                    |
                         |     +--> No ---> Add to TIB Buffer       |
                         |                    |                     |
                         |                    v                     |
                         |     Is it Return (0x0D)?                 |
                         |     +--> No ----> Continue Input         |
                         |     |                                    |
                         |     +--> Yes ---> Process Buffer         |
                         |                    |                     |
                         |                    v                     |
                         |            NEXT Routine (0AF7)           |
                         |                    |                     |
                         |                    v                     |
                         |            Dispatch Character:           |
                         |            - A-Z: User Commands          |
                         |            - a-z: Variables              |
                         |            - Others: Primitives          |
                         |                    |                     |
                         |                    v                     |
                         |            Execute Operation:            |
                         |            - Stack Operations            |
                         |            - Arithmetic (ADD,SUB,MUL)    |
                         |            - Logic (AND,OR,XOR)          |
                         |            - I/O Operations              |
                         |            - Flow Control                |
                         |                    |                     |
                         +--------------------+                     |
                                    |                               |
                                    v                               |
                            ETX Detected? (0C60)                    |
                            No ------------------------------------->

Key Commands & Addresses:

Stack Operations:
- DUP  (0C5B): Duplicate top of stack
- DROP (0C58): Remove top of stack
- SWAP (0CAF): Swap top two items
- OVER (0C86): Copy second item to top

Arithmetic:
- ADD_ (0C22): Addition
- SUB_ (0CB4): Subtraction
- MUL_ (0F21): Multiplication
- DIV_ (0D50): Division

Logic:
- AND_ (0C00): Bitwise AND
- OR_  (0C0B): Bitwise OR
- XOR_ (0C14): Bitwise XOR
- INV_ (0C1D): Bitwise Invert

I/O:
- KEY_    (0C77): Get input character
- EMIT_   (0E3C): Output character
- INPORT_ (0E78): Input from port
- OUTPORT_(0E98): Output to port

Flow Control:
- BEGIN_  (0CF5): Start loop
- AGAIN_  (0C28): End loop
- IF_     (Branch): Conditional
- ELSE_   (Branch): Alternative
```


```
MINT Complete System Flowchart

1. SYSTEM INITIALIZATION (0800-0811)
   |
   +-- SERIALINIT (0800)
   |   |-- Initialize RxChar vector (0800-0805)
   |   |-- Initialize TxChar vector (0806-080B)
   |   |-- Call InitialiseSerial (080C)
   |   └-- Jump to START (080F)
   |
   +-- Serial Communication
       |-- GETCHAR (0812-0815)
       |-- PUTCHAR (0816-081B)
       |-- InitialiseSerial (081C-082E)
       |-- TxChar (082F-0859)
       └-- RxChar (085A-0886)

2. MAIN PROGRAM FLOW
   |
   +-- START (0A6D)
   |   |-- Initialize Stack
   |   |-- Call INIT
   |   └-- Print "MINT V1.1"
   |
   +-- INTERPRET (0A82)
   |   |-- INTERPRET2 (0A8C): Calculate nesting
   |   |-- INTERPRET3 (0A94): Process chars
   |   |-- INTERPRET4 (0A9A): Loop control
   |   └-- WAITCHAR (0A9F): Input handling
   |
   +-- Character Processing
       |-- If char < 0x20: MACRO (0AAE)
       |-- If char >= 0x20: Store in TIB
       |-- If CR: Process buffer
       └-- If ETX: End processing

3. COMMAND EXECUTION
   |
   +-- NEXT (0AF7): Main dispatch
   |   |-- Get next character
   |   |-- Form jump address
   |   └-- Execute function
   |
   +-- Stack Operations (0C00-0CF7)
   |   |-- AND_ (0C00): Bitwise AND
   |   |-- OR_ (0C0B): Bitwise OR
   |   |-- XOR_ (0C14): Bitwise XOR
   |   |-- INV_ (0C1D): Invert
   |   |-- ADD_ (0C22): Addition
   |   |-- AGAIN_ (0C28): Loop end
   |   |-- ARRDEF_ (0C2B): Array definition
   |   |-- CALL_ (0C3D): Call command
   |   |-- DOT_ (0C47): Print number
   |   |-- HDOT_ (0C52): Print hex
   |   |-- DROP_ (0C58): Drop value
   |   |-- DUP_ (0C5B): Duplicate
   |   |-- ETX_ (0C60): End text
   |   |-- EXIT_ (0C63): Exit routine
   |   |-- FETCH_ (0C6D): Memory fetch
   |   |-- KEY_ (0C77): Get key
   |   |-- MUL_ (0C80): Multiply
   |   |-- NOP_ (0C83): No operation
   |   |-- OVER_ (0C86): Copy over
   |   |-- RET_ (0C8D): Return
   |   |-- ROT_ (0C94): Rotate
   |   |-- SHL_ (0C9B): Shift left
   |   |-- SHR_ (0CA0): Shift right
   |   |-- STORE_ (0CA8): Store
   |   |-- SWAP_ (0CAF): Swap
   |   └-- SUB_ (0CB4): Subtract

4. EXTENDED OPERATIONS (0D00-0E00)
   |
   +-- Comparison Operations
   |   |-- NEG_ (0CBC): Negate
   |   |-- EQ_ (0CC2): Equal
   |   |-- GT_ (0CCE): Greater than
   |   |-- LT_ (0CD2): Less than
   |   └-- CMP_ (0CD4): Compare
   |
   +-- Variable Operations
   |   |-- VAR_ (0CE3): Variable access
   |   |-- NUM_ (0CEA): Number processing
   |   └-- STR_ (0CED): String processing
   |
   +-- Flow Control
       |-- ARREND_ (0CEF): End array
       |-- DEF_ (0CF1): Definition
       |-- DIV_ (0CF3): Division
       |-- BEGIN_ (0CF5): Begin loop
       └-- ALT_ (0CF7): Alternate

5. UTILITY FUNCTIONS (0E00-0FFF)
   |
   +-- Page 6 Operations (0E00)
   |   |-- ALTVAR_ (0E00): Alt variables
   |   |-- ANOP_ (0E08): Alt NOP
   |   |-- ANONDEF_ (0E0A): Anonymous def
   |   |-- BREAK_ (0E13): Break loop
   |   |-- CARRDEF_ (0E22): Char array def
   |   |-- CFETCH_ (0E27): Char fetch
   |   |-- COMMENT_ (0E2E): Comments
   |   └-- More utility functions...
   |
   +-- Support Functions
       |-- PRINTDEC (0B8B): Print decimal
       |-- PRINTHEX (0BAF): Print hex
       |-- NESTING (0BCE): Nesting calc
       |-- RPUSH (0F8E): Return stack push
       |-- RPOP (0F99): Return stack pop
       └-- CRLF (0FD9): Print newline

6. MEMORY MAP
   |
   +-- ROM (0800-1FFF)
   |   |-- Code space
   |   └-- Constants
   |
   +-- RAM (2000-2FFF)
   |   |-- TIB (2000): Text Input Buffer
   |   |-- RSTACK (2180): Return Stack
   |   |-- DSTACK (2200): Data Stack
   |   |-- LSTACK (2280): Loop Stack
   |   |-- OPCODES (2300): Operation codes
   |   |-- MINTVARS (2400): MINT variables
   |   └-- HEAP (2528): Free memory

7. SPECIAL VECTORS
    |
    +-- VLOOPSP (2430): Loop stack pointer
    +-- VBYTEMODE (2432): Byte mode flag
    +-- GETCVEC (2448): Get char vector
    +-- PUTCVEC (244A): Put char vector
    +-- VLASTDEF (2488): Last definition
    └-- VHEAPPTR (248E): Heap pointer
```

