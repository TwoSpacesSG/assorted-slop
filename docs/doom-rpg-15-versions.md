# Doom RPG (J2ME): differences between the 15 preserved builds

Doom RPG (id Software / Fountainhead, published by JAMDAT, later EA Mobile, 2005). The 15 JARs in `Doom RPG - 15 versions/` fall into two generations:

- **JAMDAT 1.0.x** (July–August 2005): three builds. All text is inside the levels and the code.
- **1.0.92 and the EA-era ports** (October 2005 – July 2008): twelve builds. Text is in separate `.str` files, so the game can be localized.

All 15 builds play the same game. They have the same maps, entity tables (monsters, items, NPCs), shop prices, cheat codes and rank thresholds. I did not diff the monster AI or combat code across the two generations. The differences are:

- a level-6 script fix;
- one bug fix for New Game;
- text edits and one Italian translation;
- splash screens, icons and MIDI files;
- screen layout and key handling for each handset.

## The builds

Dates are the timestamps of the JAR entries. In every build except the two Sprint ones, the manifest is newer than the rest of the JAR, because it was rewritten when the file was re-packed or shared (see "Repacks").

| File | MIDlet-Version | Build ID / target | Date | Screen handling |
|---|---|---|---|---|
| `PCS561280C1218580.jar` (+ JAD) | 1.0.18 | `1.0.18.en.saa880` (Sprint, Samsung SPH-A880) | 2005-07-22 | canvas size |
| `Doom RPG.jar` | 1.0.20 | `1.0.20.en.mtv551` (Motorola V551) | 2005-07-26 | canvas size, 3D view **88 px** high |
| `PCS561280C1145786.jar` (+ JAD) | 1.0.36 | `1.0.36.en.sy5600` (Sprint, Sanyo MM-5600) | 2005-08-16 | canvas size |
| `DoomRPG_s60v2-N6600.jar` | 1.1.3 | Nokia 6600 | 2005-10-13 | height fixed at **208** |
| `DoomRPG_s40v3.jar` | 1.1.24 | Series 40 3rd ed. | 2005-10-19 | canvas size |
| `DoomRPG_K750.jar` | 1.0.92 | `1.0.92.en.sek500`: a **K500** build despite the file name | 2005-10-27 | canvas size |
| `DoomRPG_s60v2-N6680.jar` | 1.2.50 | Nokia 6680 | 2005-11-17 | height fixed at **210** |
| `DoomRPG_s40v3a-N6280.jar` | 1.3.83 | Nokia 6280 | 2006-01-18 | canvas size |
| `DoomRPG_s40v5.jar` | 1.4.67 | Series 40 5th ed. | 2006-03-21 | canvas size |
| `DoomRPG_s40v3a-N6233.jar` | 1.8.94 | Nokia 6233 | 2006-08-07 | canvas size |
| `DoomRPG_s40v2a-N6151.jar` | 1.9.98 | Nokia 6151 | 2006-08-31 | canvas size |
| `DoomRPG_s40v2a-N5200.jar` | 1.11.7 | Nokia 5200 | 2006-10-03 | canvas size |
| `Doom RPG v1.06.89-mobibuzz.net.jar` | 1.6.89 | 240×320 | 2006-10-24 | fixed **240×320** |
| `EA.Mobile.Doom.RPG.v1.8.84.S60v3.N95…BiNPDA.jar` | 1.8.84 | 240×320, **Italian** | 2006-10-24 | fixed **240×320** |
| `DoomRPG_s60v2-N70.jar` | 1.11.85 | Nokia N70 | 2008-07-02 | height fixed at **210** |

Version numbers after 1.0.x are not in date order (1.6.89 and 1.8.84 are dated after 1.11.7). They look like per-port build counters, not releases.

All builds are CLDC-1.0 and use the MIDP-2.0 `GameCanvas`, including the two whose manifests claim MIDP-1.0. The Sprint builds play sound through `com.sprintpcs.media`. The others use MMAPI.

## Two generations

| | JAMDAT 1.0.18 / 1.0.20 / 1.0.36 | 1.0.92 and EA-era builds |
|---|---|---|
| Level files | 16-byte map name and a strings section inside each `.bsp` | no name or strings in the `.bsp`; a matching `<level>.str` holds them, with the map name as string 0 |
| UI text | hard-coded in classes; `help.txt` for Help | `base.str`; Help/About is split into strings 265–269 |
| `entities.db` | 115 × 24-byte records, names inline (2,762 B) | 115 × 8-byte records (922 B) + `entities.str` |
| Font `a.png` | 144×72, ASCII only | 144×108, adds 48 accented Latin-1 glyphs; `a.map` maps characters to glyphs |
| Locale lookup | none | `.str` and `a.map` are looked up in `/<locale>/`, then `/en/`, then `/` (`microedition.locale`). **N6680 1.2.50 is the exception:** it only reads `/`. |

