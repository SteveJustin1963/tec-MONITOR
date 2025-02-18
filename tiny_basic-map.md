
    
[Online FlowChart & Diagrams Editor - Mermaid Live Editor.pdf](https://github.com/user-attachments/files/18838387/Online.FlowChart.Diagrams.Editor.-.Mermaid.Live.Editor.pdf)


```
            +--------+
            |  INIT  |
            +--------+
                |
                v
            +----------+
            |Power On  |
            +----------+
                |
                v
    +------------------------+
    |Initialize Serial Port  |
    +------------------------+
                |
                v
    +------------------------+
    |Display Boot Messages   |
    +------------------------+
                |
                v
        +----------------+
    +-->| Main REPL Loop |<--------------------+
    |   +----------------+                    |
    |           |                             |
    |           v                             |
    |   +----------------+                    |
    |   |  User Input    |                    |
    |   +----------------+                    |
    |           |                             |
    +---+-------+-------+                     |
        |               |                     |
        v               v                     |
+---------------+ +------------------+        |
|Line w/ Number | | Direct Command   |        |
+---------------+ +------------------+        |
        |               |                     |
        v               v                     |
+---------------+ +------------------+        |
|Store/Replace  | |Command Processing|        |
| Program Line  | +------------------+        |
+---------------+          |                  |
        |                  v                  |
        |          +----------------+         |
        |          | Command Types  |         |
        |          +----------------+         |
        |                  |                  |
        |    +------+------+------+-------+   |
        |    |      |      |      |       |   |
        |    v      v      v      v       v   |
        | +-----+ +-----+ +-----+ +-----+ +-----+
        | | RUN | |LIST | | NEW | |PRINT| |INPUT|
        | +-----+ +-----+ +-----+ +-----+ +-----+
        |    |      |      |      |       |
        |    v      v      v      v       v
        | +----------------+
        | | Execute Command|
        | +----------------+
        |         |
        |         v
        |  +---------------+
        |  | Find First    |
        |  | Program Line  |
        |  +---------------+
        |         |
        |         v
        |  +---------------+
        |  | Execute Line  |
        |  | Statements    |
        |  +---------------+
        |         |
        |   +-----+-----+
        |   |           |
        |   v           v
        | +-------+ +-------------+
        | |Success| |Syntax Error |
        | +-------+ +-------------+
        |   |              |
        |   |              v
        |   |        +-------------+
        |   |        |Error Handling|
        |   |        +-------------+
        |   |              |
        |   |              v
        |   |        +-------------+
        |   |        |Display Error|
        |   |        +-------------+
        |   |              |
        +---+--------------+
```


# Command Table

## Direct Commands (TAB1)
1. LIST    ; Address: 0x116F
2. RUN     ; Address: 0x1140
3. NEW     ; Address: 0x1131

## Statement and Direct Commands (TAB2)
1. NEXT    ; Address: 0x1289
2. LET     ; Address: 0x1362
3. IF      ; Address: 0x12EC
4. GOTO    ; Address: 0x115F
5. GOSUB   ; Address: 0x11ED
6. RETURN  ; Address: 0x120F
7. REM     ; Address: 0x12E7
8. FOR     ; Address: 0x122A
9. INPUT   ; Address: 0x1307
10. PRINT  ; Address: 0x1186
11. OUT    ; Address: 0x1712
12. STOP   ; Address: 0x113A
13. XON    ; Address: 0x1733
14. XOFF   ; Address: 0x1737
15. (Default handler)  ; Allows bare variable assignment

## Built-in Functions (TAB4)
1. RND     ; Random number generator
2. ABS     ; Absolute value
3. PEEK    ; Memory value retrieval
4. SIZE    ; Get free memory size

## Comparison Operators (TAB8)
```
1. >=      ; Greater than or equal
2. #     ; Not equal
3. >       ; Greater than
4. =       ; Equal to
5. <=      ; Less than or equal
6. <       ; Less than
```

## Control Characters Supported
1. CTRL-C  ; Interrupt/Restart
2. CTRL-O  ; Toggle output
3. CTRL-D  ; Exit to monitor
4. CTRL-Z  ; Delete line
5. Backspace ; Character deletion

## Special Input/Output Commands
1. OUT     ; Output to port
2. XON     ; Enable output
3. XOFF    ; Disable output


There are NO transcendental functions in this Tiny Basic implementation.

# The current function support is very basic:
1. RND     ; Random number generator
2. ABS     ; Absolute value
3. PEEK    ; Memory value retrieval
4. SIZE    ; Get free memory size

# There are no:
- Trigonometric functions (sin, cos, tan)
- Logarithmic functions
- Exponential functions
- Square root
- Other advanced mathematical functions

This is typical of early microcomputer BASIC interpreters, 
which had very limited mathematical capabilities due to memory and processing constraints.

If you wanted transcendental functions, you would need to manually extend the interpreter by:

1. Adding function definitions
2. Updating the function table (TAB4)
3. Implementing the mathematical calculations
4. Potentially adding a mathematical library

