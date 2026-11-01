# WALL-E (J2ME, 2008): game speed and movement differences between builds

This is a follow-up to `claude/wall-e-versions.md`, which covers data, editions and repacks. This doc compares the **game logic**: how fast the game ticks, how the frame loop is paced, and WALL-E's movement physics, across all 77 files.

The short version:

- **The game runs one logic step per drawn frame, with no catch-up.** Every build aims for **60 ms per step (16.7 steps/s)**, except two Samsung 240×320 builds that aim for **50 ms (20 steps/s)**. In those two, the whole game, including the level clock, runs 20 % faster.
- **Physics constants scale with the art size, and match exactly in tile units.** The exception is WALL-E's walking speed: **six builds make him walk faster**, by **+22 %** or **+56 %**.
- **The camera is twice as fast in 12 files** (camera mode and the level-start pan), and four times as fast for the level-start pan in one more. Five of them are also faster-walking builds.
- **The frame loop is built in six different ways** (timer, sleep or busy-wait; direct draw or `repaint()`). On a slow phone this changes how much the game slows down, even though the target rate is the same.

## How the game is timed

- `AppTHQwale` runs one logic step (`bd()` in `240x320noks60`) per frame:
  - player and objects move by a fixed amount per step;
  - gravity is added per step;
  - the level clock `ij` counts steps.
- Nothing measures real elapsed time, so the game's speed is the frame rate. If a phone can't keep up, the game runs in slow motion.
- Star Wars: Cantina, from the same studio two years later, does it differently. `_tickAndPaint` keeps an accumulator and runs as many 133 ms logic steps as real time requires (up to 4) before each paint. WALL-E's engine has no such accumulator.

### Target interval
The interval is a static field initialised to **60** in every build (`gT` in `240x320noks60`, `a` in most others). The exceptions are **`240x320sam`** and **`Wall-E_240x320_Samsung_tunewap.com`**:

```java
// 240x320sam: GameCanvas, busy-wait, draw included in the 50 ms
while (!h(0)) {
   int t0 = (int) j.a();
   P();                    // logic step
   a.a();                  // draw + flushGraphics
   if (this.aM++ % 10 == 0) a.serviceRepaints();
   do { Thread.yield(); } while ((int) j.a() - t0 < 50);
}
```

At 50 ms, everything is 6/5 as fast in real time: walking, falling, conveyors, lifts and the level clock. The time bonus is computed from steps, `5 × (300 − steps/16)`, so the score for a given run is the same. The run just takes 17 % less real time.

### How the 60 ms is enforced
The logic runs either directly from the loop (GameCanvas: tick, draw, `flushGraphics`) or inside `paint()` after a `repaint()` request (Nokia `FullCanvas` / plain `Canvas`).

| Pacing | Effect on a slow phone | Builds |
|---|---|---|
| **`java.util.Timer`, period 60 ms**, task draws directly | Fixed 60 ms from start to start, so drawing time is absorbed until a frame takes over 60 ms | `128x128sam`, `128x160lg`, `128x160sam`, `128x160se`, `176x220sam`, `240x320mot`, `240x320se` / `W705_walle`, `Wall-E_176x220_Samsung_tunewap`, `thq_f_Samsung_SGHC140`, `wall_e_128x160_f500`, `wall_e_240x320_c702`, `walle_motorola_240x320`; 2011: `168630/37/43/47` |
| **Timer, 60 ms**, task only calls `repaint()` | Logic runs in `paint()`. Slow paints merge requests, so steps are lost and the game slows | `128x128nok`, `128x160nok` / `WALL-E_s40v2`, `128x160sie`, `352x416nok`, `Wall-E_208x208_Nokia_tunewap`, `walle_128x128_s40`; 2011: `168640` |
| **Thread, `sleep(max(10, 60 − t))`**, then `repaint()` | About 60 ms per request; paint runs separately and requests merge as above | `128x128lg`, `130x130sie`, `132x176sie`, `176x208nok`, `240x320noks60`, `320x240nok`, `640x360noks60` / Mastiwap, `WALL-E_s40v2a`, `WALL-E_s40v3`, `WALL_E_240x320_S60V3`, `Wall-E_128x160_Nokia_tunewap`, `Wall-E_176x208_Nokia_tunewap`, `Wall-E_176x220_SonyEricsson_tunewap`, `Wall-E_240x320_Nokia_tunewap`, `Wall-E_320x240_Nokia_tunewap`, `wall_e_240x320_5700`, `walle_siemens_132x176`; 2011: `168632/36/39/49` |
| **Thread, draw, then busy-wait with `Thread.yield()`** until 60 ms after the logic step | The draw time counts toward the 60 ms, so the rate holds; the CPU never idles | `128x128mot`, `128x128se`, `128x160mot`, `176x220lg`, `176x220mot`, `176x220se`, `240x320lg`, `240x320noks40` / `WALL-E_s40v3a`, `240x320sie`, Alcatel repack, `Wall-E_128`, `Wall-E_240x320_SonyEricsson_tunewap` / `Wall_E_SE`, `thq_f_Nokia_5300`, `wall_e_128x128_f300i`, `wall_e_176x220_K550`, `walle_motorola_176x220`, rebenoj; 2011: `168633/34/35/42` |
| **Thread, draw, then `sleep(max(10, 60 − t))`**, where `t` doesn't include the draw | **The period is 60 ms plus the draw time**, so these run slower than the rest on any real phone | `P4F10422840E31FCE` (China Mobile); 2011: `168631/38/41/48` |
| **Busy-wait, 50 ms, draw included** | 20 steps/s | `240x320sam`, `Wall-E_240x320_Samsung_tunewap` |

