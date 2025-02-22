## R-Code v1.2 Reference Guide

The code reference for R-Code, version 1.2. It might be also valid for version 3 (ERS-7).

### Index

Here.

## Descriptives

### _Text Formatting Requirements_

* Lines must be less than 127 characters (comments included).
* Only alphanumeric & underscore characters `A-Z`, `0-9`, and `_`  should be used in variable names.
* All upper-case words are reserved for R-Code. Use lower-case, or mixed-case for variable names.
* Spaces & tab can be used to indent commands for readability.
* R-Code commands & parameters must be separated by colon characters `:`.

Example:
```
SET:my_var_name:123
PLAY:ACTION:STAND
```

### _Comments_

You can place comments - text which the interpreter skips - by using double-slashes `//`.

Example:
```
PLAY:ACTION:STAND // Make AIBO stand up
```

### _Jump Labels_

Lines starting with colon characters define jump labels. Can be used with `IF`, `GO`, and `CALL` commands, among others. 

Example:
```
// Make AIBO sit & stand repeatedly forever...
:JumpHere
PLAY:ACTION:SIT
WAIT
PLAY:ACTION:STAND
WAIT
GO:JumpHere
```

### _Numbers_

* Decimal numbers start with `0` to `9` or plus/minus signs `+`, `-`.
* Hexadecimal numbers (base 16) must start with `0x` or `0X` and may contain digits `0` to `9`, and `A` to `F`.
* Octal numbers (base 8) must start with `0o` or `0O` and may contain digits `0` to `7`.
* Binary numbers (base 2) must start with `0b` or `0B` and may contain digits `0` and `1`.

Examples:
```
SET:decnum:123          // Positive decimal number
SET:posnum:+123         // Positive decimal number
SET:negnum:-456         // Negative decimal number
SET:hexnum:0xABC        // Hexadecimal number (2748 in decimal)
SET:octnum:0o34567      // Octal number (14711 in decimal)
SET:binnum:0b01010101   // Binary number (85 in decimal) 
```

### _Control Commands_

| Command |                   Action                   |
|:-------:|:------------------------------------------:|
|   EDIT  | Load new program                           |
|   END   | End loading program                        |
|   RUN   | Begin program (make AIBO perform program). |
|   EXIT  | Stop program                               |
|   HALT  | Shutdown AIBO (power off)                  |
|   INIT  | Restart AIBO (reinitialize)                |
|    !    | Force program to stop.                     |
|    !!   | Stop program.                              |

#### `EDIT` - Load New Program

Place AIBO into program load mode. Any previously loaded program is erased. Normally used for transferring programs from your PC to AIBO, such as with Aibnet. Stop program load with END command.

Example:
```
EDIT
// Make AIBO sit & stand repeatedly forever...
:JumpHere
PLAY:ACTION:SIT
WAIT
PLAY:ACTION:STAND
WAIT
GO:JumpHere
END
```

#### `END` - End Program Load Mode

Use only in conjunction with `EDIT`.   See above.

#### `RUN` - Begin Program

Make AIBO start performing program loaded with `EDIT`/`END` commands.

#### `EXIT` - Stop Program

Make AIBO stop performing program. Can be used within a loaded _running_ program to stop itself. Any skit being played will continue to run until finished.

#### `HALT` - Shutdown AIBO

Make AIBO stop performing program and shutdown. Can be used within a loaded _running_ program to make AIBO turn itself off - for example if the battery gets low.

#### `INIT` - Restart/Reinitialize AIBO

Restart the R-Code parser. 

Usage:
   `INIT:<init-level>`

Only use values `2` or `9`. All others are reserved or undefined.

Examples:
```
INIT:2   // Reset dictionary & variable tables.
INIT:9   // Shutdown & reboot AIBO
```

#### `!` - Force Program to Stop

Force AIBO to stop performing program, and cancel playback immediately also. Leaves AIBO in an unknown posture.

#### `!!` - Normal Program Stop

Make AIBO to stop performing program. Same as `EXIT` command. Any skit being played will continue to run until finished.

### _Action/Skit Commands_

|       Command      |                                            Action                                           |
|:------------------:|:-------------------------------------------------------------------------------------------:|
|        PLAY        | Perform skit/action (add to a queue)                                                        |
|        WAIT        | Pause R-Code program (not skit playback).                                                   |
|        STOP        | Empty skit/action queue, and stop autorepeating skits (such as tail wagging).               |
|        QUIT        | Empty skit/action queue, and immediately stop skit playback.                                |

#### `PLAY` - Perform skit/action

Add a skit or action to the playback queue. You can dispatch several skits at once, and AIBO will perform them sequentially in turn. The R-Code program continues to run while the skit is being performed. If you want to pause the program until the skit finishes, see the WAIT command.

