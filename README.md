# letty-code

A simplified interpreted language for [letty](https://github.com/Emotional-Toys/animals-letty).

## About L-Code

The L-CODE SDK offers an environment to execute a simplified interpreted scripting language that can be used to program dot and other kinds of personal robots. You can use sensor data, variables, L-CODE's built-in commands, and more. You can customize dot to respond in customized ways or create animations with only a few lines of code. L-CODE is an interpreted language, so it is not recommended for complex calculations. although it does natively using its stack. The L-CODE program can be executed by copying it to a dot programming memory wafer with the L-CODE system runtime files. You can upload an L-CODE script to your dot by using a Bluetooth. This is the operating construct for our robotic product offerings, such as letty and [dot](https://github.com/Emotional-Toys/animals-dot).

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
        PRINT:"SEE IF THE BALL IS WAY OFF ON LETTY'S SIDE"
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

        // BALL MUST BE SOMEWHAT IN FRONT OF LETTY
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
               PRINT:"NOW RE-ORIENT LETTY VS. BALL"
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