So on hardware, the two Samsung 240×320 builds are the fastest. The builds that draw and then sleep the full 60 ms are the slowest: with a 20 ms draw they run at 12.5 steps/s instead of 16.7. The 2011 `168631` group sleeps after the draw, but the original it was made from (`176x208nok`) uses `repaint()`.

The 128×128 and 176×220 Samsung builds also run a watchdog thread. Every 200 ms it checks whether more than 3 s passed between checks, which happens when the phone suspends the MIDlet without telling it. If so, it puts the game into its interrupted state (stopping sound and play, from reading `U()`).

## Movement physics

### Units
Positions are fixed point, with one map tile equal to a fixed number of units that depends on the art scale:

| Scale | Tile | Builds |
|---|---|---|
| S | 65 536 (`<< 16`) | 128–132-pixel-wide builds, the S40v2 reduced builds, the Alcatel repack, **and the rebenoj family** (rebenoj, `168633/34/35/42`) |
| M | 98 304 (× 1.5, no shift) | 176-wide and 208×208 builds, `640x360noks60`, `Wall-E_240x320_Nokia_tunewap`, P4F, **and `168641`** |
| L | 131 072 (`<< 17`) | 240×320, 320×240 and `352x416nok` (its XL art uses L units) |

The Art column in the main doc first came from splash image sizes, which was wrong in two cases: `168641` has L-size splash screens but M-scale game code, and the rebenoj family has M-size splash screens but S-scale game code. Both are now corrected there.

### Constants per step
Everything below scales by exactly 0.5 / 0.75 / 1 between S, M and L. In tiles per step, they are the same in every build:

| | S | M | L | Tiles/step | Tiles/s at 60 ms |
|---|---|---|---|---|---|
| Walk speed (also conveyor push and magnetic-lift speed) | 11 796 | 17 694 | 23 592 | 0.18 | **3.0** |
| Horizontal speed in the air | 8 519 | 12 779 | 17 039 | 0.13 | 2.2 |
| Gravity per step | 4 681 | 7 021 | 9 362 | 0.071 | |
| Maximum fall speed | 58 982 | 88 473 | 117 964 | 0.90 | 15 |
| Jump height parameter (normal / High Jump), launch speed `−√(2·h·g·4096)` | 19 / 35 | 28 / 52 | 38 / 70 | | |

The M jump parameters are rounded down from 28.5 / 52.5. M builds therefore launch about 1 % slower and jump about 1.3 % (normal) or 0.7 % (High Jump) lower in tiles than S and L builds. That is a fraction of a pixel and not noticeable.

Larger offsets in the player code scale the same way: probe distances 114 688 / 183 500, sizes 249 037 / 380 108, throw offsets 43 690, and others.

### Faster walking in six builds
WALL-E's maximum walking speed is set in the key handler:

```java
short max = player.W /*in air*/ ? 17039 : 23592;   // 240x320noks60
...
if (abs(player.jZ + dir * max) > max) v = dir * max;
player.jZ = v;
```

In most builds the ground value equals the conveyor push (`ke = ±23592` in the base actor class) and the magnetic lift's travel speed (`jZ = ±23592` in the lift class). Six builds use a larger value **in all of those places**, while the air speed stays normal:

| Build | Scale | Walk constant | vs. normal | Tiles/s at 60 ms |
|---|---|---|---|---|
| `128x160mot` (1.0.45) | S | 18 350 | **+55.6 %** | 4.7 |
| `128x128sam` (1.0.51, reduced) | S | 18 350 | **+55.6 %** | 4.7 |
| `176x220mot` (1.0.51, reduced) | M | 21 626 | **+22.2 %** | 3.7 |
| `walle_motorola_176x220` (1.0.67) | M | 21 626 | +22.2 % | 3.7 |
| `Wall-E_208x208_Nokia_tunewap` (1.0.67) | M | 21 626 | +22.2 % | 3.7 |
| `Wall-E_128x160_Nokia_tunewap` (1.0.70, reduced) | S | 14 417 | +22.2 % | 3.7 |

- The reduced builds have no magnetic-lift class, so there only walking and conveyors are faster.
- Three of the six are Motorola builds, but `128x128mot`, `240x320mot` and `walle_motorola_240x320` are normal.
- The faster speed is not tied to a version: it occurs from 1.0.45 to 1.0.70. That fits a per-handset porting setting, perhaps for phones that ran the game slowly, but nothing in the files says why.
- Nothing else in the movement code changes in these builds: gravity, jump, air speed and offsets are the standard values for their scale.