The 8-byte entity records and all entity names are byte-for-byte the same as 1.0.36. Monster, item and NPC data did not change between generations.

## Level data

I resolved every script command's string references to the text itself and compared the levels that way.

- **1.0.18 and 1.0.20 have identical levels.** Both have the level-6 sequence-break bug: using the tile east of the Guerard/Jensen ambush first skips the ambush. Details are in `claude/doom-rpg-level06-1.0.18-vs-1.0.36.md`.
- **1.0.36 fixes level 6.** It adds one command and arms (5,9) only after the ambush.
- **All twelve 1.0.92+ builds have identical `.bsp` files, and they contain the 1.0.36 levels.** Nodes, lines, sprites, events, scripts and block maps match, including the level-6 fix. The only content differences are:
  - Sector 3: three unused strings removed. They were "Secure closet door 1/2 unlocked." and "North entrance unlocked." No script referenced them.
  - Sector 6: one unused string removed, "Power rerouted. Geothermic reactor online."
  - Sector 4: the five `[E-BOOK]` messages end in `|...` instead of `| ...`.
- The English `.str` level files are identical in all eleven English builds.

## Text and languages

### `base.str` variants (English builds)
| Variant | Builds | Change |
|---|---|---|
| original | K500 1.0.92, s40v3 1.1.24, N6680 1.2.50 | © 2005 legal text, `help@jamdat.com` |
| N6600 | N6600 1.1.3 | 7 extra strings: "Press any key / to continue", "Press FIRE / to continue", and the soft-key captions Done, Enter, Next, Resume, Select. Credits and Help say FIRE instead of OK. The Help key list reads "0 Menu Open" (no "/Back"). |
| 2005-2006 | N6280 1.3.83 | Id years become 2005-2006 / 1993-2006 |
| 2006 + JAMDAT © | s40v5 1.4.67 | as above, plus "(c) 2006 JAMDAT Mobile Inc." and an "all other trademarks" clause |
| 2006 + EA support | N6233 1.8.94, N6151 1.9.98, N5200 1.11.7, 1.6.89 | as above, and the support address becomes `help@eamobile.com` |
| EA 2008 | N70 1.11.85 | 2005-2008 years; "Published by EA Mobile"; Electronic Arts / EA SPORTS trademark text |

### The JAMDAT builds' `help.txt`
- 1.0.18: "Press OK (or 5)…". It also says BACK opens the menu or goes back; this fits the Sprint key table below.
- 1.0.36: the BACK lines are removed.
- 1.0.20 (Motorola): the GENERAL section is longer. It describes the d-pad centre button and says the menu key (or 0) opens the pause menu.

In 1.0.20 the ending URLs are split across lines as `www.doomrpg.com/` + `sarge`. The Sprint builds show `doomrpg.com/sarge`.

### Italian build (`…S60v3.N95…BiNPDA`, 1.8.84)
- **Every `.str` file is translated**, including the level dialogue, the entity names and the debug menu. Some examples:
  - Junction → "Raccordo", Sector → "Settore";
  - XP → "ESP";
  - Impling / Imp / Imp Lord → "Demone / Diavolo / Signore dei demoni";
  - Pinky and Cacodemon are left in English.
- `e.png`, the intro "Final Approach" picture, is localized: "AVVICINAMENTO FINALE", "MARTE", "PILOTA AUTOMATICO".
- `g.png`, the legal / JAMDAT / id / Fountainhead splash, is in Italian.
- **Bug:** base string 252 is **empty**. The ending screen shows it for a score of 70–79 (the second-best rank, "Veterano"). Players with that rank see the rank line and then no text. The other three ending texts are translated.
- There is no Vibrate option and no vibration call. Its English twin 1.6.89 has both.

### Spanish leftovers
- **N6600 1.1.3** has `es/e.png` ("ACCESO FINAL … PILOTO AUTOMATICO", "FOBOS") and `es/g.png`, a Spanish legal splash. Images are always loaded from `/`, never through the locale lookup, and there are no `es/*.str` files. Both files are dead data.
- **N6151 1.9.98 and N5200 1.11.7** check whether a text box equals the Spanish door-code prompt "Introducir código|[busca a alguien|que esté cerca y|sepa el código]:" and add a line break if it does. That is a layout fix for a Spanish build, compiled into these English builds.

