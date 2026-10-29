# Doom RPG (JAMDAT, J2ME): level 6 differences between 1.0.18 and 1.0.36

| Build | Handset | JAR | `level06.bsp` |
|---|---|---|---|
| **1.0.18** | Samsung SPH-A880 | PCS561280C1218580.jar | 20 114 B, 22 Jul 2005 |
| **1.0.36** | Sanyo MM-5600 | PCS561280C1145786.jar | 20 123 B, 16 Aug 2005 |

Both builds are MIDP-2.0 / CLDC-1.0. Of the data files, only `level06.bsp` ("Sector 6") and `help.txt` differ. Every class differs too (obfuscated; a different build), but this note covers only the level.

## BSP layout (from `i.a(String)` / `i.b()` in Vineflower 1.12.0 output)
All little-endian. A 29-byte header (16-byte name, three RGB colours, then bytes 25–28). Then a series of `u16 count` + records:

| Section | Record size |
|---|---|
| nodes | 10 |
| lines | 8 |
| sprites | 5 |
| events | 4 (`u32`) |
| commands | 9 (`u8 op, i32 arg, i32 cond`) |
| strings | `u16 len` + bytes |
| block map | 256 B fixed, 2 bits per cell, 32×32 |

**Event word:** bits 0–9 = cell (`y*32+x`), 10–18 = first command, 19–24 = command count, 25–28 = event **state**, 29–31 = flags (bit 0 = ignore player-originated triggers).

**Command `cond`:** a command runs only if
- its state bits (16–24) match the event state (`0x10000 << (state-1)`, or no state bits when state = 0), and
- its trigger bits match the trigger. The triggers seen here are `0x0F` = stepping onto the cell, `0x100` = Use on the facing cell (or remote trigger by op 3), and `0x20000000` = player on the cell facing angle 0 (east).
- `0x200` makes the command one-shot.

Opcodes used below: 3 = trigger the cell (as Use), 11 = set a cell's event state, 19 = increment the state, 26 = dialog string.

## The difference
Header, nodes, lines, sprites, strings and block map are **byte-identical**. The command list has **one extra command (381 → 382)**. Events 36–133 change only because their command start indices shift by +1. The real change is to the two events at the trap corridor on row 9: **(4,9)**, which is the Dr. Guerard "HAHAHA!" / Dr. Jensen "It's a trap! Run for your life!" ambush, and **(5,9)**, the next tile east. Both cells, like the whole corridor, are open floor.

### 1.0.18
```
event 36 (4,9)  cmds 100..123
  [100-119] enter, state 0: ambush cutscene (spawns, dialog, ...)
  [120] enter, state 0:  state(4,9)++            -> 1
  [121] facing east, state 1:  set state(5,9)=1
  [122] facing east, state 1:  trigger (20,3)    ("Take cover!" ... "There are too many!")
  [123] facing east, state 1:  state(4,9)++      -> 2
event 37 (5,9)  cmds 124..126
  [124] USE, state 0:  set state(4,9)=2
  [125] USE, state 0:  trigger (20,3)
  [126] USE, state 0:  state(5,9)++
```
### 1.0.36
```
event 36 (4,9)  cmds 100..124
  [100-119] unchanged
  [120] enter, state 0:  set state(5,9)=1        <- NEW
  [121] enter, state 0:  state(4,9)++            -> 1
  [122] facing east, state 1:  set state(5,9)=2  (was =1)
  [123] facing east, state 1:  trigger (20,3)
  [124] facing east, state 1:  state(4,9)++
event 37 (5,9)  cmds 125..127   cond 0x100 -> 0x10100
  [125] USE, state 1:  set state(4,9)=2
  [126] USE, state 1:  trigger (20,3)
  [127] USE, state 1:  state(5,9)++
```

### Effect
In **1.0.18** the Use script on (5,9) is armed from the start of the level (state 0). If the player uses that tile, for example from (6,9) facing west, before stepping on (4,9), this happens:
- The "Take cover! … There are too many!" sequence at (20,3) fires before the ambush.
- (4,9) jumps to state 2, so the Guerard/Jensen ambush cutscene, its spawns, and the (6,8) state change it makes **never run**.

That is a sequence-break bug.

In **1.0.36**, (5,9) starts disarmed. Only the ambush cutscene arms it (state 1). Whichever route then fires first disarms the other: facing east on (4,9) sets (5,9) to 2, and using (5,9) sets (4,9) to 2. So the (20,3) follow-up can only happen **after** the ambush, and only once.

## Other differences between the builds (not level data)
`help.txt`: the first line (a count) changes from 84 to 79, and the 1.0.18 lines "BACK can also be used to open the menu or go back." are removed in 1.0.36. That fits the Sanyo key layout.

## Method and caveats
I decompiled both JARs with Vineflower 1.12.0. I took the BSP loader, the event dispatcher (`j.b(int,int,int)`, `j.a(int,int,int,int,int)`) and the trigger sources (`s.v()`, `s.F()`, `s.a()`, Use = `j.c(x,y,1280)`) from 1.0.18. Then I parsed both `level06.bsp` files with a Python script and diffed them section by section. The reading of the bug comes from the scripts and block map only. **Not checked in an emulator.** Whether a player normally reaches (5,9) before (4,9) depends on the route into the corridor.