Usage:
   `PLAY:ACTION:skitname[:optional parameters]`

Note some builtin actions require parameters:

* Walk/kick/touch actions need an angle and distance (in millimeters).
* Turn actions need an angle.
* Head movement actions need horizontal (left/right) and vertical angles (up/down).
* Search/Track actions need a target, which must be PINK_BALL.

Angles are in degrees. Negative angles mean right. Positive angles means left.

* `0` is straight-ahead. 
* `90` means left.
* `-90` means right.   
* `-180` is backwards.

Examples:
```
// Perform the beethoven5 skit (present in DogsLife)
PLAY:ACTION:beethoven5
WAIT // wait for skit to finish
  
PLAY:ACTION:STAND         // Stand up (if not already doing so)
WAIT
  
PLAY:ACTION:WALK:0:500    // Walk 500mm forwards
WAIT
  
PLAY:ACTION:KICK:45:200   // Kick something 200mm away at 45 degrees
WAIT
  
PLAY:ACTION:TOUCH:45:200  // Touch something 200mm away at 45 degrees
WAIT
  
PLAY:ACTION:TURN:360      // Turn in place completely around.
WAIT
  
PLAY:ACTION:MOVE_HEAD:-45:-20 // Look right and down
WAIT
  
PLAY:ACTION:SEARCH:PINK_BALL  // Search for the ball
WAIT
```

#### `WAIT` - Pause Program 

Pause R-Code program temporarily from doing anything else.

Usage:
```
   WAIT              Pause until playback queue empty.  Queued skits continue.
                     Does nothing if nothing queued or being performed.
   WAIT:<number>     Wait number of milliseconds (1 to 30000).
   WAIT:<part>       R-Code 1.2 and above.   Wait until given part of AIBO idle.
```
The time/number parameter has a resolution of 32 milliseconds, e.g,

* Values `1` to `32` equal 32
* Values `33` to `64` equal 64

The part parameter can be one of the following
```
   MOTION_ALL      All motions
   MOTION_HEAD     Head motion
   MOTION_LEG      Leg motion
   MOTION_TAIL     Tail motion
   MOTION_MOUTH    Chin motion
   MOTION_EAR      Ear motion
   SOUND_ALL       All sounds
   LED_ALL         All lights
   LED_FACE        Face (eye) lights
   LED_MODE        Mode lights
   LED_TAIL        Tail lights
```
Examples:
```
WAIT:2000   // Pause R-Code for two seconds.
  
PLAY:ACTION:STAND
WAIT        // Pause R-Code until STAND action finished.
```

#### `STOP` - Empty Playback Queue
Empty the skit/action playback queue, and stop autorepeating skits once last iteration finishes.

Example:
```
PLAY:ACTION:PALONE.AUTO.TAILH // Wag tail horizontally...
WAIT:2000                     // ...for two seconds
STOP
```

#### `QUIT` - Empty Playback Queue, and Stop Playback Immediately

Empty the skit/action playback queue, and all playback immediately.   Can leave AIBO in an unknown posture, forcing a stretch to reset things.   Not recommended for normal use.

### _Debug Commands_

|       Command       |                                 Action                                |
|:-------------------:|:---------------------------------------------------------------------:|
|        PRINT        | Display a message, or contents of one or more variables               |
|        VDUMP        | Show value of variable                                                |

#### `PRINT` - Display a Message
Display a message, and optionally the value of one or more variables. Useful only if connected over WLAN.

Usage:
   `PRINT:"message"[:one or more variables]`

Message must be enclosed in quotes.   You also must use either `%d` or `%x` within the message to position displayed variable values. `%d` for decimal, `%x` for hexadecimal.

Example:
```
// Show current position of head...
PRINT:"Head_Tilt=%d   Head_Pan=%d":Head_Tilt:Head_Pan
```
Output appears as follows:
```
Head_Tilt=-45   Head_Pan=-20
```

#### `VDUMP` - Show value of variable
Second method for showing variable value. Outputs variable name and its value to the WLAN console.

Usage:
   `VDUMP:variable_name`

Example:
```
// Show current position of head...
VDUMP:Head_Tilt
```

Output appears as follows:
```
Head_Tilt = -45
```

### _Jump/Loop Commands_

