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
Values:
   The `<value>` fields can be either variables or numbers.

Operators:
   The <op> field can be one of the following:
*      `=`    Equals (compare succeeds if both values equal)
*      `==`   Equals (works same as above)
      `<>`   Not Equal
      `!=`   Not Equal (works same as above).
      `<`    Less than
      `<=`   Less than, or Equal
      `>`    Greater than
      `>=`   Greater than, or Equal
      `&`    Succeed if bitwise AND of values is non-zero
      `|`    Succeed if either value non-zero (bitwise OR of values)
      `^`    Succeed if bitwise XOR of values is non-zero
      `&&`   Succeed if both values non-zero
      `||`   Succeed if either value non-zero

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

