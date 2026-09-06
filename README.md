# Taipan for CP/M

Yet another Taipan port, this time for MBASIC on CP/M

## Preamble

Another port of Taipan! Inspired by RC2014 hardware projects.

The Applesoft BASIC code from the book,  [TAIPAN - A historical adventure for the Apple Computer (PDF)][1] by *Art Canfil*, *Karl Albrecht*, and *Jim McClenahan*, linked to from [taipangame.com][2].

This was really painless port and took just an hour or two, to get running.

### Initial port

FWIW, my initial port used the Commodore PET version as a starting point, which was a mistake and which led to all sorts of confusion. You can still see the [weirdness](weirdness/), I haven't deleted it.

Most of the notes from the "weirdness" version apply here, I just didn't bother noting them down this time. Respectively, I have made note of the main points, below.


## See also

 - [Taipan for BBC BASIC](https://gr33nonline.wordpress.com/2023/12/12/taipan-for-bbc-basic/)

## Links

 - [Waiting for some time on Z80 CP/M](https://stackoverflow.com/q/65777890/4424636)

### Related repos

 - [!!!NOT!!! TRS80Taipan](https://github.com/greenonline/TRS80Taipan)
 - [MMBASICTaipan](https://github.com/greenonline/MMBASICTaipan)
 - [MacTaipan](https://github.com/greenonline/MacTaipan)
 - [Taipan_40_Column_Apple_II](https://github.com/greenonline/Taipan_40_Column_Apple_II)
 - [CommanderX16Taipan](https://github.com/greenonline/CommanderX16Taipan)
 - [BBCTaipan](https://github.com/greenonline/BBCTaipan)
 - [PETTaipan](https://github.com/greenonline/PETTaipan)

## Notes

### Paste in the code!

**IMPORTANT: This is still a WIP, and a "tidy" BASIC file is not yet complete.**

The `.txt` file is test working evolving BASIC code, containing multiple verions of the same line.

However, due to the nature of emulators (FWIW, I used `altairz80`), if you paste in the code, then only the last version of a particular line is accepted, so pasting in the mess of evolved code will actually result in a working program..!

### Limitations and differences

 - Graphics changes due to the nature of the terminal:
   - No horizontal scroll is possible. Therefore, the pirates arrival is not animated
   - There is no positional print, i.e. `PRINT AT`. Thus, the cannon shot holes have to be drawn at the same time as the ship. Therefore, there is no animated ship destruction
   - There is no downward vertical scroll. Therefore, there is no animated sinking ship

### Clone `TAIPAN_BAS_AppleII.txt`

The initial code to be used for MBASIC on CP/M is cloned from the Apple II version, `TAIPAN_BAS_AppleII.txt`.

I think that this might be a scrolling verson, as opposed to the more usual "full screen version", for the simple reason that `LOCATE` and `PRINT AT` (nor `VTAB` nor `HTAB`) are not valid commands in MBASIC on CP/M.


#### MBASIC love spaces after keywords

Just put spaces everywhere

#### `HOME`, `NORMAL`, `INVERSE` and `FLASH` not supported

As usual, turn into variables and assign 1.

#### Use of `INKEY$` instead of `POKE` and `PEEK`

Also fixes the `Type mismatch in 60` error.

#### Remove all `VTAB`

No supported nor required as line scrolling

#### `HTAB()` becomes to `PRINT TAB()`

#### Line length seems very long

Longer than MMBASIC and C64

#### Lower case support added

More conditionals required for key press checks

#### Line blanking not required

Remove semicolon after `A$` and `B$`

#### Too many blank lines

Maybe caused by the previous solution

TODO: Finish this

#### Reverse logic to skip

If the `FOR` is in an `IF` statement, then MBASIC can complain.

Line 731 is at fault here:

```none
725 REM EMBARK (730-751)
730 IF SH < 0 THEN VTAB 13:PRINT "YOUR SHIP IS OVERLOADED, TAIPAN         ";: PRINT A$:GOSUB 760:GOTO 360
730 IF SH < 0 THEN PRINT "YOUR SHIP IS OVERLOADED, TAIPAN         ";: PRINT A$:GOSUB 760:GOTO 360
731 IF SH > = 0 THEN PRINT TAB( 11);:INVERSE=1:PRINT "EMBARKING":NORMAL=1:PRINT TAB( 9);"FROM " ;L$ (L ) : INVERSE=1:PRINT A$:NORMAL=1:FOR I = 0 TO 9:IF L = I THEN NEXT I:GOTO 740
732 IF L <> I THEN PRINT TAB( 10);I;" ";L$(I): NEXT I
```

The first `IF` in 731 is not needed as the previous line 730 already accounts for the condition being true:


```none
725 REM EMBARK (730-751)
730 IF SH < 0 THEN VTAB 13:PRINT "YOUR SHIP IS OVERLOADED, TAIPAN         ";: PRINT A$:GOSUB 760:GOTO 360
730 IF SH < 0 THEN PRINT "YOUR SHIP IS OVERLOADED, TAIPAN         ";: PRINT A$:GOSUB 760:GOTO 360
731 PRINT TAB( 11);:INVERSE=1:PRINT "EMBARKING":NORMAL=1:PRINT TAB( 9);"FROM " ;L$ (L ) : INVERSE=1:PRINT A$:NORMAL=1:FOR I = 0 TO 9:IF L = I THEN NEXT I:GOTO 740
732 IF L <> I THEN PRINT TAB( 10);I;" ";L$(I): NEXT I
```

Still not enough, remove second `IF` cond from 731 and add line 733

```none
725 REM EMBARK (730-751)
730 IF SH < 0 THEN VTAB 13:PRINT "YOUR SHIP IS OVERLOADED, TAIPAN         ";: PRINT A$:GOSUB 760:GOTO 360
730 IF SH < 0 THEN PRINT "YOUR SHIP IS OVERLOADED, TAIPAN         ";: PRINT A$:GOSUB 760:GOTO 360
731 PRINT TAB( 11);:INVERSE=1:PRINT "EMBARKING":NORMAL=1:PRINT TAB( 9);"FROM " ;L$ (L ) : INVERSE=1:PRINT A$:NORMAL=1:FOR I = 0 TO 9
732 IF L <> I THEN PRINT TAB( 10);I;" ";L$(I)
733 NEXT I
```

  [1]: https://taipangame.com/pdf/TaipanAHistoricalAdventureForTheAppleComputerAppleIIEdition.pdf
  [2]: https://taipangame.com/