|      Command     |                                           Action                                           |
|:----------------:|:------------------------------------------------------------------------------------------:|
|        GO        |  Jump to label                                                                             |
|        IF        |  Conditional test                                                                          |
|      SWITCH      | Assign Context for CASE statement                                                          |
|       CSET       | Conditional Test and Context Assign for CASE statement                                     |
|       CASE       | Perform parameter if Context matches value                                                 |
|        FOR       | Start FOR loop.   Repeat commands inside FOR/NEXT specified number of times.               |
|       NEXT       | End FOR loop                                                                               |
|       WHILE      | Start WHILE loop.   Repeat commands inside WHILE/WEND while test true.                     |
|       WEND       | End WHILE loop                                                                             |
|      REPEAT      | Start REPEAT loop.   Repeat commands inside REPEAT/UNTIL until test true.                  |
|       UNTIL      | End REPEAT loop                                                                            |
|        DO        | Start DO loop.   Repeat commands inside DO/LOOP until test true.                           |
|       LOOP       | End DO loop                                                                                |
|       BREAK      | Break out of loop                                                                          |

#### `GO` - Jump to label
The R-Code program moves to jump label.

Usage:
   `GO:label`

Example:
```
// Make AIBO sit & stand repeatedly forever...
:JumpHere
PLAY:ACTION:SIT
WAIT
PLAY:ACTION:STAND
WAIT
GO:JumpHere
```

#### `IF` - Conditional test
Compare two numbers or variables, and perform parameter accordingly. There are several different flavors of the `IF` command.

Version 1
```
   IF:<value1>:<op>:<value2>:THEN
      // command block (performed only if comparison succeeds)
   ENDIF
```
Version 2
```
   IF:<value1>:<op>:<value2>:THEN
     // then block (performed if comparison succeeds)
   ELSE
     // else block (performed if comparison fails)
   ENDIF
```
Version 3
```
   IF:<value1>:<op>:<value2>:CALL:label[:argument-count]
     If comparison succeeds, perform CALL command and jump to subroutine
       at label with optional argument count (see CALL command for details).
```
Version 4
```
   IF:<value1>:<op>:<value2>:BREAK
     If comparison succeeds, perform BREAK command and exit nearest loop.
```
Version 5
```
   IF:<value1>:<op>:<value2>:match-label
     If comparison succeeds, jump to label "match-label".
```
Version 6
```
   IF:<value1>:<op>:<value2>:match-label:else-label
     If comparison succeeds, jump to label "match-label".
     If comparison fails, jump to label "else-label".
```
_Values_
   The `<value>` fields can be either variables or numbers.

_Operators_
   The <op> field can be one of the following:
```
     =    Equals (compare succeeds if both values equal)
     ==   Equals (works same as above)
     <>   Not Equal
     !=   Not Equal (works same as above).
     <    Less than
     <=   Less than, or Equal
     >    Greater than
     >=   Greater than, or Equal
     &    Succeed if bitwise AND of values is non-zero
     |    Succeed if either value non-zero (bitwise OR of values)
     ^    Succeed if bitwise XOR of values is non-zero
     &&   Succeed if both values non-zero
     ||   Succeed if either value non-zero
```
Examples:
```
:JumpHere
IF:Back_ON:>:0:THEN
  SET:Back_ON:0
  PRINT:"Back Sensor Pressed"
ENDIF
  
IF:Jaw_ON:==:0:JumpHere
SET:Jaw_ON:0
PRINT:"Jaw Sensor Pressed"
GO:JumpHere
```

#### `SWITCH` - Assign Context for `CASE` statement
Set context for multiple branching with `CASE` statement. A useful shorthand for comparing a value against multiple numbers. Should be immediately followed by one or more `CASE` statements.

Usage:
   `SWITCH:<value1>`

Example:
```
SWITCH:some_var
  CASE:1:PRINT:"some_var = 1"
  CASE:2:PRINT:"some_var = 2"
  CASE:3:PRINT:"some_var = 3"
  CASE:4:PRINT:"some_var = 4"
  CASE:5:SET:some_var:55
  CASE:6:GO:JumpHere
  CASE:ELSE:PRINT:"some_var = Unknown"
```

#### `CSET` - Conditional test and Context Assign for `CASE` statement
Perform comparison and set context if successful.    On a successful match, subsequent CSET's are skipped.

Usage:
   `CSET:<value1>:<op>:<value2>:<context-value>`

Example:
```
CSET:some_var:<:100:1
CSET:some_var:<:200:2
CSET:some_var:<:300:3
CSET:some_var:<:400:4
CSET:some_var:<:500:5
CSET:some_var:<:500:6
CASE:1:PRINT:"some_var < 100"
CASE:2:PRINT:"some_var < 200"
CASE:3:PRINT:"some_var < 300"
CASE:4:PRINT:"some_var < 400"
CASE:5:SET:some_var:55
CASE:6:GO:JumpHere
```

#### `CASE` - Perform parameter if Context matches value
Compare context to parameter value. If equal, perform command.  Alternately, can use `ELSE` instead of value. In which case, command is performed if all previous `CASE` statements failed.

Version 1
   `CASE:<value>:<command>`

Version 2
   `CASE:ELSE:<command>`