Walking speed on the screen, all else equal:

| Build | Steps/s | Tiles/s |
|---|---|---|
| Most builds | 16.7 | 3.0 |
| `240x320sam`, `Wall-E_240x320_Samsung_tunewap` | 20 | 3.6 |
| the four +22 % builds | 16.7 | 3.7 |
| `128x160mot`, `128x128sam` | 16.7 | 4.7 |

On a real phone, multiply by how close it gets to the target rate (see the pacing table).

## Camera speed
The camera moves by a fixed distance per step, like everything else. There are two kinds of movement:

- **Camera mode**: you hold a direction to scroll around the level. It moves 1 tile per step while the key is held (about 17 tiles/s).
- **Scripted pans**: at the start of each level the camera flies from WALL-E to the item you chose and back. It moves 0.5 tile per step (about 8 tiles/s). The other pan state (`iN == 2`) moves 1 tile per step.

In `240x320noks60` this is `bh()` (`iJ += dx << 17`) and the pan code that picks `131072` or `65536` per step. Twelve files double these speeds, and one goes further:

| Builds | Camera mode | Level-start pan |
|---|---|---|
| Most builds | 1 tile/step | 0.5 tile/step |
| `128x128sam`, `128x160mot`, `130x130sie`, `132x176sie`, `walle_siemens_132x176`, `176x220mot`, `Wall-E_208x208_Nokia_tunewap`, rebenoj and its 2011 copies `168633/34/35/42` | **2 tiles/step** | **1 tile/step** |
| `Wall-E_128x160_Nokia_tunewap` | **2 tiles/step** | **2 tiles/step** (both pan states use the same value, so this pan is 4× normal) |

Five of the six faster-walking builds also have the faster camera. `walle_motorola_176x220` walks faster but has the normal camera. The three Siemens S-scale builds and the rebenoj family have the faster camera but normal walking. For the rebenoj family, the camera-mode method decompiles differently, so only the doubled pan speed was read directly.

Speeds in the 2011 re-releases follow the build each was made from.

## What else was checked and is the same everywhere
These were compared in every build after scaling by S/M/L:

- **Actor classes** (player, cubes, magnetic lifts, the bouncing-object routine): all constants match apart from the walk speed above. That covers throw speed and offsets, gravity, jump, fall speed, and the bob counter `% 12`.
- **Scoring:** 1500 points per item, and the time bonus `5 × (300 − steps/16)`.

Differences that turned up but don't affect speed:

- **Play area height:**
  - Motorola builds reserve a strip at the bottom: 240×300 (`240x320mot`, `walle_motorola_240x320`), 176×205/204 (`176x220mot`, `walle_motorola_176x220`), 128×149 (`128x160mot`), 128×116 (`128x128mot`).
  - `240x320lg` (touch) uses 240×230.
  - `176x220lg` uses 176×206.
- **Effects:** `128x160nok` / `WALL-E_s40v2` (and its 2011 copy) lacks a small particle effect: three particles spawned in front of WALL-E during one of his animations. It also lacks the framework's string-fit and font debug codes (`*787#`, `*368#`).
- **Buffer sizes:** the size of the resource read buffer (`new byte[N]`) varies from build to build. It is a memory setting with no effect on play.

## Other timing constant that differs
**Minimum splash/loading time.** After the splash screens the game waits until N ms have passed since start (`for (t = N − elapsed; t >= 100; …) sleep(t)`). N is 2 s per splash screen the build shows:
- **8 s:** 52 builds.
- **6 s:** `128x128mot`, `130x130sie`, `132x176sie`, `176x220sam`, `240x320mot`, `240x320sam`, `Wall-E_128`, `Wall-E_176x220_Samsung_tunewap`, `Wall-E_240x320_Nokia_tunewap`, `Wall-E_240x320_Samsung_tunewap`, `walle_motorola_176x220`, `walle_motorola_240x320`, `walle_siemens_132x176`, Alcatel repack.
- **4 s:** `128x128lg`, `128x128sam`, `128x160nok` / `WALL-E_s40v2`, `Wall-E_128x160_Nokia_tunewap`; 2011: `168633/34/40/42`.
- **2 s:** `176x220mot` (one splash screen).

## Method and limits
- I disassembled every JAR's classes with `javap` and decompiled them with Vineflower 1.12.0.
  - I found the loop interval by following the field read next to `Timer.schedule` or the `max(10, interval − elapsed)` call back to its `putstatic`.
  - I classified the loops by their decompiled shape and by whether the canvas method they call draws (`flushGraphics`) or only calls `repaint()`.
  - I took the movement constants from the walk handler (`var3 < 0 ? 8 : 2`), the jump routine (`(h << 1) * g`) and the actor base class. Then I checked every build for the same constants scaled by 0.5 / 0.75 / 1.
- I compared integer constants across all builds of the same scale, so a changed tuning value in the code would show up. Logic that changed without changing a constant (a reordered condition, a fixed bug) would not. The state machines are renumbered from build to build, which makes them hard to diff.
- Not run on any phone or emulator. The real frame rates depend on each phone's drawing speed; the numbers here are the targets the code aims for.