### Other text-related changes
- **Latin-1 decoding:** s40v5 1.4.67, N6233, N6151, N5200, 1.6.89 and the Italian 1.8.84 read `.str` bytes one by one as characters. The other new-generation builds use `new String(bytes)`, which uses the phone's default encoding. That can garble accented characters, which only matters in non-English builds.
- **Font:** K500 1.0.92 has its own `a.png`. It has the same 144 characters as the font in the other eleven new-generation builds, but some glyphs are drawn slightly differently, mostly the accented ones.

## "More Games" (N6151, N5200, N70)
These three builds have an extra class for an EA "More Games" menu item. Its text exists in English, French, Italian, German and Spanish, but the language index is fixed at 0 (English). The item appears only if the JAD sets `MoreGames_Impl` to one of these values:

- `static`: a text screen pointing to www.eamobile.com;
- `wap`: opens `MoreGames_WAP_URI`, after a "your game will Exit" warning;
- `wapstatic`: `wap` if a URI is set, otherwise `static`.

None of the preserved manifests sets it, so the item is hidden.

## Splash screen, icon, intro picture
| `g.png` (legal / JAMDAT / id / Fountainhead) | Builds |
|---|---|
| ©1994-2005, JAMDAT logo | all JAMDAT 1.0.x, K500 1.0.92, N6600, s40v3, N6680, N6280 |
| ©1994-2006, JAMDAT logo | s40v5, N6233, N6151, N5200, 1.6.89 |
| ©1994-2008, **EA logo** ("EA™ is an Electronic Arts™ Brand.") | N70 |
| Italian, JAMDAT logo | Italian 1.8.84 |
| Spanish, JAMDAT logo (unused) | N6600 `es/g.png` |

| Icon `doomrpg.png` | Builds |
|---|---|
| 15×15 | JAMDAT 1.0.x, K500, s40v3, N6280 |
| 29×29 | N5200, N6151, s40v5, N6680, N70 |
| 29×29, different blocky art | N6600 |
| 48×48 | N6233 |
| 54×54 | 1.6.89, Italian 1.8.84 |

## Music and sound
All sound is MIDI:

- `t.mid`: title;
- `i.mid`: the intro story (the Mars picture) and the ending;
- `e.mid`: level-up;
- `m.mid`: the menu-open sound;
- `n.mid`, `p.mid`: two short sounds that stay preloaded;
- `0.mid`–`11.mid`: weapon-switch sounds, one per weapon slot.