Example:
```
SWITCH:some_var
  CASE:1:PRINT:"some_var = 1"
  CASE:2:PRINT:"some_var = 2"
  CASE:3:PRINT:"some_var = 3"
  CASE:4:PRINT:"some_var = 4"
  CASE:5:SET:some_var:55
  CASE:6:GO:JumpHere
  CASE:ELSE:PRINT:"some_var = Unknown"
```

#### `FOR` / `NEXT` - Repeat nested commands
Repeat nested commands specified number of times. Variable gets set to `<from-value>`, and is then incremented by `1` on each pass until matches `<to-value>`.  The optional `<step-value>` can change the increment, or even make it negative for counting backwards.

Version 1
```
   FOR:<variable>:<from-value>:<to-value>
     // nested commands
   NEXT
```
Version 2
```
   FOR:<variable>:<from-value>:<to-value>:<step-value>
     // nested commands
   NEXT
```
Examples:
```
// Make AIBO sit & stand five times...
FOR:some_var:1:5
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
NEXT
  
// Count backwards and display 50,40,30,20,10,0
FOR:some_var:50:0:-10
  PRINT:"some_var = %d":some_var
NEXT
```

#### `WHILE` / `WEND` - Repeat nested commands while test true
Repeat nested commands while comparison succeeds.  Note: If comparison fails on first attempt, nested commands are not performed at all! Comparison occurs before nested commands performed.

Usage:
```
   WHILE:<value1>:<op>:<value2>
     // nested commands
   WEND
```
See `IF` command for list of available operators (the `<op>` field).

Example:
```
// Make AIBO sit & stand while back not pressed...
WHILE:Back_ON:==:0
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
WEND
```

#### `REPEAT` / `UNTIL` - Repeat nested commands until test true
Repeat nested commands until comparison succeeds. Nested commands always performed at least once. Comparison occurs after nested commands performed.

Usage:
```
   REPEAT
     // nested commands
   UNTIL:<value1>:<op>:<value2>
```
See `IF` command for list of available operators (the `<op>` field).

Example:
```
// Make AIBO sit & stand until back pressed...
REPEAT
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
UNTIL:Back_ON:>:0
```

#### `DO` / `LOOP` - Repeat nested commands while or until test true
Repeat nested commands. Optionally supports comparing values both before and after nested commands performed.

Usage:
```
   DO[:WHILE|UNTIL:<value1>:<op>:<value2>]
     // nested commands
   LOOP[:WHILE|UNTIL:<value1>:<op>:<value2>]
```
See `IF` command for list of available operators (the `<op>` field).

Examples:
```
// Make AIBO sit & stand forever...
DO
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
LOOP
  
// Make AIBO sit & stand while back not pressed...
DO:WHILE:Back_ON:==:0
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
LOOP
  
// Make AIBO sit & stand while back not pressed...
DO:UNTIL:Back_ON:>:0
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
LOOP
  
// Make AIBO sit & stand while neither jaw or back not pressed...
DO:WHILE:Jaw_ON:==:0
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
LOOP:UNTIL:Back_ON:>:0
```

#### `BREAK` - Break out of loop
Break out of loops.   Convenient for exiting a loop early, perhaps if a sensor detects something unusual.  Works with `FOR`/`NEXT`, `WHILE`/`WEND`, `REPEAT`/`UNTIL`, or `DO`/`LOOP`.

You can break out of multiple nested loops with a single `BREAK` by giving the optional level parameter.

Usage:
   `BREAK[:<level>]`

Examples:
```
// Make AIBO sit & stand until back pressed...
DO
  PLAY:ACTION:SIT
  WAIT
  PLAY:ACTION:STAND
  WAIT
  IF:Back_ON:>:0:BREAK
LOOP
  
FOR:some_var:1:10
  FOR:another_var:1:10
    IF:some_var:>5:THEN
      BREAK:2 // exit both FOR loops
    ENDIF
    PRINT:"%d %d":some_var:another_var
  NEXT
NEXT
```

### _Subroutine Commands_

| Command |                  Action                  |
|:-------:|:----------------------------------------:|
|   CALL  | Call subroutine                          |
|   ARG   | Retrieve subroutine argument             |
|  RETURN | Return from subroutine                   |
|   RET   | Return from subroutine (context version) |

#### `CALL` - Call a Subroutine
The R-Code program moves to the given label, but remembers where it came from. Using `RET` or `RETURN` resumes at the old location. You can pass arguments to subroutines by `PUSH`ing them beforehand. Arguments allow reusing a subroutine in different places, without dedicating variables. See ARG command for details.

Usage:
   `CALL:label[:argument-count]`

Example:
```
:JumpHere
CALL:SayHello
GO:JumpHere
  
:SayHello
PRINT:"Hello"
RET
```

