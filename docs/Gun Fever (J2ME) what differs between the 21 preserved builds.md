# Gun Fever (J2ME): what differs between the 21 preserved builds

Oct 31, 2026 · @Volt

The 21 JARs of DeValley's *Gun Fever* hold only **six versions** of the game. The other fifteen are the same version recompiled for a different screen or handset API, and the code changes between them are platform glue. Story, rooms, enemies and dialogue are the same in every English build.

## Short version

- **Six versions:** French 01.00.01 (June 2006), English 1.0.4c (January 2007), 1.0.6c (February 2007), an In-Fusio edition (February 2007), 1.0.7m (July 2007) and a Japanese "Lite" port by Worker Bee (December 2010).
- **Two art sets.** Every build uses either the 128-pixel art set (12 px tiles, 128x128 play area) or the large set (24 px tiles, up to 240x240 play area). The music also comes in a small and a large arrangement.
- **Real gameplay changes are tiny.** Between 1.0.4c and 1.0.7m the game data changes in exactly two entries: one object list in one forest scene, and the music assignment of the menu screens. The rest are robustness fixes to the music player and save loading.
- **The two cut-down builds** (Motorola 128x160 1.0.6c and In-Fusio) drop the 16 character portraits and the title illustration and print the speaker's name instead. The Motorola one also has no music at all. The In-Fusio one has a proof-read English script.
- **One balance difference follows screen size, not version:** two of the three crystal-paying minigames (the scenes the Japanese source calls Z\_Cave\_12\_BossGame and Z\_Hill\_05\_BigRockGame) pay 1.5 times as much per point on the small-art builds.
- **The Japanese Lite build is the most useful one for research.** It was compiled without obfuscation, so it carries DeValley's own names for every class, method and resource.

## The 21 builds

Grouped by version, oldest first. "Code" is the version string compiled into the game, which can differ from the manifest's `MIDlet-Version`. "Built" is the date on the JAR entries; redistributors later re-saved some manifests, and those later dates are ignored here. "Screen" is the minimum canvas size the build asks for.

| JAR | Manifest / code version | Built | Screen | Art set | Canvas and drawing | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| Gun\_176x208\_FRA.jar | 01.00.01 / none | 2006-06-27 | 176x208 | large | Nokia FullCanvas, DirectGraphics | French; oldest; single resource pack |
| GunFever\_128x128.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 128x128 | small | Nokia FullCanvas, DirectGraphics | MIDP-1.0 |
| GunFever\_s40v2a.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 128x160 | small | Nokia FullCanvas, DirectGraphics | MIDP-1.0 |
| GunFever\_s40v3.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 208x208 | large | Nokia FullCanvas, DirectGraphics | MIDP-1.0 |
| GunFever\_s40v3a.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 240x320 | large | Nokia FullCanvas, DirectGraphics | differs from s40v3 only in screen size |
| GunFever\_SonyEricsson\_128x160m2\_en\_v1.0.4c.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 128x160 | small | MIDP-2 Canvas, drawRegion | soft keys ignored; dedomil.net manifest |
| GunFever\_SonyEricsson\_176x220m2\_en\_v1.0.4c.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 176x208 | large | Nokia FullCanvas, drawRegion | dedomil.net manifest |
| Gun\_K800.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 240x320 | large | MIDP-2 Canvas, drawRegion | soft keys ignored |
| GunFever\_Samsung\_128x160m2\_E340\_en\_v1.0.4c.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 128x160 | small | MIDP-2 Canvas, drawRegion | dedomil.net manifest |
| GunFever\_Samsung\_240x320m2\_en\_v1.0.4c.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 240x320 | large | MIDP-2 Canvas, drawRegion | dedomil.net manifest |
| GunFever\_Motorola\_240x299m2\_en\_v1.0.4c.jar | 1.0.4 / 1.0.4c | 2007-01-19 | 240x299 | large | MIDP-2 Canvas, drawRegion | Motorola soft keys; dedomil.net manifest |
| GunFever\_s60v1.jar | 1.0.6 / 1.0.6c | 2007-02-26 | 176x208 | large | Nokia FullCanvas, DirectGraphics | single resource pack |
| GunFever\_s60v2.jar | 1.0.6 / 1.0.6c | 2007-02-26 | 176x208 | large | Nokia FullCanvas, DirectGraphics | same data as s60v1; only the paint loop differs |
| GunFever\_Motorola\_128x160m2\_en\_v1.0.6c.jar | 1.0.6 / 1.0.6c | 2007-02-26 | 128x160 | small, cut | MIDP-2 Canvas, drawRegion | no portraits, no title picture, no music; dedomil.net manifest |
| Gun\_Fever.jar | 1.0.4 / none | 2007-02-28 | 176x204 | small, cut | MIDP-2 Canvas, drawRegion | In-Fusio edition for Motorola; edited English |
| GunFever\_CX65\_Retail\_Etty.jar | 1.0.7 / 1.0.7m | 2007-07-20 | 132x176 | small | MIDP-2 Canvas, drawRegion | soft keys ignored |
| Gun\_K300\_Retail\_Etty.jar | 1.0.7 / 1.0.7m | 2007-07-20 | 128x128 | small | Nokia FullCanvas, drawRegion |  |
| Gun\_K500\_Retail\_Etty.jar | 1.0.7 / 1.0.7m | 2007-07-20 | 128x160 | small | Nokia FullCanvas, DirectGraphics | MIDP-1.0 |
| Gun\_K750\_Retail\_Etty.jar | 1.0.7 / 1.0.7m | 2007-07-20 | 176x208 | large | Nokia FullCanvas, drawRegion |  |
| gun\_fever\_s60v2\_s60v3\_176x208.jar | 1.0.7 / 1.0.7m | 2007-07-20 | 176x208 | large | Nokia FullCanvas, DirectGraphics | named "GunFever N70"; single resource pack |
| Gunfever\_will\_Lite.jar | 1.0.3 / none | 2010-12-16 | up to 240x320 | large | MIDP-2 Canvas, CLDC-1.1 | Japanese, Worker Bee / Living Games; time-limited trial |

