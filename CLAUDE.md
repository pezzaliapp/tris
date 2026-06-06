# CLAUDE.md — Il Cubo di PezzaliApp (repo: pezzaliapp/tris)

> Fonte di verità del progetto. Leggimi a ogni avvio. Lavora a piccoli passi:
> completa UN obiettivo, testalo, fai un commit, poi fermati (salvo richiesta di continuare).

## Regola NON negoziabile: codice 100% nostro
- Il codice deve essere **interamente originale dell'autore (Alessandro Pezzali)**.
- **VIETATE librerie e codici di terzi**: niente Three.js, niente "the-cube" (Boris Šehovac),
  niente gl-matrix/jQuery/ecc., niente CDN, `import`, `<script src>`, `@import`,
  `fetch()` esterni, font scaricati. Tutto scritto a mano nel singolo file.
- Se per un'attività ti servisse una libreria: **NON usarla**, reimplementa la funzione.
- Non copiare codice da progetti esistenti (nemmeno MIT): si scrive da zero.
- Verifica dopo ogni modifica:
  `grep -nE "cdnjs|unpkg|jsdelivr|three|gl-matrix|https?://[a-z]" index.html`
  → deve restituire solo eventuali data-URL/blob locali, nessuna risorsa esterna.

## Stato attuale (NON ripartire da zero)
`index.html` è già la **v2 completa**, motore WebGL2 proprio, con:
- Cubo 3×3×3: drag-faccia per ruotare strato (animazione+snap), orbita, zoom, pulsanti, tastiera.
- Mischia, Reset, Annulla/Ripeti, timer, contamosse, rilevamento Risolto.
- Personalizza facce: Colore/Numero/Lettera/Testo/Emoji/Immagine (texture generate via canvas).
- Salvataggio localStorage + link `?cfg=`, Esporta/Importa JSON.
- Sfide: Scacchiera, Superflip, Cube-in-cube, 6 punti; modalità Memo.
- Opzioni: suoni (WebAudio nostro), vibrazione, daltonica; Salva PNG con watermark.
Motore verificato: mossa+inversa torna al risolto; il Superflip riporta i pezzi a casa con
soli orientamenti ruotati (proprietà del vero superflip).

## Architettura (da rispettare/estendere, non stravolgere)
- Singolo file `index.html` (HTML+CSS+JS inline), nessun build step, nessuna dipendenza.
- mat4 column-major a mano; rotazioni intere 3x3 per lo stato (no drift); shader WebGL2:
  `PLIT` (corpo scuro, vertex color), `PTEX` (sticker con texture per-faccia), `PPICK` (picking).
- 26 cubie, ognuna {home, pos(int -1..1), orient(int 3x3), geo{bvao, stickers[]}}.
- Texture facce: atlas canvas 3x3 per faccia (`paintFace`), UV per-sticker da `BASIS`/`tileOf`.

## Da rifinire / fare (in ordine)
- [x] **UV facce**: VERIFICATO (2026-06-06) — orientamento corretto su TUTTE e 6 le facce.
      Niente rotazioni/specchiature: per ogni faccia R×V = normale uscente (mano destra),
      e la UV in-tile usa lo stesso BASIS della disposizione (testo dritto, non riflesso).
      Test eseguiti: (1) simulazione numerica di FACES/BASIS/tileOf+UV → ogni faccia mostra
      1 2 3 / 4 5 6 / 7 8 9 vista frontalmente; (2) modalità "Numero" renderizzata in browser
      (screenshot da due angoli opposti): U/F/R e L/D/B con cifre dritte e ordinate uguali.
      "Immagine" usa lo stesso percorso UV → si ricompone correttamente. Nessuna modifica al codice necessaria.
- [x] **Segno del drag**: VERIFICATO (2026-06-06) — nessuna inversione in alcun angolo di vista.
      Analisi: tutto è calcolato in spazio-MONDO con la normale REALE della faccia toccata (il picking
      la codifica in `vFace`, `code=uBase+vFace`), quindi è invariante rispetto all'orbita camera.
      Tangente t = asse in-piano che meglio segue il dito a schermo; asse di rotazione ω = n×t; la
      velocità di superficie v = ω×r = t·(n·r) − n·(t·r) ha componente lungo +t perché n·r = 1+H > 0
      su ogni faccia esterna afferrabile ⇒ la superficie segue sempre il dito. `dir=sign(ω[rAxis])`,
      l'animazione (`mat4.rotAxis`) e il commit intero (`rotInt`) usano la stessa famiglia rotX/Y/Z ⇒
      segno sempre coerente. Logica spostata in `beginLiveTurn` (vecchio `resolveTurn` rimosso).
- [x] **Rotazione proporzionale al drag (inerzia)**: FATTO (2026-06-06). Lo strato segue il dito 1:1
      mentre trascini (stato `live`, conversione esatta px→radianti via `pxPerRad`, prospettiva inclusa).
      Al rilascio (`releaseLiveTurn`): se >45° o flick veloce → snap a ±90° nel verso del moto con durata
      proporzionale alla velocità del dito (veloce=secco, lento=morbido); altrimenti torna a 0 (annulla).
      Lo stato intero si committa SOLO a 90° (in `draw()`), history/contamosse coerenti. Pulsanti
      U/D/L/R/F/B e tastiera restano a scatto (animazione `anim` from→to). Non testato col mouse
      dall'AI: serve verifica interattiva manuale.
- [ ] **PWA**: aggiungere `manifest.webmanifest`, `service-worker.js` (cache offline) e icone
      generate da noi. NB: questo introduce più file oltre a index.html: ammesso, ma niente
      librerie. Bump versione e cache a ogni release.
- [ ] **Modalità 2×2 e 4×4**: parametrizzare N (oggi 3). Generalizzare cubie, layer, scramble,
      numerazione e UV. Selettore dimensione nella UI.
- [ ] **Best time**: salvare il miglior tempo per dimensione in localStorage e mostrarlo.
- [ ] **Animazione scramble** (opzionale): mescola animato invece che istantaneo.

## Regole di lavoro
- Un obiettivo per volta; commit `feat:`/`fix: descrizione` su `main`.
- Aggiorna la checklist qui sopra spuntando ciò che completi.
- Dopo ogni modifica: apri l'app (`python3 -m http.server` nella cartella) e verifica a vista.
- Esegui sempre il grep anti-dipendenze. Non rinominare `index.html`. Non usare "Rubik" nell'app.
- Il push su GitHub lo fa l'umano.