#### `ARG` - Get Stack Argument for Subroutine
Extracts an argument `PUSH`ed before calling a subroutine. The same number of arguments must be retrieved as were pushed. The first argument pushed is the first retrieved.

Usage:
   `ARG:<variable>`

Example:
```
:JumpHere
PUSH:10
PUSH:Back_ON
PUSH:42
CALL:MySubroutine:3    // Tell subroutine there are 3 arguments
PRINT:"Back Again"
GO:JumpHere
  
// Subroutine starts here...
:MySubroutine
ARG:somevar1
ARG:somevar2
ARG:somevar3
PRINT:"Subroutine Args = %d %d %d":somevar1:somevar2:somevar3
RET
```

#### `RETURN` - Return from Subroutine (stack version)
Return to R-Code position following `CALL` command. If returning optional value, then use the `POP` command to retrieve result.

Usage:
   `RETURN[:<value>]`

Example:
```
:JumpHere
CALL:MySubroutine
POP:somevar
PRINT:"Subroutine result = %d":somevar
GO:JumpHere
  
:MySubroutine
PRINT:"Entered MySubroutine"
RETURN:123
```

#### `RET` - Return from Subroutine (context version)
Return to R-Code position following `CALL` command. Return value placed into Context variable, for use with `CASE` statement. If value not given, or zero is returned, original Context value on entry to subroutine is returned.

Usage:
  `RET:<new-context-value>`

Example:
```
CALL:MySubroutine
CASE:1:GO:JumpHere
CASE:2:GO:JumpThere
CASE:3:GO:JumpSomewere
// etc...
  
:MySubroutine
PRINT:"Entered MySubroutine"
RET:1
```

### _Variable Declare and Assign_

|       Command       |                         Action                        |
|:-------------------:|:-----------------------------------------------------:|
|        GLOBAL       | Declare global variable                               |
|        LOCAL        | Declare local variable                                |
|         LET         | Assign variable a value                               |
|         SET         | Assign variable (plus system functions)               |
|         CLR         | Clear sensor variable                                 |
|        VSAVE        | Save variable value to memory stick                   |
|        VLOAD        | Load variable value from memory stick                 |

#### `GLOBAL` - Declare Global Variable
Define a global variable visible to entire program.  Not generally needed, since AIBO makes all unknown new variables global by default. Can be used to optionally assign a value.

Usage:
   `GLOBAL:<variable>[:<value>]`

Example:
```
GLOBAL:somevar               // Declare variable, defaults to zero.
GLOBAL:anothervar:123        // Declare another, defaulted to 123.
```

#### `LOCAL` - Declare Local Variable
Define a local variable visible in current scope only. Scope means something like a subroutine, or loop, e.g., `WHILE`/`WEND`.

Example:
```
LOCAL:somevar               // Declare variable, defaults to zero.
LOCAL:anothervar:123        // Declare another, defaulted to 123.
```

#### `LET` - Assign Variable a Value
Specify value variable to be equal to.

Usage:
   `LET:<variable>:<value>`

Example:
```
LET:somevar:123       // Assign user variable
```

#### `SET` - Assign Variable (plus system functions)
Specify value variable to be equal to. Also can clear sensor variables. Generally you can safely use `SET` for all assignments as there are only special reasons to use `LET`.

Usage:
   `SET:<variable>:<value>`

Once the system sets the following variables, the only way to clear them is using `SET`.
```
   Head_ON
   Head_OFF
   Head_Hit       
   Head_Pat       
   Head_LONG      
   Back_ON        
   Back_OFF       
   Back_LONG      
   Jaw_ON         
   Jaw_OFF        
   Jaw_LONG       
   RFLeg_ON       
   RFLeg_OFF      
   LFLeg_ON       
   LFLeg_OFF      
   RRLeg_ON       
   RRLeg_OFF      
   LRLeg_ON       
   LRLeg_OFF      
```
Examples:
```
SET:somevar:123       // Assign user variable
SET:Head_ON:0         // Clear Head_ON sensor
```

#### `CLR:SENSORS` - Clear Sensor Variables
Sets to zero all persistent sensor variables for those which store previous values.

Usage:
   `CLR:SENSORS`


#### `VSAVE` - Save Variable
Save variable value to memory stick. The variable name is used as a filename with a maximum name length of 8 characters. Use `VLOAD` to read it back. Good for storing learned information.

Usage:
   `VSAVE:<variable>`

Example:
```
VSAVE:somevar   // Save value to /OPEN-R/APP/PC/AMS/SOMEVAR.SAV
```

#### `VLOAD` - Load Variable
Load value from memory stick. The variable name is used as a filename with a maximum name length of 8 characters.

Usage:
   `VLOAD:<variable>`