There are two sets:
- **Original set** (multi-track type 1, 960 ppq; `t.mid` 8,096 B): JAMDAT 1.0.18 / 1.0.36, K500 1.0.92, N6600 1.1.3.
- **Re-exported set** (single-track type 0, 480 ppq; `t.mid` 6,835 B): every other new-generation build. The notes, channels and instruments are the same. Trailing silence was trimmed, so `t.mid` is 17.2 s instead of 20.2 s and `e.mid` is 12.2 s instead of 17.7 s. Within this set:
  - `p.mid` has channel volume 64 in s40v3, N6280, N6151, N5200 and N70, and 127 in N6680, s40v5, N6233, 1.6.89 and Italian 1.8.84.
  - In **N6151 and N5200**, `1.mid` (the fire extinguisher's switch sound) has lost its GM "Seashore" hiss. Only the two drum hits remain.
- **Motorola 1.0.20** ships only `t.mid` and `p.mid`. Its JAD sets `DoomRPG-S-Limited: 1`. That mutes one sound channel and lets the music channel play only `t.mid`. The other MIDI files are missing from the JAR; I can't tell whether JAMDAT or the re-packer removed them.
- Volume: s40v3, N6280, N6233, N6151 and N70 set MMAPI volume 99, N5200 100, s40v5 59, 1.6.89 and Italian 1.8.84 50. The others never set a volume.

## Screen layout
The 3D view width is `DoomRPG-ViewSize` (minimum 128), or the canvas width if that key is not set. The height is calculated in one of three ways:

| Formula | Builds |
|---|---|
| width × 176/256, rounded up to even (176 px wide → 122) | JAMDAT 1.0.18 / 1.0.36, K500 1.0.92, N6600, N6280 |
| screen height − 62 (fills the screen: 208 → 146, 320 → 258) | s40v3, N6680, s40v5, N6233, N6151, N5200, N70, 1.6.89, Italian 1.8.84 |
| fixed 88 | Motorola 1.0.20 |

N6600 fixes the screen height at 208, N6680 and N70 at 210, and 1.6.89 and Italian 1.8.84 fix the whole screen at 240×320. Those two therefore only fit 240×320 phones, whatever the file name says.

## Keys
Every build uses the same keypad layout (2/8 move, 4/6 turn, 1/3 strafe, 5 use, 7/* weapons, 9 pass, # automap, 0 menu) and the same d-pad mapping. Extra key tables:

- **Sprint 1.0.18 / 1.0.36**: left soft (−6) and CLR (−8) open the menu, and right soft (−7) toggles the automap.
- **Motorola 1.0.20**: Motorola soft keys 21/22/23, compared as absolute values. It uses a different internal action numbering.
- **N6600 1.1.3**: left soft (−6) and right soft / Clear (−7 / −8). This build also has the "Press FIRE" strings.
- The other builds have no soft-key table.

## Gameplay code
- **New Game no longer keeps your consumables.** Small and large medkits, soul spheres, berserkers and dog collars are counted in a 5-byte array. In all three JAMDAT builds, K500 1.0.92 and N6600 1.1.3, New Game does not clear it. Choosing New Game from the main menu after playing, without restarting the app, therefore starts the new game with the previous game's consumables. s40v3 1.1.24 (2005-10-19) and every later build clear the array on New Game.
- The cheats are **the same in all 15 builds.** Type the digits while any menu is open:
  - **3666**: debug menu. It has:
    - Cheats: Noclip, Disable AI, Give all, Give ammo, God mode, Level up, Give map;
    - Change Map: any level, including Des. Junction, Reactor, Credits and the Items test map;
    - Stats;
    - Developer: render variables, Benchmark, Force Error, memory/monster dumps.
  - **4332**: "Weapons & Ammo!". All weapons and 99 of each ammo type.
  - **43629**: "Health & Armor!". Health, max health, armor and max armor all become 99.
- The rank thresholds (80 / 70 / 50 points) and the static tables in the code (the item vendor price lists) are identical in all builds.

## JAD settings
All builds read the same `DoomRPG-*` keys. Motorola 1.0.20 also reads `S-Minimal`.

| Key | Effect |
|---|---|
| `ViewSize` | 3D view width (minimum 128) |
| `Frames` | number of frames a door or moving wall takes (2–64, default 4; 1.0.20 allows 1). The Sprint JADs use 8 (1.0.18) and 2 (1.0.36). The debug menu's `r_frames` changes it in game. |
| `Map` + `SkipIntro` | with `SkipIntro: 1`, New Game skips the intro and loads the `Map` file directly, e.g. `/level05.bsp`. This is a developer shortcut in every build. |
| `S-MaxPrefetched` / `S-MaxRealized` / `S-Limited` / `S-Explosions` | how many MIDI players are kept, and whether to cut music down |
| `SlowBlit` / `SplitBlit` / `SlowDrawRegion` / `SkipShakeX` | drawing workarounds for particular phones |

## Repacks
- `Doom RPG.jar` (1.0.20): the manifest says "Jar/Jad re-config by Davo" and is dated 2005-11-05. It sets `ViewSize 128`, `SlowBlit 1`, `SplitBlit 8`, `S-Limited 1` and `MIDlet-Data-Size`.
- `DoomRPG_K750.jar`: "Doom RPG Shared by Terjef". It is the K500 build.
- 1.6.89 ("JAMDAT Mobile Inc./dfs") and 1.8.84 ("JAMDAT Mobile Inc./BiNPDA"): warez repacks. Their manifests claim MIDP-1.0.
- The nine "Electronic Arts" builds all have a `Created-By: Metaflow` manifest dated 2009-07-10. In-game, most still say JAMDAT; only N70 shows the EA logo and legal text.

## Method
I unzipped each JAR and decompiled it with the project's Vineflower 1.12.0. Then I compared:
- class string constants, and the JAD-key, canvas, key-table, sound, new-game, cheat and ending code;
- every data file by hash.

For the data files I wrote Python parsers:
- `.str`: u16 count, then u16 length + Latin-1 bytes for each string;
- both BSP layouts;
- `entities.db`;
- MIDI files, with `mido`.

Level scripts were compared after resolving each command's string references to text. I located the string fields by opcode: 4, 8, 24, 26 and 40 use the whole argument; 2, 27 and 39 the low byte; 10 two bytes; 23 the high 16 bits. Images were compared pixel by pixel. **Nothing was checked in an emulator or on a phone.** The New Game consumables bug and the empty Italian ending come from reading the code and data.
