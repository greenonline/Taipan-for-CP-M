# Taipan for CP/M

Yet another Taipan port, this time for MBASIC on  CP/M

## Preamble

Another port of Taipan! Inspired by RC2014 hardware projects.

This was really painless and took just an hour or two.

## Notes

### Clone `TAIPAN_BAS_PET.txt`

The initial code to be used for MBASIC on CP/M is cloned from the Commodore PET version, `TAIPAN_BAS_PET.txt`.

I think that this might be a scrolling verson, as opposed to the more usual "full screen version", for the simple reason that `LOCATE` and `PRINT AT` are not valid commands in MBASIC on CP/M

Apparently, according to Google AI

> Standard Microsoft BASIC-80 (MBASIC) running under CP/M does not have a built-in LOCATE or PRINT AT command to position the cursor at specific screen coordinates.To print text at a specific row and column, you must send terminal-specific ANSI escape sequences or control codes to the screen using `CHR$(27)`

It goes on to say:

> ### ANSI Terminal Cursor Positioning
> 
> If your CP/M environment uses an ANSI-compatible terminal or emulator (like ANSI.SYS), you can position the cursor using the `ESC [ row ; col H` command:
> 
> #### basic
> 
> ```none
> 10 ' Position cursor at Row 10, Column 20 (1-indexed)
> 20 R$ = CHR$(27) + "[" + STR$(10) + ";" + STR$(20) + "H"
> 30 PRINT R$; "Hello World";
> ```
> 
> Use code with caution. 
> 
>  - `CHR$(27)`: Sends the ASCII Escape character.
>  - Semicolon (`;`): Required in `PRINT` statements to prevent an automatic carriage return/newline from messing up the escape sequence.
>  - `H`: The ANSI command to move the cursor to the given row and column.
> 
>  If you tell me the model of your terminal or emulator (e.g., VT100, Kaypro, Heathkit H19, ADM-3A), I can give you the exact coordinate escape codes it requires.
 
The AI generated BASIC code obviously does not work, in a terminal at least, using `altairz80`.

It is probably safe to say that positioning is out, and a scrolling format, with full page (24 lines) scrolls, being in order.