Example:
```
VLOAD:somevar   // Load value stored in file /OPEN-R/APP/PC/AMS/SOMEVAR.SAV
```

### _Stack Commands_
The following commands operate on the stack. R-Code manages the stack in the same manner as Forth: The last item placed on the stack is the first one removed.

|      Command     |                         Action                         |
|:----------------:|:------------------------------------------------------:|
|       PUSH       | Add value to stack                                     |
|        POP       | Remove value from stack                                |
|        DUP       | Duplicate value on top of stack                        |
|        JT        | Jump if top-most value on stack non-zero               |
|        JF        | Jump if top-most value on stack zero                   |

#### `PUSH` - Add Value to Stack
Add variable or constant value onto the stack.

Usage:
   `PUSH:<value>`

Example:
```
PUSH:42
PUSH:somevar
MUL             // Multiply somevar by 42.  Result left on stack.
```

#### `POP` - Remove Value from Stack
Remove top-most value from stack. If variable not specified, top-most stack value is discarded. If stack is empty, `POP` can cause an error.

Usage:
   `POP[:<variable>]`

Examples:
```
POP:somevar    // Remove top-most value from stack, and store in somevar
POP            // Discard next value on stack
```

#### `DUP` - Duplicate Value on Stack
Duplicate/copy top-most value on stack.

Example:
```
PUSH:7
DUP           // duplicate value of 7 on stack (now there are two 7's)
ADD
POP:somevar   // somevar equals 14
```

#### `JT` - Jump if True
Remove top-most value from stack. If non-zero, jump to label.

Usage:
   `JT:<label>`

Example:
```
JT:JumpHere     // POP topmost value from stack.  If non-zero, jump.
```

#### `JF` - Jump if False
Remove top-most value from stack. If zero, jump to label.

Usage:
   `JF:<label>`

Example:
```
JF:JumpHere     // POP topmost value from stack.  If zero, jump.
```

### _Operators_

The following commands perform arithmetic on variables and value parameters.

|             Variable            |                                       Value Parameter                                      |
|:-------------------------------:|:------------------------------------------------------------------------------------------:|
| ADD:<var>:<value>               | Add value to variable.    variable = variable + value                                      |
| SUB:<var>:<value>               | Subtract value from variable.    variable = variable / value                               |
| MUL:<var>:<value>               | Multiply variable by value.    variable = variable * value                                 |
| DIV:<var>:<value>               | Divide variable by value.    variable = variable / value                                   |
| MOD:<var>:<value>               | Remainder of dividing variable by value.    variable = variable % value                    |
| AND:<var>:<value>               | Bitwise AND variable with value.   variable = variable & value                             |
| IOR:<var>:<value>               | Bitwise OR variable with value.   variable = variable \| value                             |
| XOR:<var>:<value>               | Bitwise XOR variable with value.   variable = variable ^ value                             |
| NOT:<var>                       | Bitwise invert variable.   variable = ~variable                                            |
| LAND:<var>:<value>              | Logical AND.   If both variable and value non zero, set to 1.   Otherwise 0.               |
| LIOR:<var>:<value>              | Logical OR.   If either variable or value non zero, set to 1.   Otherwise 0.               |
| LNOT:<var>                      | Logical NOT.  If variable zero, set to 1.   Otherwise 0.                                   |
| RND:<var>:<from>:<to>           | Set variable to random number between <from> and <to>.                                     |

Examples:
```
ADD:somevar:123
SUB:anothervar:456
  
SET:somevar:0x55
AND:somevar:0xAA    // somevar equals 0
  
SET:somevar:0x55
IOR:somevar:0xAA    // somevar equals 255 (0xFF in hex)
  
SET:somevar:0x55
LAND:somevar:0xAA   // somevar equals 1 (both non-zero)
SET:somevar:0
LAND:somevar:0xAA   // somevar equals 0 (because one value zero)
  
SET:somevar:0
LIOR:somevar:0      // somevar equals 0 (both zero)
SET:somevar:0
LIOR:somevar:0xAA   // somevar equals 1 (one value non-zero)
SET:somevar:0x55
LIOR:somevar:0      // somevar equals 1 (one value non-zero)
  
SET:somevar:0x5555
NOT:somevar         // somevar equals 0xAAAA
  
SET:somevar:0
LNOT:somevar        // somevar equals 1
SET:somevar:0x5555
LNOT:somevar        // somevar equals 0
  
RND:somevar:1:10    // somevar set to random value between 1 and 10
```

### _Stack Operators_

The following commands perform arithmetic on the stack. R-Code manages the stack in the same manner as Forth: The last item placed on the stack is the first one removed. To do math with the stack, first `PUSH` two values then perform the operation and `POP` the result. This is also called _postfix_ notation, as, `3 4 ADD`.

