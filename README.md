# Virus Escape

A 3D desktop browser game (proof of concept). You're a virus inside the body: chew
through tissue to reach a glowing capillary and escape into the bloodstream, while
antibodies hunt you through the tunnels you leave behind.

## How it plays

- Move with the arrow keys or WASD. Holding a key glides from cell to cell, a quick tap
  moves one cell (even if tapped a moment early), and pressing the opposite way turns
  back at once.
- Hold a direction against tissue to chew it. Pink soft tissue goes quickly, dark dense
  tissue takes longer, purple membranes can't be chewed at all.
- Yellow fat pockets are very quick to chew, and while you're inside one the antibodies
  lose your scent and drift about. They can still bump into you, so don't wait next to them.
- Glowing gems sit in little cavities in the tissue and in the vessels:
  - ⚡ Enzyme: chew about three times faster for 6 s.
  - 💨 ATP: move 1.7× faster for 6 s.
  - 👻 Cloak: antibodies can't sense you for 6 s.
  - 🎈 Decoy: drops an orange copy of you; antibodies chase it for 8 s or until they pop it.
- Red blood vessels are already open, so you move fast there, but antibody patrols drift
  along them. Once you break into a vessel, they can reach you.
- Antibodies only move through open space. They follow the shortest open route to you,
  so every tunnel you chew is a road for them too.
- More antibodies come in through your entry wound (the start corner). The first ones arrive
  after half the time a perfect virus would need to escape (worked out for each level from the
  quickest route, chewing included), so harder levels give you longer; the 🚨 timer at the top
  counts down to them. After that, more come every 12 seconds.
- The tissue right round the entry wound is always soft, so the first few bites are easy.
- Reach the glowing ring to win; the next level is a different organ, with more patrols
  and faster antibodies. If one touches you, you're caught: press Space to try again.
- The minimap (bottom right) shows tissue, vessels, your green trail, the antibodies
  (blue) and the exit (blinking).
- R restarts the level, M turns the sound on or off (remembered).

## The organs

Each level is a different part of the body, with its own colours, cell shapes and layout,
in this order (after the heart they repeat, harder each time):

1. 💪 **Muscle**: rounded pink cells with nuclei, straight membranes and blood vessels.
2. 🫁 **Lungs**: soft bubbly cells around big open alveoli. Quick to cross, but the guards
   patrol them.
3. 🍲 **Gut**: a field of tall finger-like villi and glowing pools of stomach acid. Acid
   slows you down, but not the guards.
4. 🧠 **Brain**: wrinkled grey matter in wavy unchewable folds, with nerve sparks racing
   along them.
5. 🦴 **Bone**: a honeycomb of hard, slow bone with soft red marrow in each comb.
6. ❤️ **Heart**: rings of striated heart muscle round a big central blood chamber, with a
   much stronger heartbeat.

Every level is checked when it's made, and if walls cut the exit off, a way through is
opened.

## Running it

Serve the folder and open it in a desktop browser:

```bash
python -m http.server 5225
```

then go to http://localhost:5225/.

## Tuning knobs

All at the top of the script in `index.html`:

| Constant | What it does |
| --- | --- |
| `GRID` | Level size in cells (31 × 31) |
| `VIRUS_SPEED` | Virus speed through open space, cells per second |
| `CHEW_TIME` | Seconds to chew soft and dense tissue |
| `AB_SPEED`, `AB_SPEED_PER_LEVEL` | Antibody speed and how much it grows each level |
| `AB_WANDER_FACTOR` | Speed of antibodies that have no route to you yet |
| `FIRST_WAVE_FRACTION`, `FIRST_WAVE_MIN` | First antibodies arrive after this share of the fastest possible escape time, but never sooner than the minimum |
| `WAVE_EVERY` | Seconds between later arrivals |
| `START_SOFT_RADIUS` | Dense tissue and membranes this close to the entry wound become soft |
| `PATROLS` | Antibodies waiting in the vessels at the start (+1 per level) |
| `MAX_ANTIBODIES` | Cap on antibodies at once |
| `CATCH_DIST` | How close an antibody must get to catch you |
| `VESSELS`, `MEMBRANES`, `DENSE_THRESHOLD` | Level layout: vessel count, membrane walls, share of dense tissue |
| `FAT_BLOBS` | Fat pockets to hide in (+1 every second level) |
| `PICKUPS_IN_TISSUE`, `PICKUPS_IN_VESSELS` | How many power-up gems each level has, and where |
| `ACID_SLOW` | How much the gut's acid pools slow the virus |
| `THEMES` | The organs: colours, cell shape, layout and extras for each |
| `ENZYME_CHEW`, `ATP_SPEED` | Strength of the chewing and speed boosts (durations are in `POWERS`) |
| `TAP_BUFFER` | How long a tapped direction is remembered before the virus reaches a cell centre |
| `SFX_VOL`, `VOICE_VOL`, `MUSIC_VOL`, `HEART_VOL` | Loudness of effects, spoken lines, music and the heartbeat |
| `MUSIC_DUCK` | How far the music dips while someone talks |
| `VOICE_GAP`, `VOICE_GAP_LONG` | Seconds before the same kind of line can be said again (longer for chewing, bumping and power-up remarks) |
| `CHEW_TAUNT` | Chance the virus says something when it finishes a cell |
| `CLIP_TRIM` | Per-clip level fixes, measured so the clips sit at a similar loudness |

## Look

Everything is built in code, with no picture files. The virus is a purple, spiky blob
with devil horns, slit red eyes under angry brows, a toothy grin and clawed arms that
chomp while it chews. The antibodies are white blood cell guards in police caps, walking
on little legs and waving a Y-shaped antibody; a "?" over a guard's head means it has lost
your scent. The tissue cells are glossy with a nucleus each and swell gently with a
heartbeat, red blood cells tumble along the vessels, and bloom makes the gems, exit and
spikes glow.

three.js 0.160.0 and its post-processing addons come from jsDelivr. Add `?debug` to the URL
to get the game state as `window.ve` in the console, for testing.

## Sound

Recorded clips in `assets/`, made with ElevenLabs; each effect falls back to a sound made in
the browser while (or if) its clip can't load, e.g. when the page is opened from disk.

- **Music**: a sneaky title theme, a tense gameplay loop that crossfades into a frantic chase
  loop as hunting guards get close, and short stings when you escape or get caught. The loops
  are made seamless in the browser by blending each track's last 2 seconds into its start.
- **Voices**: the guards shout when they arrive, spot you again, lose you, find a decoy or
  catch you; the virus comments on each organ, sneers on power-ups, mutters while chewing,
  grumbles at membranes, whispers when it hides in fat, and gloats or curses at the end. Each
  moment has 3 to 5 recorded variants, picked at random and never the same one twice in a row.
  One line plays at a time and the music dips under it.
- **Effects**: three chomps picked at random, a swallow, a squishy bump, a power-up chime,
  a police whistle when more guards come in, handcuffs on a catch, a cackle on escape, a
  heartbeat every beat, a decoy pop and a shimmer when you turn invisible.

Voices and sounds: elevenlabs.io · Music: Eleven Music (also shown on the title and end cards).

### Audio credits

All made on **2026-10-19** with the ElevenLabs **Starter** (paid) plan, after re-reading the
Terms of Use and Music Terms (both "last updated 09 October 2026"). Starter-plan audio may be
used commercially, but Eleven Music output may not go in a monetised ("Studio") game, so this
game must stay free of payments and ads. We credit "elevenlabs.io" and "Eleven Music". No real
person's voice was used. The audio is here only as this game's own sounds: it is not licensed
for anyone else to reuse, and it is not covered by any licence that may later be put on this
game's code. Cost: 2,029 credits for the first set; the 45 extra voice lines (made 2026-10-20,
same plan, voices and settings) cost 464.

