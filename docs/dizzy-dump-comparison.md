# Dizzy 3 and 5 (ZX Spectrum): comparing the TZX dumps

All addresses are decimal unless they're written as 0x…. Every block in every dump has a valid XOR checksum. I disassembled with SkoolKit and simulated individual routines in its Z80 simulator. I didn't play either game through in an emulator.

Dump names follow the filenames: "a" and "a2" are the alternate dumps `48K-128K a` and `48K-128K a2`.

## Fantasy World Dizzy (Dizzy 3): 3 dumps, 2 builds

| File | TZX | MD5 | What it is |
|---|---|---|---|
| `…48K-128K.tzx` | v1.11 | b2868ac0… | The later, fixed build. Pauses are about 2.06 s. It has an ID 0x2A "stop tape if 48K" block before the 128K music block. This is the dump TASVideos lists as "Good". |
| `…48K-128Ka.tzx` (a) | v1.03 | 7891a099… | The same build. Every data payload is byte-identical to the file above. Only the packaging differs: flat 1000 ms pauses and no 0x2A block. |
| `…48K-128Ka2.tzx` (a2) | v1.10 | 3e0885c3… | A different, earlier build. The file says "Created with Ramsoft MakeTZX", and the pauses are irregular (0.99–5.97 s), as in a sampled tape. |

Tape layout, the same in all three: header "DIZZY 3", BASIC (294 bytes), headerless loader (256 bytes at 23750), screen, main block (40,959 bytes at 24576–65534) and the 128K music block (16,383 bytes, paged into bank 3 at 49152).

**a2 differs from the other two only inside the main block, in 97 bytes:**

1. **47662 and 47696**, the operands of `LD IX,nn`. In a2 the buffer for the particle effect (32 particles × 2 bytes) starts at **23590 (0x5C26)**. In the other two it starts at **23654 (0x5C66)**. The effect is routine 47506, which runs when you rescue Daisy (just after the text "OH MY HERO! I KNEW YOU'D COME TO MY RESCUE").
   - The loader keeps its "128K detected" flag at **23627 (0x5C4B)**. That address is inside a2's buffer: it's byte +1 of particle 18, which is a random coordinate.
   - Every frame, the IM 2 handler (63702) calls 24950. When the flag is non-zero, 24950 pages in bank 3 and calls the music player at 49155. On a 48K machine, 49155 holds game data.
   - **Result: in a2 on a 48K Spectrum, the game crashes as soon as you rescue Daisy.** It survives only if the random value happens to be 0, a 1-in-256 chance. On a 128K machine the music stops only in that same 1-in-256 case. In the simulator, routine 47640 set the flag to 211 in a2 and left it untouched in the other build.
2. **45117–45119**: three dead padding bytes after `JP 63693`. They're `00 00 00` in the later build and `25 A7 FE` in a2.
3. **57083–57182**: a 100-byte runtime scratch buffer used by the sprite routine (filled at 56722 and read through SP at 57020). The two builds contain different leftover memory there.

Items 2 and 3 are uninitialised memory, which means a2 was saved from a separate build or mastering session. Item 1 fixes a real bug, so a2 is almost certainly the earlier release.

## Spellbound Dizzy (Dizzy 5): 2 dumps, 2 builds

| File | TZX | MD5 | What it is |
|---|---|---|---|
| `…128K.tzx` | v1.03 | 60576ec5… | Build A, the earlier one. Pauses are 1000 ms and there's no metadata. TASVideos used this exact file, under a "48-128k" filename, for its Spellbound Dizzy TAS. |
| `…48K-128K.tzx` | v1.12 | eb347bc2… | Build B, the later one. Its archive info says "Perfect TZX sampled from the original cassette by Church of the Latter-Day Speccy", price 3.99. Pauses are about 2.07 s. |

The "128K" label on build A is misleading. Both builds use the same loader in the REM statement, run by `RANDOMIZE USR 23787` (the BASIC shows `USR 0`, but the real number is hidden).

**Identical in both builds:** the header, both 6,912-byte screens, bank 6 (16,384 bytes) and bank 1 (97 bytes).
**Different:** the BASIC/loader (6 bytes: the hidden `CLEAR` value and the length, address and entry point of the main block), the main block (41,343 bytes at 24192 vs 41,263 at 24272), bank 4 (15,977 vs 15,986 bytes) and bank 7 (4 pointer bytes).

At startup both builds relocate 21,501 bytes from the start of the main block down to 23296. After that, both are at the same runtime addresses. I compared the post-relocation images.

### 48K mode vs 128K mode: this is what all three changes hinge on