Interesting but not immediately relevant, although the erroneous notes above about positioning, come from here.

 - [Assembly Language Programming for the RC2014 Zed.](http://w8bh.net/Assembly%20for%20RC2014Z.pdf)

### MBASIC has no locate

Fist task, duplicate all lines with `LOCATE` in them and remove the `LOCATE` statemnt. THe lines wre duplicated so that I had a record of the `LOCATE` coordinates, which could be used with `TAB` if required later.

Previous, initial foray, I had gone up to line 25.

Next, I went up to line 180 + 590. Safe to say that output will be unformatted and a mess. But the splash screen is appearing.

#### Swapping `TABx` for `LOCATEx`

From line 430, `THEN LOCATE 13` can be replaced with `THEN TAB 13`, I started doing this at 430

 - TODO: Need to go back and do previous lines
 - NOTE: Have I confused x and y?

Interestingly, line 641 used to use `TAB`:

```none
641 ifh(x,i)<>0thenprinttab(21);h(x,i);:printtab(30);" ";:print" ";l(x,i):nexti
641 ifh(x,i)<>0thenlocatei+3,21:printh(x,i);:locatei+3,30:print" ";:print" ";l(x,i):nexti
641 ifh(x,i)<>0thenlocatei+3,21:printh(x,i);:print" ";:print" ";l(x,i):nexti
```

likewise line 680

```none
680 locate12, i+3:print v(i);:if l(i,x1) = 0 then invers=1: print tab(21) " ";
680 locatei+3,12:printv(i);:if l(i,x1) = 0 then invers=1:locatei+3,21:print" ";
680 locatei+3,12:printv(i);:if l(i,x1) = 0 then invers=1:tab21:print" ";

```

I do seem to be confused x and y, when using the `TAB` initially.

 - TODO: Need to go back and check and fix previous lines for x and y coords

From 680, you can just copy and paste the first TAB line after the second LOCATE line, like so:

```none
684 ifl(i,x1)<>0 then print tab(31);" ";l(i,x1): next i
684 ifl(i,x1)<>0 then locatei+3,31:print " ";l(i,x1): next i
684 ifl(i,x1)<>0 then print tab(31);" ";l(i,x1): next i
```

Obviously it would be better to delete the second locate line and just fall back on to the original `TAB` line. Can do that later

 - TODO: just remove second and now third lines?


Whole file done, but inconsistantly



### `Type mismatch in 60`

```none
60 get x$: rem poke -16368, 0
```

Remove the `POKE`

```none
60 get x$: rem poke -16368, 0
60 get x$
```

But there is no `GET` for keys, use `INKEY$`

```none
60 get x$: rem poke -16368, 0
60 x$ = INKEY$
```

See [CP/M MBASIC - INKEY function?](https://forum.vcfed.org/index.php?threads/cp-m-mbasic-inkey-function.77371/)


### Syntax error in 130

No immediately obvious error:

```none
130 CLS:PRINT "port ";L$(L);: PRINTM$(M);". ";DA+1;",";Y
```

`CLS` is not a valid MBASIC command:

```none
130 CLS:PRINT "port ";L$(L);: PRINTM$(M);". ";DA+1;",";Y
130 PRINT "port ";L$(L);: PRINTM$(M);". ";DA+1;",";Y
```

But there is still a syntax error.

`printm$(m);` seems to be the issue.

A test code:


```none
10 DIM M$(3): M$(0) = "A": M$(1)="B": M$(2)="C"
20 print M$(2)
```

This works, so the issue is a missing psace between print and variable name.

This works

```none
10 DIM M$(3): M$(0) = "A": M$(1)="B": M$(2)="C"
20 print M$(2)
```


This gives syntax error

```none
10 DIM M$(3): M$(0) = "A": M$(1)="B": M$(2)="C"
20 printM$(2)
```


Very odd behaviour

### Syntax error at 810


```none
810 ifc>dandd>2000andrnd(1)>.7thengosub 1340:tab 12
```

More missing spaces isues?

Fix:

```none
810 if c>d and d>2000 and rnd(1)>.7 then gosub 1340:tab 12
```

Needs a space aftrr the `IF`.

This works:

```none
812 if c>d and d>2000 and rnd(1)>.7then print"iron lotus ruffians, taipan!" ; 
```

This does not (missing space in `ifc`):


```none
812 ifc>d and d>2000 and rnd(1)>.7then print"iron lotus ruffians, taipan!" ; 
```

Likewise, not work (missing space in `thenc`):

```none
813 if c>d and d>2000 and rnd(1)>.7 thenc = int (c / 3) :gosub 760:gosub 130

```

work

```none
813 if c>d and d>2000 and rnd(1)>.7 then c = int (c / 3) :gosub 760:gosub 130
```


### Syntax error at 890

```none
890 locate12:print l$(l);" market forces have": print "driven ";g$(i);
890 tab12:print l$(l);" market forces have": print "driven ";g$(i);
```

It did not like the `TAB`. This works:

```none
890 locate12:print l$(l);" market forces have": print "driven ";g$(i);
890 tab12:print l$(l);" market forces have": print "driven ";g$(i);
890 print l$(l);" market forces have": print "driven ";g$(i);
```



### Subscript out of range in 784

```none
784 sleep(200)
```

MBASIC on CP/M has not SLEEP, WAIT, DELAY, PAUSE - well, there is a WAIT but not sleep related.

Could use this but CPU clock frequency dependant:

```none
10 PRINT "Waiting..."
20 FOR I = 1 TO 1000: NEXT I
30 PRINT "Done!"
```



No fix just REM

```none
784 sleep(200)
784 REM
```


### There are two sets of 810-813

Probably for a previous logic ladder: reduce line length, and inverse logic. I had put the equation into the `dice` variable to reduce the length of `IF` conditionals. It also explains the lack of spaces.

Which set of code to use? The original, long conditionals, is probably better, although the `dice` version is easier on the eye.

### There are two sets of 820-823

Probably for a previous logic ladder: reduce line length, and inverse logic. I had put the equation into the `dice` variable to reduce the length of `IF` conditionals.  It also explains the lack of spaces.

Which set of code to use? The original, long conditionals, is probably better, although the `dice` version is easier on the eye.


### The correct way to use `TAB()`

```none
223 print gp(i);:locate13 + i / 2,21:print g$(i + 1);
223 print gp(i);:TAB 21:print g$(i + 1);
223 print gp(i);:print tab(21) g$(i + 1);
```

Note: The second line prints "21", need to use `()` for arguments to `TAB`
Note: `TAB(5)` as a stndalone command does not work. It must be used with `PRINT`, as in `PRINT TAB(10)`

Only the "Y" co-ord in a `LOCATE` statement, i.e. the second argument of `LOCATE`, can be used with `TAB`. Any `LOCATE` statement with only one argument, can be discarded, as there is no `VTAB` command in MBASIC.

TODO: Go through all of the `locate` lines again, and use the `y` coord, in the `PRINT TAB(y)`

Note that the "evolution" of lines 221-224 is correct now, use that as a pattern:

```none
221 print tab(8) " ";l$(l);" market prices ":nrmal=1:print a$
222 for i = 0 to 4 step 2: locate 13 + i / 2,1:print g$(i);
222 for i = 0 to 4 step 2: print g$(i);
222 for i = 0 to 4 step 2: print tab(1) g$(i);
223 locate13 + i / 2,10:print gp(i);:locate13 + i / 2,21:print g$(i + 1);
223 print gp(i);:locate13 + i / 2,21:print g$(i + 1);
223 print gp(i);:TAB 21:print g$(i + 1);
223 print gp(i);:print tab(21) g$(i + 1);
223 print tab(10) gp(i);:print tab(21) g$(i + 1);
224 locate13 + i / 2,30:print gp(i + 1) :next i
224 print gp(i + 1) :next i
224 print tab(30) gp(i + 1) :next i
```


### Other changes


#### Semicolons and empty `PRINT` statements

I found, as the `LOCATE` and `AT` is nt available, that I had to add semi colons to print tatements to keep things on one line. However this also then required empty `PRINT` statements to be added in order to correctly format the proceding section on to a new line.

#### NEXT without FOR in 736

This seems to be a mess:


```none
730 if sh < 0 then locate13:print "your ship is overloaded, taipan         ";
730 if sh < 0 then tab13:print "your ship is overloaded, taipan         ";
731 if sh < 0 then print a$:gosub 760:goto 360
732 if sh >= 0 then cls: print tab( 11);:invers=1:print "embarking":nrmal=1
732 if sh >= 0 then print tab( 11);:invers=1:print "embarking":nrmal=1
733 if sh >= 0 then print tab( 9);"from " ;l$ (l ) : invers=1:print a$:nrmal=1
734 if sh >= 0 then for i = 0 to 9:if l = i then next i:goto 740
736 if l <> i then print tab( 10);i;" ";l$(i): next i
```


The `IF SH >= 0` is redundant

```none
730 if sh < 0 then locate13:print "your ship is overloaded, taipan         ";
730 if sh < 0 then tab13:print "your ship is overloaded, taipan         ";
731 if sh < 0 then print a$:gosub 760:goto 360
732 if sh >= 0 then cls: print tab( 11);:invers=1:print "embarking":nrmal=1
732 print tab( 11);:invers=1:print "embarking":nrmal=1
733 print tab( 9);"from " ;l$ (l ) : invers=1:print a$:nrmal=1
734 for i = 0 to 9:if l = i then next i:goto 740
736 if l <> i then print tab( 10);i;" ";l$(i): next i
```

But this still fails.

Better would be to rewrite 734-736 as

```none
734 for i = 0 to 9
735 if l <> i then print tab( 10);i;" ";l$(i)
736 next i
```


#### Space between `PRINT` and `A$`

For example

```none
350 if num>sg(x1)then printa$;
350 if num>sg(x1)then print a$;
```

#### Space between `AND`/`OR` and `X$`

The last line runs, the others do not:

```none
375 if (x$<>"g"orl<>0)andx$<>"t"andx$<>"r"andx$<>"l"andx$<>"e" then gosub 770
375 if (x$<>"g" or l<>0)and x$<>"t"and x$<>"r"and x$<>"l"and x$<>"e" then gosub 770
376 if (x$<>"g"orl<>0)andx$<>"t"andx$<>"r"andx$<>"l"andx$<>"e" then goto 370
376 if (x$<>"g"or l<>0)and x$<>"t"and x$<>"r"and x$<>"l"and x$<>"e" then goto 370
```


#### Don't skimp on spaces!


Look at the progression of line 720:


The last line runs, the others do not:

```none
720 fori=0to9step2: locate (i / 2) + 4:print a$: locate (i / 2) + 4: 
720 fori=0to9step2: tab (i / 2) + 4:print a$: tab (i / 2) + 4: 
720 fori=0to9step2: print a$:  
720 for i=0to9step2: print a$:  
720 for i=0 to 9 step 2: print a$:  
```

#### Arrays start at 0 index

The last line runs, the others do not:

```none
721 print i;" ";l$(i);: print tab(20) i + 1;" ";l$(i+1):next i:print
721 print i;" ";l$(i);: print tab(20) i + 1;" ";l$(i):next i:print
```

#### No space required before `THEN`

The last line runs, the others do not:

```none
642 if h(x,i)<>0thenprint tab(21) h(x,i);
642 if h(x,i)<>0then print tab(21) h(x,i);
```

#### Space between `NEXT` and `I`

The last line runs, the others do not:

```none
643 if h(x,i)<>0then print tab(30) " ";:print" ";l(x,i):nexti
643 if h(x,i)<>0then print tab(30) " ";:print" ";l(x,i):next i
```


#### Remove all `TAB` and `LOCATE`

Only use `TAB` in conjunction with `PRINT`

#### Rewriting line 682 and 684

Much more logical to move the `NEXT I` to the end, remove from conditional:

```none
682 if l(i,x1) = 0 then print tab(32);"?":next i:goto 690
682 if l(i,x1) = 0 then print tab(32);"?"

684 if l(i,x1)<>0 then print tab(31);" ";l(i,x1): next i
684 if l(i,x1)<>0 then print tab(31);" ";l(i,x1)
685 next i
```