The file names are mostly the uploaders' labels. The "Retail Etty" manifests were rewritten by a redistributor (`MIDlet-Vendor: DeValley -Etty-` and similar), and the dedomil.net ones carry that site's ad strings. In both cases only the manifest carries a later date; every other entry keeps the original build date.

## How the versions relate

&#91;embedded content: version family · 6 versions, 21 builds\]

The English line runs French 01.00.01, 1.0.4c, 1.0.6c, 1.0.7m. The In-Fusio edition and the Japanese port start from 1.0.4c-era data; the Motorola cut-down starts from 1.0.6c.

## What every build shares

All 21 builds run the same DeValley engine on the same numbered resources. DeValley's own builds were made with J2ME Polish (the line Polish-Version: 1.3\<beta3> survives in every manifest nobody rewrote). They and the In-Fusio build are obfuscated to `MainApp` plus classes `a` to `h`: `g` is the canvas and main loop, `h` the engine, `d` the player, `f` the other actors, `e` their common sprite base. The Japanese build keeps the original names (`GameClass`, `Game`, `CPlayer`, `CSprite`, and methods prefixed `dva`).

**Resources** are numbered from 0 and stored in packs named by one letter. Most builds load each pack from its own file, listed in an index file `/h`. The S60 and French builds put everything in one file, `/d`. The names below come from the Japanese build's `IResource` interface, whose ids run one higher from 111 on, because it adds a Worker Bee logo at the end of the images.

| Pack | Ids | Count | Contents (Japanese source names) |
| --- | --- | --- | --- |
| a | 0-42 | 43 | animation libraries, `L_Barrel` ... `L_VanClaw` |
| g | 43 | 1 | `GameManager`: save layout and new-game values |
| i | 44-110 | 67 | images: `B_Gameset`, 16 dialogue portraits `F_*`, the icon, menu art `M_*` (logo, font, `M_Title_Camy`), sprite sheets `S_*` |
| l | 111-150 | 40 | tile maps `W_00_Interlude` ... `W_Menu_Save` |
| s | 151-246 | 96 | scenes `Z_AncienMount_02` ... `Z_Train_04_Pilot` |
| t | 247-361 | 115 | text: a hidden credit string `A3rdParty`, menu texts `Da_*`, dialogues `Dialog_*` |
| r | 363-449 | 87 | 13 tables (menu screens, shop prices, portrait list ...), 44 sprite-data entries `D_*`, 30 palettes `P_*` |