The loader decides the mode once (result stored at 23986, passed to the game as 255 for 128K or 0 for 48K):
- It tests whether 0x7FFD paging works: it writes to 49152 in bank 0 and bank 4 and compares. If paging works, the game runs in **128K mode** and loads banks 4, 7, 6 and 1.
- If paging doesn't work, or "4" is held as the loader starts (row 0xF7FE, bit 3), it runs in **48K mode** and stops loading after the main block.
- So "which model" really means which mode the game ends up in. 48K mode means a 48K/+ machine, or a 128K-family machine with paging locked (48 BASIC) or with "4" held.

In 128K mode, the startup code (45754, list at 45882 in A / 45962 in B) patches about 20 places in main memory, mostly redirecting routines into bank 4 (`LD E,0x14 / LD HL,addr / JP 38408` calls bank 4). Among them:
- **Both object-dispatch loops** (41779 and 41850) are replaced by bank-4 versions (49476 and 49384).
- The bank-4 loops look up each object type in bank-4 override tables first (49568 "other", 49656 "per-frame"). They only fall back to the main tables (43787, 43863) when the override entry is 0. Types 0x00–0x12 are all overridden, including **type 8 (the leprechaun), type 9 (Theo) and type 0x11 (the Yolkfolk/Theo copies at home)**.
- The room-entry/status routine is also redirected (51638 → bank 4).

**So the main-block handlers for those object types run only in 48K mode.** All three Spellbound code changes are in 48K-mode code paths, or (change 3) only make a difference there. In 128K mode, builds A and B behave identically for all three.

### Real changes from build A to build B

**1. In build A's 48K mode, the leprechaun swallows the action key. This explains the "drop the shamrock, stand between, pick it up and use it straight from the inventory" trick.**

How the action key works (main loop at 51387):
1. 36854 reads the key. A fresh press sets the latch 46246 = 0 ("unconsumed").
2. 41830 runs every object's per-frame handler in object-list order. Any handler can take the press through 36959 (or 36967), which sets 46246 = 255.
3. Only if the press is still unconsumed does `CALL 36967 / CALL Z,40815` open the inventory. 36991 un-consumes a press (46246 ← 0).

The leprechaun is object type 8 (record 43217: x=0x38, y=0x88, room 98, sprite 0x3A, 8×32).

| | First press while touching the leprechaun | Later presses |
|---|---|---|
| Build A, 48K mode (handler 49001: `CALL 36959 / RET NC`) | 36959 has already consumed the press. `RET NC` always returns, because every exit of 36959 clears carry. **Nothing happens and the inventory doesn't open.** | Same, forever. |
| Build B, 48K mode (48997: `RET NZ`, `JP NZ,36991`, `DEC (IX+6)`) | Greeting (messages 49–50). | The press is released, so the inventory opens. |
| Both builds, 128K mode (bank-4 handler 50890) | Greeting. | The press is released if Dizzy is carrying anything (41451); otherwise he greets you again. |

The shamrock→talisman exchange (message 51, "OH! TIS MY SHAMROCK! HERE, TAKE THIS CHARM: …") goes through the item-use table at 41020 (entries `[room, x, y, item sprite] → handler`), scanned by 40822 after you choose an item in the inventory:
- The entry `62 3A 90 15 → 46575` needs room 98, item sprite 0x15 (GOLD SHAMROCK), and Dizzy inside the box from 36910 with BC = 0x0410: **Dizzy x+32 in 53–61, Dizzy y in 145–178**.
- Handler 46575 removes the shamrock, puts the WEIRD TALISMAN (item record 27, sprite 0x1E) into room 98 (42694 ← 98) and shows message 51.
- The leprechaun's collision box (36891 → 36910 with his 8×32 sprite) is Dizzy x+32 in 51–63, Dizzy y in 137–186. **The use box lies entirely inside it.**
- So in build A's 48K mode, every spot where using the shamrock works also has Dizzy touching the leprechaun, and pressing action there is eaten. The normal "walk up, open the inventory, use" route is impossible.

The workaround works because of the order of the object list:
- Items (type 0x14, group at 42448) come before the leprechaun's group (43210), so an item's handler sees the press first.
- The item handler 50150 does `CALL 36959` (touching the item → take the press) and, if there's a free slot, `JP 40792`. That picks the item up, **opens the inventory immediately** (40816 → 41087), and then runs the same use-item scan with Dizzy's current position.
- So: drop the shamrock just beside the leprechaun, stand so Dizzy touches both the shamrock and the use box ("between" them), press action to pick it up, and select the shamrock in the inventory that pops up. That gives the talisman.

The trick is needed only in **build A in 48K mode**. In 128K mode (either build), or in build B, you can just talk to him once and then use the shamrock normally, which fits "only for some people". The greeting is never shown in build A's 48K mode, but it isn't required. Build B's fix (release the press after the first greeting) is what makes the normal route work.

