# Stellar Drift

Top-down space combat. Ét HTML-dokument, intet build, intet framework. Åbnes direkte i browser.

## Filosofi

### En fil, procedurelt gameplay — portrætter og lyd som undtagelse
Hele spillets *gameplay* bor i én `.html`-fil — inline CSS, inline JS, Canvas 2D. Ingen `npm`, ingen bundler. Stjerner, skibe, projektiler, eksplosioner — alt tegnes procedurelt med `ctx`. Hvis noget *gameplay-element* kræver en ekstern fil, har vi taget et forkert valg.

Undtagelserne ligger ved siden af `.html`-filen:
- **Lyd:** `title-music.mp4`, `stellar-drift-beginning.mp3` (kan ikke laves procedurelt med rimelig indsats).
- **Brian-portrætter:** `Brian<Mood>.jpeg` × 10 (pixel art af pilotens humør, top-højre i HUD'en). Humør-stige fra værst til bedst: `Apathy → Grief → Afraid → Angry → Antagonistic → Content → StrongInterest → Cheerful → Enthusiastic → Serenity`. Default: `Content`. Styres via `setBrianMood(name)` i JS.

Reglen er stadig: nye ting tegnes procedurelt med mindre der er en god grund. Stemningsbåren karakterkunst og musik er gode grunde. Et nyt fjende-skib er det ikke.

### Faseopdelt udvikling via git-branches
Spillet bygges i faser. `main` indeholder altid den seneste *færdige* fase. Nyt arbejde foregår på en branch — typisk `phase-2`, `phase-3` osv. Når fasen er færdig, merges den ind i `main` og tagges (`phase-2`, `phase-3`, ...). Phase 1 = Core (skib, fjende, kanon, baggrund) er tagged som `phase-1` i historikken. Senere faser tilføjer mekanikker uden at rive det færdige fundament ned.

### Kapitalskib-feel, ikke arcade
Tingene er *langsomme med vilje*. Accelerationen er 60 px/s², topfart 220, kanonen genlader på ~900 ms, projektiler flyver i 320 px/s så man kan *se* dem rejse. Spilleren skal mærke at de styrer noget tungt. Når noget føles for hurtigt, sænk det — referencen er flåde, ikke jagerfly. Kommentarer som `// was 1100, you can see them travel` er bevidste — de fortæller hvorfor en værdi blev valgt.

### Tre uafhængige rotationssystemer
- **Skroget** står fast (peger altid op på skærmen). Det roterer aldrig.
- **Thruster-disken** roterer langsomt med `←`/`→` og skubber skibet modsat sin egen orientering.
- **Tårnet** roterer hurtigt mod musen (~18 rad/s, næsten øjeblikkeligt).

Dette er spillets identitet. Tre koblede men separate kontroller giver dybden. Lad være med at koble dem sammen igen.

### Realistisk fysik med en blød kappe
Ingen friktion — momentum er ægte. Eneste tilgivelse er en blød `MAX_SPEED`-cap og verdens torus-wrap (Asteroids-stil, men i verdens skala — se næste afsnit). Glid og inerti er feature, ikke bug.

### Kamera med lookahead, torus-verden (phase 3)
Verden er `WORLD_SCREENS` (3×3) skærme stor og wrapper som en torus — men *relativt til spilleren*: spilleren flyver frit i ubegrænsede koordinater, og alle andre entiteter normaliseres hver frame ind i vinduet ±½ verden omkring skibet (`wrapRelative`). Det gør at al delta-matematik (kollisioner, sigte, magnet) virker uden søm-specialtilfælde. Kameraet følger skibet med velocity-lookahead (`CAM_LOOKAHEAD`, clampet så skibet aldrig ryger ud til skærmkanten) og eksponentiel udglatning. Stjernelagene + Milky Way tegnes med parallax-offset og tiler uendeligt — det er kameraets bevægelse gennem parallaxen der sælger følelsen af rum. HUD'en er DOM og upåvirket af kameraet.

### Visuel rigdom gennem lag, ikke sprites
Vi tegner detaljer manuelt: gradients til skrog, panellinier, vents, monteringsringe, glødende flammer, additivt blendede stjerner, en baked Milky Way med støvbånd og nebula-glød. `globalCompositeOperation = 'lighter'` er vores ven. Statiske ting (Milky Way) prerenderes til en offscreen canvas — dynamiske ting tegnes per frame.

### Kommenter *hvorfor*, ikke *hvad*
Koden indeholder forklarende kommentarer ved ikke-trivielle valg: hvorfor en konstant blev ændret, hvorfor en rotation peger som den gør, geometriske udregninger der ikke er åbenlyse. Behold den stil. Lad være med at kommentere ting et godt variabelnavn allerede siger.

## Filer

- `stellar_drift.html` — selve spillet. Én fil, hele tiden.
- `CLAUDE.md` — dette dokument.
- `.gitignore` — hvad git skal ignorere.

## Kørsel

Åbn `stellar_drift.html` i en moderne browser. Det er det.

## Git-workflow (kort)

```
git status                       # hvor er jeg, hvad er ændret?
git log --oneline --decorate     # historik, tags og branches
git checkout -b phase-2          # start ny fase fra nuværende branch
git add stellar_drift.html       # markér ændringer til næste commit
git commit -m "..."              # gem snapshot
git checkout main                # tilbage til hovedlinjen
git merge phase-2                # flet færdig fase ind
git tag phase-2                  # markér milestone
```

Tommelfingerregler:
- Commit ofte, små skridt. Commits er gratis.
- `main` skal altid være spilbar. Eksperimenter sker på branches.
- Hver afsluttet fase får et tag (`phase-N`), så vi altid kan hoppe tilbage.
