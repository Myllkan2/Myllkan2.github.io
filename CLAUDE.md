# Stellar Drift

Top-down space combat. Ét HTML-dokument, intet build, intet framework. Åbnes direkte i browser.

## Filosofi

### En fil, ingen afhængigheder
Hele spillet bor i én `.html`-fil — inline CSS, inline JS, Canvas 2D. Ingen `npm`, ingen bundler, ingen assets på disk. Stjerner, skibe, projektiler — alt tegnes procedurelt med `ctx`. Hvis noget kræver en ekstern fil, har vi taget et forkert valg.

### Faseopdelt udvikling
Filnavnet siger det selv: `4stellar_drift_phase1.html`. Hver fase er sit eget dokument. Vi bygger oven på den foregående fase ved at kopiere filen, ikke ved at versionsstyre den. Phase 1 = Core (skib, fjende, kanon, baggrund). Senere faser tilføjer mekanikker uden at rive det færdige fundament ned.

### Kapitalskib-feel, ikke arcade
Tingene er *langsomme med vilje*. Accelerationen er 60 px/s², topfart 220, kanonen genlader på ~900 ms, projektiler flyver i 320 px/s så man kan *se* dem rejse. Spilleren skal mærke at de styrer noget tungt. Når noget føles for hurtigt, sænk det — referencen er flåde, ikke jagerfly. Kommentarer som `// was 1100, you can see them travel` er bevidste — de fortæller hvorfor en værdi blev valgt.

### Tre uafhængige rotationssystemer
- **Skroget** står fast (peger altid op på skærmen). Det roterer aldrig.
- **Thruster-disken** roterer langsomt med `←`/`→` og skubber skibet modsat sin egen orientering.
- **Tårnet** roterer hurtigt mod musen (~18 rad/s, næsten øjeblikkeligt).

Dette er spillets identitet. Tre koblede men separate kontroller giver dybden. Lad være med at koble dem sammen igen.

### Realistisk fysik med en blød kappe
Ingen friktion — momentum er ægte. Eneste tilgivelse er en blød `MAX_SPEED`-cap og skærmwrap (Asteroids-stil). Glid og inerti er feature, ikke bug.

### Visuel rigdom gennem lag, ikke sprites
Vi tegner detaljer manuelt: gradients til skrog, panellinier, vents, monteringsringe, glødende flammer, additivt blendede stjerner, en baked Milky Way med støvbånd og nebula-glød. `globalCompositeOperation = 'lighter'` er vores ven. Statiske ting (Milky Way) prerenderes til en offscreen canvas — dynamiske ting tegnes per frame.

### Kommenter *hvorfor*, ikke *hvad*
Koden indeholder forklarende kommentarer ved ikke-trivielle valg: hvorfor en konstant blev ændret, hvorfor en rotation peger som den gør, geometriske udregninger der ikke er åbenlyse. Behold den stil. Lad være med at kommentere ting et godt variabelnavn allerede siger.

## Filer

- `4stellar_drift_phase1.html` — Phase 1: Core. Spilbart fundament.

## Kørsel

Åbn `.html`-filen i en moderne browser. Det er det.
