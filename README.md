# Valutaomregner

To valutaer, et beløb, et svar. Ikke mere end det.

**Live:** https://faegin.github.io/currency-converter/

Statisk side uden build-trin. Kan installeres på hjemmeskærmen og virker offline.

## Filer

| Fil | Funktion |
|---|---|
| `index.html` | Hele appen: markup, styling, logik |
| `manifest.webmanifest` | Gør siden installerbar som app |
| `sw.js` | Service worker, offline-drift |
| `icon-192.png`, `icon-512.png`, `icon-maskable.png` | App-ikoner |

Alle filer skal ligge i repo-roden.

## Kør lokalt

```bash
python3 -m http.server 8000
```

Åbn http://localhost:8000

## Ved ændringer

Hæv `CACHE`-versionen øverst i `sw.js`, ellers får brugerne den gamle udgave.

Se `CLAUDE.md` for flere detaljer og faldgruber.
