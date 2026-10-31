## Assorted Slop
Here I'll put the biggest successes of my AI-generated stuff mostly related to J2ME games so that there won't be any other places that will have to suffer from it.
### Docs
#### Manic Miner
Claude yaps about things I asked it in regards to the obscure official J2ME port of Manic Miner. I didn't know there was a cheat code in this version before.
#### Doom RPG
Claude yaps about a version difference in Doom RPG between the first two versions (Samsung SPH-A880 and Motorola V551) and all the later versions starting with the Sanyo MM-5600 version, pertaining to level 6. This is probably of interest to speedrunners.
### Tools
#### Gun Fever
Gun Fever for the J2ME by DeValley has a Japanese version with a large set of sound effects that are never played due to one stubbed function. The provided patch fills this function and expands the sound indexes because they are one sound effect too short, and also it adds a missing line to the manifest file so that the game can start. Sadly on tested phones (Nokia N82 and Sony Ericsson K800i) it's laggy when playing the sounds, maybe that has to do with why they scrapped it.
### Releases Section
#### Mario Forever J2ME Port
This is one of the worst things I've ever done. It started as an experiment as to porting the PC fangame Mario Forever (made using the Multimedia Fusion game engine) to J2ME by porting the original data file to the J2ME version of the Fusion runtime. It went rather out of hand though since the runtime needed adjustment. The game works okay on some phones with 240x320, 320x240 and 640x480 (Nokia E6-00) resolutions but is usually far too slow. There is no music because the original game used tracker format music, and porting it to the MIDI format was out of scope.