**Text** entries are Latin-1. An entry starting with byte 0 is plain; any other entry is packed with a small character dictionary and a bit code. Dialogue lines start with `@` and a letter that picks the speaker's portrait.

**Saves** go to the record store `savGF`, record 1. It holds three slots of 128 bytes; the room and dialogue counts in `GameManager` set the bit layout inside a slot.

**Music** is 14 MIDI files, `/m0` to `/m13`. The Japanese source names them: Far West of Hope, Gun Versus, Into the Temple, Ghost Forest, Under Sand, Final Boss, Dust City, Miaou Brando, Underground Battle, Meet Faith, Victory, Ordinatron Breath, Minigame, Death. Only the Japanese build has sound effects.

**Speed:** the main loop waits for 83 ms per frame (about 12 fps) on small-art builds and 62 ms (about 16 fps) on large-art builds. The Japanese build uses a 62 ms timer.

## English versions: 1.0.4c, 1.0.6c, 1.0.7m

The three English versions have byte-identical text (apart from one hidden string), images, animations, tile maps and music. Each step changes one data entry and some code. To keep handset differences out, each step was compared on matching screens: SonyEricsson 176x220 against S60 and K750, s40v2a against K500, 128x128 against K300.

### 1.0.4c to 1.0.6c (February 2007)

- **One scene changes:** `Z_Forest_07_SecretPath` (scene 43). This is the secret forest path (tile map W\_12\_Forest\_SecretPath, Ghost Forest music). Two of its 49 placements are deleted and re-added at the end of their groups: the spawn point of a green Marauder in the top-right corner, now 2 px narrower (which moves the spawn 1 px left), and a breakable stone row four tiles long, same size and place. It looks like a level-editor re-export. The re-add renumbers the scene's enemies and objects, which only changes which save bits they use. Collision, sprite loading (7 of 8 sprite-type slots) and save ranges (6 enemy and 21 object slots, none overflowing) come out the same, so I see no effect on play.
- **Dialogue box and portrait** are placed against the bottom and right edge of the whole screen instead of the play area. This only shows where the screen is bigger than the play area, such as the 128x160 1.0.7m builds (read from the code, not seen running).
- **Music:** a track restart requested by the key handler or by an interruption is now done by the main loop on its next frame instead of on the spot.
- **Saves:** loading creates the `savGF` store if it is missing. 1.0.4c silently skipped the load instead; both end up with empty slots.
- **Memory:** every load is followed by a garbage collection and a 40 ms pause.
- The hidden credit string changes (see "Text oddities").

### 1.0.6c to 1.0.7m (July 2007)

- **Menu music:** in the menu-screen table (`r` entry 8), six menu screens that had no track now name track 0, the title theme. Together with a new "don't restart the track that is already playing" flag, the title music now carries on through the main menu and its sub-screens instead of stopping when you leave the title.
- **Music player rework:** every track start is queued and performed by the game thread, not the key or system thread. Music resumes after an interruption (`showNotify`) and when a one-shot track ends.
- **Small fixes:** a null check in actor code, a tile-buffer routine reworked (same result as far as I can tell).
- The hidden credit string changes again.

Nothing in 1.0.7m changes rooms, enemies, prices or text.

## French 01.00.01 (June 2006)

`Gun_176x208_FRA.jar` is the oldest build, seven months before 1.0.4c, and the only one in French. Its manifest still holds an unexpanded J2ME Polish template: `MIDlet-Jar-URL: ${AppName}_${polish.name}_${polish.locale}_${Version}.jar`.