|        Variable        |                                     Value Parameter                                     |
|:----------------------:|:---------------------------------------------------------------------------------------:|
| ADD                    | Add value to variable.   <push> = <pop2> + <pop1>                                       |
| SUB                    | Subtract value from variable.   <push> = <pop2> - <pop1>                                |
| MUL                    | Multiply variable by value.    <push> = <pop2> * <pop1>                                 |
| DIV                    | Divide variable by value.    <push> = <pop2> / <pop1>                                   |
| MOD                    | Remainder of dividing variable by value.    <push> = <pop2> % <pop1>                    |
| AND                    | Bitwise AND variable with value.   <push> = <pop2> & <pop1>                             |
| IOR                    | Bitwise OR variable with value.   <push> = <pop2> \| <pop1>                             |
| XOR                    | Bitwise XOR variable with value.   <push> = <pop2> ^ <pop1>                             |
| NOT                    | Bitwise invert variable.   <push> = ~<pop>                                              |
| LAND                   | Logical AND.   If both <pop1> & <pop2> non zero, <push> 1.   Otherwise 0.               |
| LIOR                   | Logical OR.   If either <pop1> or <pop2> non zero, <push> 1.   Otherwise 0.             |
| LNOT                   | Logical NOT.  If <pop> value zero, <push> 1.   Otherwise 0.                             |
| EQ                     | If <pop2> and <pop1> equal, <push> 1.   Otherwise 0.                                    |
| NE                     | If <pop2> and <pop1> not equal, <push> 1.   Otherwise 0.                                |
| LT                     | If <pop2> less than <pop1>, <push> 1.   Otherwise 0.                                    |
| LE                     | If <pop2> less than or equal to <pop1>, <push> 1.   Otherwise 0.                        |
| GT                     | If <pop2> greater than <pop1>, <push> 1.   Otherwise 0.                                 |
| GE                     | If <pop2> greater than or equal to <pop1>, <push> 1.   Otherwise 0.                     |
| RND:<from>:<to>        | <push> random value between <from> and <to>                                             |
| RND:<to>               | <push> random value between zero and <to>                                               |

Examples:
```
PUSH:200
PUSH:100
SUB
POP:somevar         // result is 100 (200-100)
  
PUSH 100
PUSH 2
PUSH 4
PUSH 6
ADD
MUL
DIV
POP:somevar         // result is 5 = 100/((4+6)*2)
```

### _System Variables_

