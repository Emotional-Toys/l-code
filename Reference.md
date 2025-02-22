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

