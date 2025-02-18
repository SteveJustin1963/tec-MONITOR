more below



```

+----------------------------------------------------------------------------------------+
|                                     START (0xBA4)                                       |
|                                          |                                              |
|                                          v                                              |
|                             Initialize Stack (SP=RET_STACK)                            |
|                                          |                                              |
|                                          v                                              |
|                               initInput (0x387)                                         |
|                                          |                                              |
|                                          v                                              |
|                               initSerial (0x9A1)                                       |
|                                          |                                              |
|                                          v                                              |
|                               initKernel (0x4F0)                                       |
|                                          |                                              |
|                                          v                                              |
+------------------+               REPL LOOP (0x429)                              +-------+
                   |                       |                                      |
                   v                       v                                      |
          +----------------+     INTERPRET (0x42F)     +-----------------------+ |
          |                |              |            |                       | |
          |  Serial I/O    |              v            |   Memory Operations   | |
          |                |     parseWord (0x8E9)     |                      | |
          | serialIn 0x9B8 |              |            |    AT     (0x6F5)    | |
          | getc    0xA05  |              v            |    STORE  (0x6FD)    | |
          | putc    0x9F4  |     Check length > 0?     |    COMMA  (0x72E)    | |
          |                |         |          |       |    ALLOT  (0x780)    | |
          +----------------+      Yes |          | No   |                       | |
                                     v          |       +-----------------------+ |
                              Check STATE       |                                 |
                                |              |                                  |
                     STATE=1    |              |                                  |
            +-------------------|              |                                  |
            |                  | STATE=0       |                                  |
            v                  v              |                                  |
    compileWord (0x4A7)  interpWord (0x465)  |                                  |
            |                  |              |                                  |
            v                  v              |                                  |
    FIND (0x31B) -----> FIND1 (0x322)        |                                  |
            |                  |              |                                  |
     Not Found|       Found    v              |                                  |
            |           FIND2 (0x339)         |                                  |
            v                  |              |                                  |
  Compile as Number    FIND3 (0x368)         |                                  |
            |                  |              |                                  |
            |           FIND4 (0x36D)         |                                  |
            |                  |              |                                  |
            v                  v              |                                  |
    Check Immediate   getBody (0x379)         |                                  |
      |         |            |               |                                  |
 Immediate|     |No         v               |                                  |
      |         |     EXECUTE (0x188)       |                                  |
      v         v            |               |                                  |
Execute    Compile           |               |                                  |
Immediately  CALL            |               |                                  |
      |         |            |               |                                  |
      |         |            |               |                                  |
      +---------|------------|---------------+                                  |
                |            |                                                  |
                +------------+--------------------------------------------------+

Dictionary Structure:
+----------------------------------------------------------------------------------------+
| HEADER (format)                                                                         |
| +-------------+--------+--------------+-------------+                                    |
| | Link (2B)   | Flags  | Name Length  | Name ...    |                                  |
| +-------------+--------+--------------+-------------+                                    |
|                                                                                         |
| Key Entry Points:                                                                       |
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
