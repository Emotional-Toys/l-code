## R-Code v1.2 Reference Guide

The code reference for R-Code, version 1.2. It might be also valid for version 3 (ERS-7).

### Index

Here.

### Descriptives

_Text Formatting Requirements_

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

_Comments_

You can place comments - text which the interpreter skips - by using double-slashes `//`.

Example:
```
PLAY:ACTION:STAND // Make AIBO stand up
```

_Jump Labels_

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

_Numbers_

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

_Control Commands_

| Command |                   Action                   |
|:-------:|:------------------------------------------:|
|   EDIT  | Load new program                           |
|   END   | End loading program                        |
|   RUN   | Begin program (make AIBO perform program). |
|   EXIT  | Stop program                               |
|   HALT  | Shutdown AIBO (power off)                  |
|   INIT  | Restart AIBO (reinitialize)                |
|    !    | Force program to stop.                     |

