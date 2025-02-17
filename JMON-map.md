```
                   POWER ON/RESET
                         │
                         ▼
                   Initialize JMON
                   [0000-006A]
                         │
                         ▼
              Save Registers & Setup
                   [006B-008F]
                         │
                         ▼
     ┌────────────────────────────────────┐
     │           Main Loop                │
     │    Update Display [00B2-00E1]      │
     └─────────────┬──────────────────────┘
                   │
                   ▼
            Check Input Mode
                   │
         ┌─────────┴──────────┐
         ▼                    ▼
    Data Mode           Address/Function Mode
    [00D0-00E1]         [0102-017F]
         │                    │
         │                    │
    ┌────┴────────────────────┘
    │
    ▼
Process Key Input [0181-01E5]
    │    
    ├─── "+" Key: Increment address
    │    
    ├─── "-" Key: Decrement address
    │    
    ├─── "GO" Key: Execute at current address
    │    
    ├─── "AD" Key: Return to monitor
    │    
    └─── Data Keys: Enter data at current address
         │
         ▼
    Auto-increment?
         │
    ┌────┴────┐
    │         │
    ▼         ▼
   Yes        No
    │          │
    │          └───────┐
    ▼                  ▼
Inc Address    Stay at Current Address
    │                  │
    └──────────────────┘
            │
            ▼
    Back to Main Loop
```


```
LED Display Scan Routine [01BA-01D4]
    │
    ├─── Initialize scan bit
    ├─── Output segment data
    ├─── Output scan bit
    ├─── Delay
    └─── Move to next digit

Key Input Processing [06AD-06D4]
    │
    ├─── Detect key press
    ├─── Debounce
    ├─── Validate
    └─── Process auto-repeat

LCD Interface [023C-026B]
    │
    ├─── Initialize LCD
    ├─── Check busy flag
    ├─── Send commands
    └─── Display data

Tape Operations [04F0-05FD]
    │
    ├─── Write leader
    ├─── Write data blocks
    ├─── Calculate checksums
    ├─── Read data
    └─── Verify checksums
```

```
              POWER ON/RESET [0000]
                         │
                         ▼
              Initialize JMON [0000-006A]
              Save Registers [006B-008F]
                         │
                ┌────────┴──────────┐
                ▼                   ▼
         Normal Monitor        SHIFT+0 or AD+0
         Operations            (Tape Software)
         │                         │
    ┌────┴────┐             ┌──────┴──────┐
    ▼         ▼             ▼             ▼
Update     Process      MENU DRIVER    Hardware
Display    Keys         [03E3]        Control
[00B2]     [0181]           │             │
    │         │        ┌────┴────┐        │
    └────┬────┘        ▼         ▼        │
         │          Forward    Backward   │
         │            (+)       (-)       │
         │             │         │        │
         │             └────┬────┘        │
         │                  ▼             │
         │       ┌─────────────────┐      │
         │       │Perimeter Handler│      │
         │       │     [0479]      │      │
         │       └────────┬────────┘      │
         │                ▼               │
         │         Parameter Entry        │
         │         [04F0-04FF]            │
         │               │                │
         │          ┌────┴────┐           │
         │          ▼         ▼           │
         │        SAVE       LOAD         │
         │         │          │           │
         │    ┌────┴────┐  ┌──┴───┐       │
         │    │  [0686] │  │[0630]│       │
         │    │Tone Gen │  │Decode│       │
         │    └────┬────┘  └──┬───┘       │
         │         │          │           │
         │    ┌────┴────┬─────┴───┐       │
         │    ▼         ▼         ▼       │
         │  High      Low     Checksum    │
         │  [0684]   [0680]   Verify      │
         │    │         │     [04F0]      │
         │    └────┬────┘      │          │
         │         ▼           │          │
         │    Data Blocks      │          │
         │    (256 bytes)      │          │
         │         │           │          │
         │         └─────┬─────┘          │
         │               ▼                │
         │      Status Display            │
         │      [05A1-05B4]               │
         │               │                │
         └───────────────┴────────────────┘

Hardware Control Layer:
----------------------
┌─────────────────────────────────┐
│ Display Buffer [082C/D]         │
│ LED Control [01BA-01D4]         │
│ Speaker Output [0680-0686]      │
│ Keyboard Input [0820]           │
│ Tape Interface [04F0-05FD]      │
└─────────────────────────────────┘

Memory Management:
-----------------
┌─────────────────────────────────┐
│ Current Edit Location [082E]    │
│ Monitor Control Byte [082B]     │
│ Auto Key Status [082A]          │
│ Key Press Flag [0825]           │
└─────────────────────────────────┘
```