- **Voices**, `assets/say-*.mp3`, 10 clips, model Eleven Multilingual v2:
  - Guards, stock voice "Harry - Fierce Warrior" (stability 0.4, style 0.3): "Halt! Stop that
    virus!" (`guard-halt`), "There it is! Get it!" (`guard-there`), "Huh... where'd it go?"
    (`guard-where`), "Gotcha! You're under arrest!" (`guard-gotcha`), "Hey! It's a fake!"
    (`guard-fake`).
  - Virus, stock voice "Callum - Husky Trickster" (stability 0.35, style 0.45): "Heh heh heh...
    Time to make some trouble." (`virus-start`), "So long, suckers!" (`virus-escape`),
    "Curses!" (`virus-caught`), "Ooh, delicious power!" (`virus-power`), "Shh... nobody here
    but us fat cells." (`virus-hide`).
  - Extra variants, numbered `-2` to `-5` after the first:
    - Guards: "Freeze, germ!", "All units! Intruder at the entry wound!", "You there! Stop
      right there!", "Backup's here! Spread out and find it!" (`guard-halt-2..5`); "Found you!",
      "Over here, boys!", "I see it! Move, move, move!", "Got eyes on the suspect!"
      (`guard-there-2..5`); "It's gone! Keep looking!", "Where'd the little rascal go?", "Lost
      it. Check the fat!" (`guard-where-2..4`); "Got you, germ! Case closed.", "Nobody escapes
      the immune system!", "Gotcha! Off to the lymph node with you!" (`guard-gotcha-2..4`); "A
      decoy?! Oh, come on!", "Rats! A dummy!", "That's not the real one!" (`guard-fake-2..4`).
    - Virus: "Mmm... fresh tissue. Let's dig in.", "Nobody will notice a little nibble.", "Out
      of my way, cells!" (`virus-start-2..4`); "Too slow, white blood cells!", "Catch me if you
      can! Oh wait... you can't.", "Off to the bloodstream! Wheee!" (`virus-escape-2..4`);
      "Curses! Foiled again!", "No fair! I was so close!", "Unhand me, you big blob!"
      (`virus-caught-2..4`); "Ooh, that's the stuff!", "Now we're cooking!", "Power up! Heh heh."
      (`virus-power-2..4`); "Shh... I'm just a bit of fat.", "They'll never find me in here.",
      "Hiding... hiding... heh." (`virus-hide-2..4`).
    - Virus, new moments: "Nom nom nom.", "Crunchy!", "Mmm, tasty cells.", "Chomp, chomp,
      chomp." (`virus-chew-1..4`); "Bah, membrane!", "Ow! Too tough.", "Can't chew that. Go
      around." (`virus-bump-1..3`); and one per organ (`virus-organ-*`): "Muscle. Chewy, but
      delicious.", "Ahh, the lungs. Lovely and roomy.", "Ugh, the gut. Mind the acid.", "The
      brain! So many juicy thoughts.", "Bone... this is going to take some chewing.", "The
      heart... let's make it skip a beat."
- **Sound effects**, `assets/sfx-*.mp3`, 12 clips made with the ElevenLabs sound effects model,
  each from a short description: chomp1-3 (wet cartoon bites), gulp, bump (rubbery thud), power
  (sparkly chime), whistle (police whistle), caught (handcuffs and a sad trombone), cackle
  (villain laugh), heartbeat (one muffled lub-dub), pop (squishy balloon pop), cloak (ghostly
  shimmer).
- **Music**, made with Eleven Music (music_v2), instrumental: `music-title.mp3` (30 s,
  mischievous villain theme: pizzicato, bassoon, harpsichord), `music-play.mp3` (60 s, tense
  sneaky loop: staccato strings, pulsing bass, marimba), `music-chase.mp3` (45 s, comedic
  police chase at 140 bpm; the game loops its first 43.2 s), `sting-escape.mp3` (6 s evil
  victory fanfare) and `sting-caught.mp3` (5 s deflated tuba game-over).
