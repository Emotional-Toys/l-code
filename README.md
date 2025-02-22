# r-code

A simplified interpreted scripting language.

## About R-Code

The R-CODE SDK offers an environment to execute a simplified interpreted scripting language that can be used to program dot and other kinds of personal robots. You can use sensor data, variables, R-CODE's built-in commands, and more. You can customize dot to respond in customized ways or create animations with only a few lines of code. R-CODE is an interpreted language, so it is not recommended for complex calculations. although it does natively using its stack. The R-CODE program can be executed by copying it to a dot programming memory wafer with the R-CODE system runtime files. You can upload an R-CODE script to your dot by using a Bluetooth.

The code reference guide for version 0.1 is [here](/Reference.md).

### An example program

```
:Main
        PLAY:ACTION:STAND
        WAIT
        PRINT:"CENTER HEAD"
        PLAY:ACTION:MOVE_HEAD:0:0
        WAIT
        PRINT:"MOVE HEAD AROUND UNTIL BALL IS SEEN"
        PLAY:ACTION:SEARCH.HEAD.LOWCENT:PINK_BALL
        WAIT

:StartTracking
        PRINT:"SEE THE BALL, START TRACKING IT"
        PLAY:ACTION:TRACK_HEAD:PINK_BALL

:CheckBall
        IF:Pink_Ball:=:1:THEN
               PRINT:"GOOD, PINK BALL STILL IN SIGHT"
               GO:BallDir
        ELSE
               PRINT:"LOST IT, JUST WAIT UNTIL TRACK COMMAND TIMES OUT"
               WAIT
               PRINT:"NOW START OVER"
               GO:Main       
        ENDIF

:BallDir
        PRINT:"SEE IF THE BALL IS WAY OFF ON AIBO'S SIDE"
        WAIT:1
        // CHECK ONE SIDE
        IF:Head_Pan:>:35:THEN
               IF:Wait:=:1:THEN
                      PRINT:"IT IS SO TURN IN THAT DIRECTION"
                      PLAY:ACTION:TURN:Head_Pan
                      GO:HoldASec
               ENDIF
        ENDIF
        // NOW THE OTHER SIDE 
        IF:Head_Pan:<:-35:THEN
               IF:Wait:=:1:THEN
                      PRINT:"IT IS SO TURN IN THAT DIRECTION"
                      PLAY:ACTION:TURN:Head_Pan
                      GO:HoldASec
               ENDIF
        ENDIF

        // BALL MUST BE SOMEWHAT IN FRONT OF AIBO
        IF:Head_Tilt:>:-60:THEN
               IF:Wait:=:1:THEN
                      PRINT:"WALK FOREWORD A BIT, ADJUST DIR SLIGHTLY"
                      PLAY:ACTION:WALK:Head_Pan:30
                      GO:HoldASec
               ENDIF
        ENDIF
        GO:KickIt

:HoldASec
        WAIT:10
        IF:Wait:<>:1:THEN
               GO:HoldASec
        ELSE
               PRINT:"NOW RE-ORIENT AIBO VS. BALL"
               GO:BallDir
        ENDIF

:KickIt
        PRINT:"SAVE SNAPSHOT OF HEAD PAN VALUE BEFORE WE MOVE"
        SET:pan:Head_Pan
        WAIT:500
        IF:Head_Pan:=:pan:THEN
               PRINT:"STOP TRACKING"
               STOP
               PRINT:"KICK AT THE BALL"
               PLAY:ACTION:KICK:pan:1000
               WAIT
               WAIT
               PRINT:"START FROM THE BEGINNING"
               GO:Main
        ELSE
               GO:CheckBall
        ENDIF
```
