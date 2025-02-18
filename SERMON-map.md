# IMPLEMENTED WORDS (Verified from main.z80.lst)

## Core Words:
```
    COMPILE (0x46)  - Compile the next address
    LITERAL (0x69)  - Compile tos as integer literal
    CREATE  (0x7C)  - Create new dictionary entry
    HIDDEN  (0xC1)  - Hide/unhide word
    :       (0xDE)  - Begin colon definition
    [       (0xFD)  - Enter interpretation mode
    ]       (0x109) - Enter compilation mode
    ;       (0x115) - End colon definition
```

## Control Flow:
```
    ?branch (0x15D) - Conditional branch
    branch  (0x170) - Unconditional branch
    execute (0x188) - Execute word
    for     (0x195) - Start FOR-NEXT loop
    next    (0x1AE) - End FOR-NEXT loop
    ahead   (0x1C4) - Forward branch
    if      (0x1E4) - Begin conditional
    else    (0x206) - Alternative path
    then    (0x218) - End conditional
    aft     (0x22D) - Special loop construct
    begin   (0x243) - Start loop
    until   (0x254) - End conditional loop
    again   (0x26A) - End unconditional loop
    repeat  (0x281) - End WHILE loop
    while   (0x29B) - Conditional in loop
```

## Constants and Variables:
```
    rp0    (0x2A9) - Return stack initial value
    true   (0x2B8) - Constant -1
    false  (0x2C8) - Constant 0
    bl     (0x2D5) - Space character constant
    base   (0x2E4) - Number base variable
    here   (0x2F3) - Dictionary pointer
    latest (0x304) - Most recent word
    state  (0x314) - Compiler state
```

## Stack/Memory Operations:
```
    0<     (0x517) - Test if negative
    and    (0x524) - Bitwise AND
    or     (0x536) - Bitwise OR
    xor    (0x549) - Bitwise XOR
    not    (0x55C) - Bitwise NOT
    =      (0x56B) - Test equality
    U<     (0x587) - Unsigned less than
    <      (0x5AB) - Less than
    >      (0x5C9) - Greater than
    max    (0x5D5) - Maximum
    min    (0x5EE) - Minimum
    negate (0x60D) - Two's complement
    /      (0x61B) - Division
    U*     (0x64D) - Unsigned multiply
    +      (0x676) - Addition
    -      (0x686) - Subtraction
    1+     (0x69C) - Increment
    1-     (0x6A4) - Decrement
    cells  (0x6AF) - Size of cell
    2*     (0x6B5) - Multiply by 2
    2/     (0x6C0) - Divide by 2
    2+     (0x6CB) - Add 2
    2-     (0x6DD) - Subtract 2
```


# Memory Access:
```
    @      (0x6F5) - Fetch
    !      (0x6FD) - Store
    +!     (0x711) - Add to memory
    ,      (0x72E) - Comma (compile)
    c,     (0x741) - Compile byte
    allot  (0x780) - Allocate space
```

## Stack Manipulation:

```
    dup    (0xA60) - Duplicate top
    drop   (0xA6F) - Remove top
    swap   (0xA7E) - Exchange top two
    over   (0xA91) - Copy second to top
    nip    (0xAA2) - Remove second
    tuck   (0xAAD) - Copy top under second
    rot    (0xABA) - Rotate top three
    2dup   (0xACE) - Duplicate top pair
    2drop  (0xADD) - Remove top pair
    2swap  (0xAEB) - Exchange top two pairs
    2over  (0xB01) - Copy second pair to top
    2nip   (0xB1B) - Remove second pair
    depth  (0xB29) - Stack depth
```

## I/O and Other:

```
    KEY    (0x3A1) - Get character
    word   (0x8BF) - Parse word
    .      (0x98D) - Print number
    COUNT  (0xB49) - String length
    ok     (0xB91) - Print ok message
```

```
+----------------------------------------------------------------------------------------+
|                                     START (0xBA4)                                      |
|                                          |                                             |
|                                          v                                             |
|                             Initialize Stack (SP=RET_STACK)                            |
|                                          |                                             |
|                                          v                                             |
|                               initInput (0x387)                                        |
|                                          |                                             |
|                                          v                                             |
|                               initSerial (0x9A1)                                       |
|                                          |                                             |
|                                          v                                             |
|                               initKernel (0x4F0)                                       |
|                                          |                                             |
|                                          v                                             |
+------------------+               REPL LOOP (0x429)                              +------+
                   |                       |                                      |
                   v                       v                                      |
          +----------------+     INTERPRET (0x42F)     +------------------------+ |
          |                |              |            |                        | |
          |  Serial I/O    |              v            |   Memory Operations    | |
          |                |     parseWord (0x8E9)     |                        | |
          | serialIn 0x9B8 |              |            |    AT     (0x6F5)      | |
          | getc    0xA05  |              v            |    STORE  (0x6FD)      | |
          | putc    0x9F4  |     Check length > 0?     |    COMMA  (0x72E)      | |
          |                |         |          |      |    ALLOT  (0x780)      | |
          +----------------+     Yes |          | No   |                        | |
                                     v          |       +-----------------------+ |
                              Check STATE       |                                 |
                                |               |                                 |
                     STATE=1    |               |                                 |
            +-------------------|               |                                 |
            |                   | STATE=0       |                                 |
            v                   v               |                                 |
    compileWord (0x4A7)  interpWord (0x465)     |                                 |
            |                  |                |                                 |
            v                  v                |                                 |
    FIND (0x31B) -----> FIND1 (0x322)           |                                 |
            |                  |                |                                 |
     Not Found|       Found    v                |                                 |
            |           FIND2 (0x339)           |                                 |
            v                  |                |                                 |
  Compile as Number    FIND3 (0x368)            |                                 |
            |                  |                |                                 |
            |           FIND4 (0x36D)           |                                 |
            |                  |                |                                 |
            v                  v                |                                 |
    Check Immediate      getBody (0x379)        |                                 |
         |         |            |               |                                 |
Immediate|      No |            v               |                                 |
         |         |   EXECUTE (0x188)          |                                 |
         v         v            |               |                                 |
     Execute    Compile         |               |                                 |
     Immediately  CALL          |               |                                 |
         |         |            |               |                                 |
         |         |            |               |                                 |
         +---------|------------|---------------+                                 |
                   |            |                                                 |
                   +------------+-------------------------------------------------+

Dictionary Structure:
+----------------------------------------------------------------------------------------+
| HEADER (format)                                                                        |
| +-------------+--------+--------------+-------------+                                  |
| | Link (2B)   | Flags  | Name Length  | Name ...    |                                  |
| +-------------+--------+--------------+-------------+                                  |
|                                                                                        |
| Key Entry Points:                                                                      |
| - LATEST (0x304) -> Most recent dictionary entry                                       |
| - HERE   (0x2F3) -> Current dictionary compilation pointer                             |
| - STATE  (0x314) -> Compilation state (0=interpret, 1=compile)                         |
+----------------------------------------------------------------------------------------+

Control Flow Keywords:
+----------------------------------------------------------------------------------------+
| - IF     (0x1E4) -> Conditional branching                                              |
| - ELSE   (0x206) -> Alternative branch                                                 |
| - THEN   (0x218) -> End of conditional                                                 |
| - BEGIN  (0x243) -> Start of loop                                                      |
| - UNTIL  (0x254) -> Conditional loop end                                               |
| - AGAIN  (0x26A) -> Unconditional loop                                                 |
| - FOR    (0x195) -> Counting loop start                                                |
| - NEXT   (0x1AE) -> Counting loop end                                                  |
+----------------------------------------------------------------------------------------+
```
