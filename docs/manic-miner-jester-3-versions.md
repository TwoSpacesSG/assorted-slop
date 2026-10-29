# Manic Miner (Jester Interactive, J2ME): RMS, cheats, endings, and version differences

| Build | JAR | MIDlet-Version | Date in JAR | Screen / tiles | Rooms |
|---|---|---|---|---|---|
| **S40 1.0.6** | Manic_Miner_s40_106.jar | 1.0.6 | 19 Dec 2002 | 128×128, 8 px tiles | 20 |
| **3510i 1.0.7** | Manic_Miner_3510i_107.jar | 1.0.7 | 13 Feb 2003 | 96×65 viewport, 8 px tiles | 20 |
| **S40 1.1** | Manic_Miner_s40_11.jar | 1.1 ("for Nokia Series 40") | 5 Sep 2003 | 16 px tiles, `sprites-0..5.dat` | **5** |

All three builds are MIDP-1.0 and use the Nokia UI API (`DirectGraphics`, `Sound`). The class names are obfuscated, so the names below are from Vineflower 1.12.0 output. Game class: `g` (1.0.6 and 1.0.7) or `f` (1.1). Unlock/permissions class: `b` (1.0.6) or `c` (1.0.7). Menu: `Manic`.

## RMS record stores
All stores use record ID 1 only. The game writes it with `addRecord` if the store is empty and with `setRecord(1, …)` otherwise.

### `prefs`, which every build has
| Build | Bytes | Meaning | Default when missing |
|---|---|---|---|
| 1.0.6 | `[music, sfx, 0, 0]` | each 0 or 1 | all on |
| 1.0.7 | `[music, sfx, vibrate, 0]` | each 0 or 1 | all on |
| 1.1 | `[music, sfx, vibrate, 0]` | each 0 or 1 | all on |

### `saved`: highest level reached (1.0.6 and 1.0.7 only)
- 2 bytes: `[0x00, level]`. `level` is a 0-based room index from 0 to 19.
- Writer: `g.if()`. It runs at game over and when the player reaches the paywall. It only writes if the new level is **greater than** the stored one, and **never while the cheat is active**.
- Reader: the Select Level menu takes `n = rec[1] & 0xFC` and lists "Level 1", plus "Level k+1" for every k < n where `(k & 3) == 3`. So the menu offers **Levels 1, 4, 8, 12, 16**. Item i starts room `i*4 − 1`, which gives rooms 0, 3, 7, 11, 15.
- Starting from Select Level does **not** check permissions. Only advancing to the next room does.

### `permissions`: purchased level unlock (1.0.6 and 1.0.7 only)
```
u8  0x00            version / magic (the record is ignored unless this is 0)
u8  count
count × (u8 key, u8 value)   stored in a Hashtable<Integer,Integer>
```
- If the store is missing, the game creates it as `{0 → 5}`, which is the bytes `00 01 00 05`.
- Access check: `allowed(key=0, room) = room < value[0]`. By default the game therefore allows rooms 0–4.
- "Unlock Levels" does an HTTP GET. 1.0.7 uses `http://www.mobserv.com/java/manicunlock.php?UID=<JAD UID>` and 1.0.6 uses `…/manic_unlock.php?GID=0&UID=<UID>`. The game accepts the body only if the response has `Content-Type: application/vnd.jester.java-permissions-mm`. The body has the same format as the record, and the game merges it in and saves it. The server is long dead.
- **RMS unlock:** write `00 01 00 14` (key 0 → 20) to `permissions`. That allows all 20 rooms.

High scores are **never** saved. The HIGH value only lasts for the current MIDlet session in every build.

## Cheat (all three builds)
Key sequence: **`8 9 7 3 9 7 4 8 3 7`**, which is `{56,57,55,51,57,55,52,56,51,55}`. A wrong key resets the counter. The game shows no on-screen confirmation. The flag stays set until the MIDlet restarts.

While the cheat is active, **`#`** completes the current room and goes to the next one:
| Build | When `#` works |
|---|---|
| 1.0.6 | any game state |
| 1.0.7 | only during play (state 3) |
| 1.1 | while lives ≥ 0 |

Side effects:
- The skip uses the normal "next room" path, so **it cannot get past the 5-level paywall**.
- In 1.0.6 and 1.0.7, `saved` is not updated while the cheat is on.

Other keys:
- `0`: back to the main menu.
- 2/4/6 and 1/3: jump, left, right, and diagonal jumps. 1.0.6 also maps soft/arrow key codes.
- Extra life every 10 000 points in all builds.

## Endings
None of the builds has a real ending sequence. There is no Willy-home or swordfish screen. The outcomes are:

| Event | 1.0.6 / 1.0.7 | 1.1 |
|---|---|---|
| Next room not permitted (default: after room 5, Eugene's Lair) | Saves progress. Shows the **"Well Done!"** form: *"…you've completed five levels and can purchase the next five bonus levels. Just text MANIC to 87021… Max cost £3…"* | After the last room (5 of 5), shows **"Well Done!"**: *"…completed the first five levels… Look out for releases of further level packs in the future."* |
| Final Barrier (room 20) completed | Index wraps from 19 to 0, so play loops to **Central Cavern** with score and lives kept. The loop needs the unlock, because room 0 < 5 always passes. | n/a |
| Lives run out | Game Over (the S40 build shows two extra images), saves progress, back to the menu | Game Over, back to the menu (nothing saved) |

## Version differences
**1.0.6 (S40) → 1.0.7 (3510i)**
- Ported to the 96×65 screen. The room data is the same size, but the graphics indices are remapped (`bitmaps.dat` 36 147 B → 28 146 B).
- **The Final Barrier gained extra scenery.** In 1.0.6 the top rows are empty (`ff 92 00`). In 1.0.7 they are filled with 5 new tile types (0x8C–0x90). This looks like the surface picture from the Spectrum original, but I haven't checked it visually. The room grows from 211 B to 265 B, which accounts for the whole +54 B in `rooms.dat`.
- The menu gained icons and an **About** page with the story text, and a **Vibrate** preference was added. "Online Options → Unlock Levels" became a top-level "Unlock Levels" item.
- `#`-skip is restricted to play state, and the unlock URL changed (see above).

**→ 1.1 (S40, Sep 2003)**: a re-release with a different engine.
- Only the **first 5 rooms** are included. `rooms.dat` uses a new format with 32-bit offsets, and the art is 16 px, split across `sprites-N.dat` with a loading bar.
- **Removed:** unlock and networking, the `permissions` and `saved` stores, and Select Level. The menu is now Play / Preferences / About.
- All UI strings moved into `resource.dat`. About now reads "© Jester Interactive 2002 - 2003" without the story.
- The SMS paywall text was replaced by a "future level packs" message.
- The cheat and `#` skip are still present.

All 20 room names are identical in 1.0.6 and 1.0.7, from Central Cavern to The Final Barrier, and follow the Spectrum order.

## Method
I unzipped each JAR, decompiled it with the project's Vineflower 1.12.0 jar, and read the RMS, key-handling and game-loop code. I wrote small Python parsers for the `resource.dat` and `rooms.dat` index formats and used them to pull out strings and room names and to diff each room. None of this was checked in an emulator.