|               Variable              |                             Value Parameter                            |
|:-----------------------------------:|:----------------------------------------------------------------------:|
| AiboId                              | Least significant byte of WLAN IP address. Zero if not connected.      |
| AiboType                            | 210, 220, 310, or 7                                                    |
| Year                                | Year (2000 or later)                                                   |
| Month                               | Month (1-12)                                                           |
| Day                                 | Date (1-31)                                                            |
| Hour                                | Hour (0-23)                                                            |
| Min                                 | Minute (0-59)                                                          |
| Sec                                 | Second (0-59) - 2 second resolution                                    |
| Dow                                 | Day of week (0=Sunday, 1=Monday, 2=Tuesday, ..., 6=Saturday)           |
| Seed                                | Random number seed (default is 1)                                      |
| Status                              | Startup Status. 0=Normal, 1=Recovery (from falling over)               |
| Context                             | Context value                                                          |
| Wait                                | Number of actions/skits queued for playback                            |
| Clock                               | Clock (incremented by 1 every 32 ms)                                   |
| Brightness                          | Ambient brightness (0-255)                                             |
| Pink_Ball                           | Pink ball. Set 1 once detected (clear w/SET)                           |
| Pink_Ball_H                         | Pink ball horizontal angle (in degrees, 0=center, pos=left, neg=right) |
| Pink_Ball_V                         | Pink ball vertical angle (in degrees, 0=center, neg=down, pos=up)      |
| Pink_Ball_D                         | Distance to pink ball from nose camera (in millimeters)                |
| AU_Voice                            | Set if voice command recognised (clear w/SET)                          |
| AU_Voice_ID                         | Voice ID (1-53)                                                        |
| AU_AiboSound                        | Set 1 if AiboSound detected (clear w/SET)                              |
| AU_AiboSound_ID AiboSound ID (1-35) |                                                                        |
| AU_AiboTone                         | Set 1 if AiboTone detected (clear w/SET)                               |
| AU_AiboTone_ID                      | AiboTone ID (1-68)                                                     |
| Temp_Hi                             | Set if temperature too high. AIBO will auto-shutdown in 20 seconds.    |
| LFLeg_1                             | Left-front Hip Joint (front/back direction, in degrees)                |
| LFLeg_2                             | Left-front Hip Joint (left/right direction, in degrees)                |
| LFLeg_3                             | Left-front Knee Joint (degrees)                                        |
| LRLeg_1                             | Left-hind Hip Joint (front/back direction, in degrees)                 |
| LRLeg_2                             | Left-hind Hip Joint (left/right direction, in degrees)                 |
| LRLeg_3                             | Left-hind Knee Joint (degrees)                                         |
| RFLeg_1                             | Right-front Hip Joint (front/back direction, in degrees)               |
| RFLeg_2                             | Right-front Hip Joint (left/right direction, in degrees)               |
| RFLeg_3                             | Right-front Knee Joint (degrees)                                       |
| RRLeg_1                             | Right-hind Hip Joint (front/back direction, in degrees)                |
| RRLeg_2                             | Right-hind Hip Joint (left/right direction, in degrees)                |
| RRLeg_3                             | Right-hind Knee Joint (degrees)                                        |
| Batt_Rest                           | Battery charge remainder [%]                                           |
| Batt_Temp                           | Battery temperature [C]                                                |
| Body_Temp                           | Body temperature [C]                                                   |
| Distance                            | Distance to obstacle [mm]                                              |
| Head_ON                             | Set if head sensor pressed (clear w/SET)                               |
| Head_LONG                           | Set if head sensor pressed for 3 or more seconds (clear w/SET)         |
| Head_Pat                            | Set if head sensor stroked back and forth twice (clear w/SET)          |
| Head_Hit                            | Set if head sensor touched briefly and forcefully (clear w/SET)        |
| Head_OFF                            | Amount of time head sensor pressed [msecs]                             |
| Back_ON                             | Set if back sensor pressed (clear w/SET)>                              |
| Back_LONG                           | Set if back sensor pressed for 3 or more seconds (clear w/SET)         |
| Back_OFF                            | Amount of time back sensor was pressed [msecs]                         |
| RFLeg_ON                            | Set if right front paw sensor was pressed (clear w/SET)                |
| RFLeg_OFF                           | Amount of time right front paw sensor was pressed [msecs]              |
| LFLeg_ON                            | Set if left front paw sensor was pressed (clear w/SET)                 |
| LFLeg_OFF                           | Amount of time left front paw sensor was pressed [msecs]               |
| RRLeg_ON                            | Set if right hind paw sensor was pressed (clear w/SET)                 |
| RRLeg_OFF                           | Amount of time right hind paw sensor was pressed [msecs]               |
| LRLeg_ON                            | Set if left hind paw sensor was pressed (clear w/SET)                  |
| LRLeg_OFF                           | Amount of time left hind paw sensor was pressed [msecs]                |

### _Platform Specific Variables_
```
ERS-210
   Head_Tilt       Head Up/Down Angle (degrees) 
   Head_Pan        Head Left/Right Angle (degrees)
   Head_Roll       Head Roll Angle (side-to-side, degrees)
   Mouth           Mouth (degrees)
   Jaw_ON          Set if chin sensor pressed (clear w/SET)
   Jaw_OFF         Length of time face sensor was pressed [ms]
   Jaw_LONG        Set if chin sensor was pressed for 3 or more seconds (clear w/SET)
   Tail_Pan        Tail: horizontal (left/right) angle [degrees]
   Tail_Tilt       Tail: Vertical (up/down) angle [degrees]
  
ERS-220
   Head_Tilt       Head: vertical (up-down) angle [degrees]
   Head_Pan        Head: horizontal (left-right) angle [degrees]
   Head_Roll       Head: roll angle [degrees]
   Jaw_ON          Face sensor pressed (clear w/SET)
   Jaw_OFF         Length of time face sensor was pressed [ms]
   Jaw_LONG        Face sensor was pressed for 3 seconds or longer (clear w/SET)    
  
ERS-310
   Head_Tilt       Head: vertical (up-down) angle 1 [degrees]
   Head_Tilt_2     Head: vertical (up-down) angle 2 [degrees]
   Head_Pan        Head: horizontal (left-right) angle [degrees]

   Tail_Roll3      Tail rotated 3 times (in either direction)(clear w/SET)
   Tail_RollR      Tail rotated clockwise (clear w/SET)
   Tail_RollL      Tail rotated counterclockwise (clear w/SET)
   Tail_U_LONG     Tail pressed forward 3 seconds (clear w/SET)
   Tail_D_LONG     Tail pressed backward 3 seconds (clear w/SET)
   Tail_R_LONG     Tail pressed right 3 seconds (clear w/SET)
   Tail_L_LONG     Tail pressed left 3 seconds (clear w/SET)
   Tail_U_ON       Tail pressed forward (clear w/SET)
   Tail_D_ON       Tail pressed backward (clear w/SET)
   Tail_R_ON       Tail pressed right (clear w/SET)
   Tail_L_ON       Tail pressed left (clear w/SET)
```

#### END