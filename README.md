# Il Cubo di PezzaliApp — v2 (tutto nostro, zero librerie)

Cubo di Rubik **3×3×3** in **WebGL2 puro**, singolo file `index.html`, **nessuna libreria**
(niente Three.js, niente motori di terzi). Da pubblicare su https://github.com/pezzaliapp/tris.

## Perché "tutto nostro"
Il progetto di riferimento *ILCUBO* è ottimo ma poggia su due componenti di terzi (MIT):
il motore 3D **"the-cube"** (Boris Šehovac) e **Three.js**. Qui quelle parti sono
**riscritte da zero**: matematica delle matrici, shader WebGL2, geometria, picking,
rotazione degli strati, e tutta la logica di gioco. Le funzionalità sono le stesse,
il codice è interamente nostro.

## Funzioni
- Cubo 3×3×3 con motore proprio: **trascina una faccia** per ruotare lo strato (animazione + snap),
  drag fuori = orbita, scroll/pinch = zoom; pulsanti **U D L R F B** (+ inverso ’) e tastiera.
- **Mischia**, **Reset**, **Annulla/Ripeti**, **timer**, **contamosse**, rilevamento **Risolto**.
- **Personalizza facce**: per ogni faccia Colore / Numero / Lettera / Testo / Emoji / **Immagine**
  (l'immagine viene divisa in 3×3 e applicata come texture, generata da noi via canvas).
- **Salvataggio** in localStorage + **link condivisibile** `?cfg=…` + **Esporta/Importa JSON**.
- **Sfide**: pattern famosi (Scacchiera, Superflip, Cube-in-cube, 6 punti) e modalità **Memo**.
- **Opzioni**: suoni (WebAudio nostro), vibrazione, modalità **daltonica** (simboli sulle facce).
- **Salva PNG** della vista con watermark "Il Cubo di PezzaliApp · pezzaliapp.com".

## Prova in locale
```bash
python3 -m http.server 8000   # poi http://localhost:8000/
```

## Pubblica sul repo
```bash
git clone https://github.com/pezzaliapp/tris.git
cd tris
cp /percorso/index.html .
git add index.html
git commit -m "Cubo di PezzaliApp v2: motore proprio + personalizzazione, sfide, opzioni (no librerie)"
git push origin main
```

## Verifica assenza di librerie esterne (deve dare solo data-URL locali, nessun CDN)
```bash
grep -nE "cdnjs|unpkg|jsdelivr|three|https?://[a-z]" index.html
```

## Note
- Il motore (math/shader/rotazioni) è verificato: mossa+inversa torna al risolto, e il
  Superflip riporta i pezzi a casa con soli orientamenti ruotati (proprietà del vero superflip).
- La resa **visiva** delle texture personalizzate va provata nel browser: l'orientamento del
  testo/immagine su alcune facce è coerente per-faccia ma può richiedere una rifinitura fine.
- Marchio: l'app si chiama *Il Cubo di PezzaliApp*; niente nome "Rubik" né branding ufficiale.