**2. After Theo's final exchange, build A's 48K mode shows the star count as "I1".**
- Theo is type 9 (record 7 at 43371). In 48K mode his handler 48873 → 48880 needs ≥5 stars (44754 = stars carried: reset at game start, +1 per star at 46821). When all 7 Yolkfolk are home (46226 = 7) it takes the final branch at 48980.
- Build A runs `XOR A / LD (44754),A` before the shared `SUB 5`, so the counter becomes 251. Build B just subtracts 5.
- The 128K-mode version of this logic (bank 4, 51384–51512) never zeroes the counter: `LD A,72 / LD (42748),A`, extended messages (BC = 1389), then `SUB 5`. So **this bug can't happen in 128K mode in either build**; B's fix makes 48K mode match 128K mode.
- How it looks: the star count is printed on each room entry by 51715 → 44059, which outputs only two characters: tens as the character code 48 + tens (0 tens shows as a space), then the units digit. For 251 that's character 48+25 = 73, **'I'**, then '1'. So the display reads **"I1"**, never "251" (confirmed in the simulator: 3 → " 3", 12 → "12", 251 → "I1"). The finale branch jumps to 51561, which switches to the home room (10), so the corrupted count shows immediately. It's cosmetic: Theo's record is hidden afterwards (`SET 7,(IX+3)`) and nothing else checks the count.

**3. In build A's 48K mode, Theo appears at home as a WOODEN RAILING graphic instead of himself.**
- Records 0–7 (43322…, 7 bytes: type, x, y, room, sprite ID, attributes, state) are the 7 Yolkfolk (types 0x0A–0x10, sprites 0x30–0x36) plus Theo (record 7, type 9, sprite **0x37**), each in their underworld room.
- Records 8–15 (type 0x11, all in room 10 = home) are their home copies. They're hidden while state = 0. Sending character k home sets record k+8's state to 1 and switches to room 10. The finale does this for record 15, **Theo's home copy**.
- Record 15's sprite ID is **0x26 in build A** and **0x37 in build B**.
- 48K mode draws type-0x11 objects with 48658 → 41931, using the 48K sprite table at 26742. There, 0x37 is Theo (10×37) and **0x26 is the 16×16 WOODEN RAILING graphic**: item name index = sprite + 2 = 40, "WOODEN RAILING". It's used by five railing item records (42835–42871), all hidden at the start. So in build A's 48K mode, Theo's arrival home after the finale shows a railing instead of Theo.
- 128K mode draws Theo with the larger 128K graphics from the bank-4 sprite table (58336 in A / 58345 in B): head and body 0x24/0x26, plus spell sparkles 0x27 drawn 12 lines lower (51550). The startup patches also adjust y for the taller sprites (43373 ← 0x68, 43429 ← 0x40).
- Build B keeps 0x37 in the data and adds 9 bytes at the start of bank-4's type-0x11 "other" handler (51579: `CP 0x37 / JR NZ / LD A,0x26 / LD (IX+4),A`). That converts the ID to 0x26 only in 128K mode, the first time the object is processed in the current room. So 128K mode looks the same in both builds, and 48K mode gets the correct Theo sprite.

### Everything else is relocation

- The startup code at the top of the relocated region is 80 bytes higher in build B. Build A has 80 zero bytes at 46072–46151 instead.
- Pointer fixups: 187 shifted by +1 (code after change 2 is 1 byte longer), 5 by +9 (bank 4 grew), 5 by +80 (startup code) and 1 by −4. Bank 7 contains only +1 fixups.
- The last bytes before 65535 differ only because data shifted by 1 byte reaches the end of memory there.

### Text format (useful for later work)

Messages are packed as nibbles. The pointer at 46213 gives the base. The 45-character alphabet is at base+0 and the messages start at base+45. Nibbles 0–12 index the alphabet directly. Nibble 13 means 13 + the next nibble, and 14 means 29 + the next nibble. Nibble 15 is a control prefix: 15,15 ends a message, codes 8 and up take no parameters, 3–7 take 1 byte, 1–2 take 2 bytes and 0 takes 5 bytes (code 12 is a line break). The routines are 44513, 44607 and 44681. Banks: main messages at runtime 23296; menu strings, item names (index = item sprite + 2, e.g. 23 GOLD SHAMROCK, 32 WEIRD TALISMAN, 40 WOODEN RAILING, 45 IRON RAILING) and room names (from 47) at 25745; and a second alphabet in bank 4 (128K extended dialogue).

Item records (group at 42448, 9 bytes): type 0x14, x, y, room (254 = carried, 0 = not yet in the world), sprite, attributes, 3 more bytes.

### Sprite format

The 48K table is at 26742 and the 128K table at 58336 (A) / 58345 (B) in bank 4. Each entry points to a 2-byte header [w, h] followed by h rows of w/2 bytes of 1-bit graphics, with no mask.