- **No Options screen.** The main menu has four items (COMMENCER, INSTRUCTIONS, CREDITS, QUITTER) instead of five. One menu text, one menu screen and one row of the main-menu tile map are missing, so every resource after the text pack sits one id lower.
- **Game data matches 1.0.4c** once that one-id shift is taken out, including the 1.0.4c form of the forest scene that 1.0.6c changed.
- **Vibration on every hit.** The vibrate call when Camy takes damage is not tied to any setting, and with no Options screen there is no way to turn it off. The "Activer la vibration?" prompt in the text pack is never shown.
- **Right soft key acts as 5** (fire, talk, select) everywhere.
- **Older code:** none of the 1.0.6c or 1.0.7m music and save changes; credits show no version line.
- **Untranslated leftover:** the title screen's "Press 5 to start!" is in English.
- The French train-computer hint says "oui" for the cats where the English says "no" (see "Text oddities").

## The cut-down builds: Motorola 128x160 1.0.6c and In-Fusio

Two builds trade the dialogue portraits for name labels to save space. Both use the small art set, and both remove the same things:

- the 16 dialogue portraits (`F_*`) and the Camy title illustration (`M_Title_Camy`), 17 images in all;
- the table that maps speaker letters to portraits;
- the title-screen scene `Z_Menu_Title` and one tile map (the one at `W_Intro01`'s position in the Japanese list), so the title screen is just the logo and text.

In their place a new text entry lists 18 speaker names, and the name is drawn in a purple-blue box with a white border above the dialogue box: Miow Brando, Patron, VanClaw, Narration, Prof Goodeye, ???, Soldier, Rody, Ordinatron, DesertMan, DesertGirl, New Upgrade!, Narration, ForestMan, ForestGirl, Sunny01, Hermit, HillGirl. "Sunny01" is the train mechanic, apparently a working name that shipped. Every resource id after the cuts moves down, so saves from these builds do not fit any other build.

### Motorola 128x160, 1.0.6c

- 1.0.6c code and data otherwise, including the changed forest scene.
- **No music at all:** no MIDI files and no player code. With that the JAR is 102,337 bytes, just under 100 KiB.
- Motorola key codes: soft keys -21 and -22, and key -6 read as "down".

### In-Fusio edition (`Gun_Fever.jar`)

Published by In-Fusio and rebuilt with their own tools (Ant 1.6.5, Java 1.5; no J2ME Polish line in the manifest). It uses Motorola key codes and a 176x204 canvas, which fits Motorola's 176x220 phones.

- **Based on 1.0.4c data:** the forest scene has the 1.0.4c form, and the hidden credit string is just "DeValley 2006 (GF)".
- **Bigger view, small art:** a full-screen 176x204 canvas with a 170x198 play area, so it shows more of each room than any other small-art build. The logo and font images are larger than in the 128-pixel builds.
- **Pause:** the left soft key pauses during play, and the game also pauses when the phone hides the canvas (a call, for example).
- **New menu graphics:** four extra PNGs draw button bars on three menu screens, the five-item main menu among them.
- **No vibration:** the vibrate-on-hit call is gone; the Vibration toggle in Options is still there and does nothing.
- **Proof-read English:** dozens of lines are corrected or Americanised ("defence" to "defense", "Spacious this train!" to "This train is pretty spacious!", "My kif, it's mobile games..." to "My hobby is mobile games..."), prompts read "5/OK", and the instructions add "Press left soft key to pause the game." The credits add an In-Fusio publisher line.
- **Two debugging leftovers** print to the console: ` Abdul  ` from actor code and `Exception while drawing` from the renderer.

## Japanese Lite (Worker Bee, December 2010)

`Gunfever_will_Lite.jar` (ガンフィーバー\[Lite版\], 664,551 bytes) is a port by Worker Bee Inc. for the Living Games label: programming by Shiro Takamisawa, project management by Hiroshi Ogawa, followed by the original DeValley credits. It is the same engine and the same game, built from DeValley's source **without obfuscation**, so its classes, methods and constants carry their real names. Its forest scene has the 1.0.4c form.

**What it adds or changes**

- **Sound effects:** 13 WAV files (`/m14` to `/m26`) for cursor, select, cancel, item, damage, four attack types, explosion, purchase and rolling. In this Lite JAR the play function is stubbed out, so they stay silent (the project note on the SFX restore patch covers that).
- **Re-sequenced music:** the 14 tracks are new 4-channel format-1 MIDI files, close to the small-art arrangements.
- **Japanese text,** stored as base64 of UTF-8 inside the usual text entries. Three minigame shops have their "Ready?" line split into its own entry, giving 118 text entries.
- **Menu:** a sixth item, ウェブサイト (website), opens livinggames.jp and quits. Soft-key labels are drawn by the game.
- **Start-up:** a Worker Bee logo, then one of four 240x240 adverts for other Living Games apps, picked at random (`ex0.png` to `ex3.png`).
- **Trial limit:** the game reads `EXPIRATION_YEAR`, `EXPIRATION_MONTH` and `EXPIRATION_DATE` from the JAD or manifest. Past that date it shows a "trial expired, buy the full version" screen and exits on any key. The text says the trial is otherwise complete. This JAR's manifest has none of these attributes, and without `EXPIRATION_YEAR` the start-up code fails; `EXPIRATION_YEAR: OFF` turns the check off.
- **Moved tables:** the menu, shop and portrait tables are compiled into the code, so the `r` pack shrinks to 74 entries (sprite data and palettes).
- **Extra files:** 20 pre-transformed copies of sprite sheets (`/i0` to `/i19`) with an index (`/flip`), and four picture files that no class ever loads. `data00` is a JPEG photo of a sleeping kitten (800x524, saved on a Mac with an Apple RGB colour profile) whose first 1,679 bytes are zeroed; a JPEG start marker put in front of the main image's tables at offset 4,604 recovers it completely. `data01` to `data03` are three identical copies of a 10,124-byte PNG with its first 116 bytes zeroed. Its header and the start of its single compressed stream are gone, so it cannot be decoded. They look like filler.
- The screen is capped at 240x320 and uses the large art set. Vibration uses `Display.vibrate`.

The train-computer hint is rewritten rather than translated (see "Text oddities").

## Screen size and handset differences

Within one version, the only difference that changes the game itself is the art set, and with it one pair of numbers in the minigame payouts.

|  | Small art (128-pixel builds, In-Fusio) | Large art (176 pixels and up, Japanese) |
| --- | --- | --- |
| Tiles and play area | 12 px tiles; 128x128 play area centred on the screen (In-Fusio 170x198) | 24 px tiles; play area up to 240x240, centred |
| Frame time | 83 ms (about 12 fps) | 62 ms (about 16 fps) |
| Music | mostly 4-channel arrangements | 5 to 8 channels; Final Boss, Dust City, Meet Faith and Death run longer (Dust City 48 beats to 120) |
| Payout per point, `Z_Cave_12_BossGame` | 3 crystals | 2 crystals |
| Payout per point, `Z_Hill_05_BigRockGame` | 15 crystals | 10 crystals |

**Minigame payouts.** When a minigame ends, "These are your winnings! N crystals" pays N = score x rate. The rate comes from the shop table (20 per point), but the code overrides it in two scenes, with different numbers per art set. The small-screen builds therefore pay 1.5 times as much in those two minigames. The Japanese source names the variables: `price`, overridden when `Csav_sSlot[27]` (the current scene) is 166 or 201 in its numbering.

**Handset glue**, which changes nothing in play:

- **Canvas:** Nokia `FullCanvas` (the Nokia-named builds, SonyEricsson 176x220, K300, K500, K750, French) or MIDP-2 `Canvas` with full-screen mode (Samsung, Motorola, SE 128x160, K800, CX65, In-Fusio, Japanese).
- **Drawing:** MIDP-2 `drawRegion`, or on MIDP-1 builds a clip rectangle plus Nokia `DirectGraphics` for flipped sprites.
- **Vibration** when Camy is hit, if switched on: Nokia `DeviceControl.startVibra(100, 1000)` or MIDP-2 `Display.vibrate(1000)`. The French build always vibrates; the In-Fusio build never does.
- **Soft keys:** left soft acts as `*` (back, save menu) and right soft as `5` (select). Codes are -6/-7 on most builds and -21/-22 on Motorola builds. SonyEricsson 128x160, K800 and CX65 ignore the soft keys entirely, so use `*` and `5` there.
- **Paint loop:** either the game thread waits for the previous frame to be painted, or it repaints and calls `serviceRepaints`. This is the only difference between `GunFever_s60v1.jar` and `GunFever_s60v2.jar`.
- Builds that differ only in screen size, such as `s40v3` and `s40v3a`, differ in nothing else.

## Text oddities

**The hidden distributor string.** Text entry 0 (named `A3rdParty` in the Japanese source) is the only text that changes between the English versions. It names who each build was made for, followed in most builds by two lines of French filler ("Voila quelques mots pour que la compression dans ce texte soit active": a few words so that compression is active in this text), with a different set of letters capitalised each time.

| Builds | Entry 0 |
| --- | --- |
| French 01.00.01 | DeValley 2006 (GunFever for Mediaplazza) |
| 1.0.4c | DeValley 2006 (GF for Playmobile) |
| 1.0.6c | GunFever for MediaPlazza |
| 1.0.7m | GunFever for Air Gamer |
| In-Fusio, Japanese | DeValley 2006 (GF) |

**A French word in English menus.** The list of title-menu words in every DeValley English build ends with `*: RETOUR`. In-Fusio's edit changes it to `*: BACK`.

**An unused prompt.** Every build carries a "VIBRATION / Activate vibration?" yes-no screen text; the Japanese build even leaves it untranslated. No version's menu-screen table points at it, so it is never shown, and the only start-up question is the music one. Vibration starts switched on and is changed under Options, where it is saved with the game. The French build has no Options screen and always vibrates; In-Fusio keeps the Options toggle but has no vibrate call behind it.

**The train-computer puzzle.** On the train, a rescue PC asks "continue?" four times, with the answers labelled "25.5.19" and "14.15" (Y-E-S and N-O as alphabet positions). The question table, identical in every build, accepts only **No, Yes, No, Yes** (read from the table, not tested in play). The hint text differs:

- English DeValley builds: "Answer "no" as many time as Mary has brothers... Answer "no" as many time as there are cats in this train."
- French: the second line says "oui" (yes). The In-Fusio edit also changes it to "yes", so the English "no" looks like a translation slip.
- Japanese: rewritten as "Remember Mary. Remember the people you met on the train."

**"Sunny01"** appears as the train mechanic's name in the speaker list of the two cut-down builds, where the other characters have proper names.

## Which build to play or study

- **English, large art, newest code:** `Gun_K750_Retail_Etty.jar` (176x208, Nokia UI API) or `gun_fever_s60v2_s60v3_176x208.jar` (S60). These have the 1.0.7m music handling.
- **English at 240x320:** only 1.0.4c exists at that size (`GunFever_s40v3a.jar`, `Gun_K800.jar`, the Samsung and Motorola builds). The game is the same; the play area is 240x240 with bars above and below.
- **English, small art:** `Gun_K300_Retail_Etty.jar`, `Gun_K500_Retail_Etty.jar` or `GunFever_CX65_Retail_Etty.jar` (1.0.7m). Remember the higher minigame payouts.
- **Best English text:** `Gun_Fever.jar` (In-Fusio), at the cost of the portraits and the title picture.
- **French:** `Gun_176x208_FRA.jar`, the only one.
- **Reverse engineering:** start from `Gunfever_will_Lite.jar`. Its names map onto the obfuscated builds almost one to one: resource ids are the same up to 110, one higher from 111 (the tile maps, scenes and first dialogues), and drift further after its extra text entries. Many routines are the same code.

## Method and caveats

- All 21 JARs were unpacked and decompiled with Vineflower 1.12.0. Code was compared with a diff that blanks out obfuscated names, so a renamed field does not count as a change, on pairs of builds for the same screen.
- Resource packs were split with a small Python reader of the `/h` and `/d` formats, then compared entry by entry; the text entries were decoded (plain, dictionary-packed or, in the Japanese build, base64 UTF-8). Resource and variable names come from the Japanese build's unobfuscated classes.
- MIDI files were compared by channel, instrument and note counts, not by ear.
- **Nothing here was checked in an emulator or on a phone.** Behaviour described from code (dialogue placement, music carrying through menus, pause, the puzzle answer, payouts) is read from the decompiled source.
- Why the forest scene was re-exported, and why the Japanese build carries the picture files, is not known.
