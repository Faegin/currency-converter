# CLAUDE.md

## Hvad det er

En valutaomregner. Én skærm: vælg to valutaer, tast et beløb, se svaret.
Hostes som statisk site på GitHub Pages: https://faegin.github.io/currency-converter/

Designprincippet er hele pointen med projektet: **appen skal blive ved med at være simpel.**
Play Store er fuld af valutaomregnere proppet med grafer, nyheder, konti og reklamer.
Denne skal ikke ende der. Tilføj ikke features uden at de er bedt om eksplicit.

## Teknik

- Ingen build, ingen npm, ingen framework. Ren HTML, CSS og vanilla JS i én fil.
- Seks filer i repo-roden, ingen undermapper. Service workeren finder dem ikke ellers.
- `index.html` indeholder alt: markup, styling og logik.

## Faldgruber (lært på den hårde måde)

**1. Hæv altid cache-versionen i `sw.js` når `index.html` ændres.**
`const CACHE = 'valuta-v1'` skal blive til `v2`, `v3` og så videre.
Glemmer du det, serverer service workeren stædigt den gamle udgave, og du
fejlsøger noget der allerede er rettet.

**2. Frankfurter v2 svarer HTTP 422 på ukendte query-parametre.**
Tilføj ikke en cache-buster som `?_=${Date.now()}` til det API. Det brækker kaldet.
Brug `cache: 'no-store'` i stedet.

**3. Kursdata må ikke caches af service workeren.**
`sw.js` springer bevidst alle cross-origin requests over. Cachede kurser
håndteres i stedet i localStorage, hvor de kan overskrives med friske data.

## Kursdata

EUR bruges som base, og krydskurser beregnes lokalt. Derfor kræver et valutaskift
ikke et netværkskald.

Kilder prøves i rækkefølge, første der svarer vinder:
1. jsDelivr: `cdn.jsdelivr.net/npm/@fawazahmed0/currency-api@latest/v1/currencies/eur.json`
2. Cloudflare-spejl: `latest.currency-api.pages.dev/v1/currencies/eur.json`
3. Frankfurter v1: `api.frankfurter.dev/v1/latest?from=EUR`

Fejler alle tre, falder appen tilbage på `FALLBACK_EUR` i `index.html`
(indbygget tabel per 24. maj 2026). Statuslinjen viser altid hvilken kilde
der leverede kursen, og hvilken dato den er noteret. Fjern ikke den visning,
den er det eneste diagnoseværktøj brugeren har.

## Test

Der er ingen testsuite. Verificér manuelt:
- `python3 -m http.server 8000` i repo-roden, åbn `http://localhost:8000`
- Statuslinjen skal sige "Live kurs" med en dato fra seneste hverdag
- Skift tema, genindlæs, valget skal huskes
- Slå netværket fra, genindlæs, appen skal stadig starte og vise gemte kurser

Service worker og installations-prompt virker kun over HTTPS eller på localhost.
