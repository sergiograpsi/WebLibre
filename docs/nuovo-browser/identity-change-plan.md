# Piano cambio identità Android (scaffolding — nessuna ship in Fase 1)

Obiettivo: app **indipendente** (nome, icona, `applicationId`, certificato di firma) senza riusare brand/asset WebLibre, Firefox, Tor Project, WireGuard, ecc. oltre a quanto già consentito dal codice AGPL e dalle marchi citati correttamente (es. `Tor™`, `WireGuard®` in stringhe tecniche già presenti).

## Stato attuale (upstream WebLibre)

| Elemento | Valore attuale | File principali |
|---|---|---|
| `applicationId` | `eu.weblibre.gecko` (+ `.alpha` sui flavor alpha) | `apps/weblibre/android/app/build.gradle` |
| `namespace` | `eu.weblibre.gecko` | stesso file |
| Label UI | placeholder `${appName}` → `WebLibre` / `WebLibre Alpha` / Impeller | flavor `productFlavors` |
| Package Kotlin app | `eu.weblibre.gecko` | `apps/weblibre/android/app/src/main/kotlin/eu/weblibre/gecko/` |
| Plugin nativi | package `eu.weblibre.*` (mozilla, tor, singbox, …) | `packages/*/android/...` |
| Icone | `assets/icon/icon.png`, adaptive launcher | `pubspec.yaml` `flutter_launcher_icons`, `flutter_launcher_icons-alpha.yaml` |
| Font brand | `assets/fonts/WebLibre.ttf` family `WebLibre` | `pubspec.yaml` |
| Deep link / intent action | `eu.weblibre.gecko.gatekeeper.*`, account callback | `AndroidManifest.xml` |
| MethodChannel | es. `eu.weblibre.gecko/navigation_bar` | `apps/weblibre/lib/core/providers/navigation_bar.dart` |
| Signing release | env `KEY_PATH`, `KEY_ALIAS`, `KEY_PASSWORD` | `build.gradle` + CI `.github/workflows/build.yml` |
| Store listing | `metadata/en-US/*`, package F-Droid/Play `eu.weblibre.gecko` | `metadata/`, README |

## Decisioni da prendere (Sergio) prima della Fase 2

1. **Nome commerciale** definitivo (non “WebLibre”).
2. **`applicationId`** nuovo in reverse-DNS di proprietà (es. `it.<dominio>.browser`) — **non** riusare `eu.weblibre.*`.
3. **Icona e adaptive icon** originali (PNG/SVG) — non copiare `assets/icon/*` di WebLibre.
4. **Keystore di release** dedicato (Play App Signing o upload key propria); non usare il keystore locale di verifica `fase1-local.jks`.
5. Se/come rinominare i **package Java/Kotlin** dei plugin path (alto costo, merge upstream più difficile) vs tenere package interni e cambiare solo `applicationId` + brand UI.

## Strategia raccomandata (basso impatto sui merge)

**Fase A — brand surface (consigliata prima)**  
- Cambiare solo: `applicationId`, `appName` flavor, icone launcher, stringhe UI/About, `metadata/`, deep-link scheme/host, eventuali `MethodChannel` app-level.  
- Lasciare i package Kotlin `eu.weblibre.*` dei plugin path finché non c’è un motivo Play/legale; sono identificatori di codice, non l’ID installabile.

**Fase B — package nativi (opzionale, costosa)**  
- Rename `eu.weblibre.*` → nuovo namespace in app + plugin.  
- Richiede aggiornamento massivo Manifest, Pigeon, test Kotlin; ogni sync upstream diventa conflittuale.

## Checklist operativa (non eseguita in Fase 1)

- [ ] Creare keystore di produzione e segreti CI sul fork (mai in git).
- [ ] Sostituire asset icona; rigenerare con `flutter_launcher_icons`.
- [ ] Aggiornare `applicationId` / flavor names / `metadata/`.
- [ ] Rimuovere o sostituire font `WebLibre.ttf` e riferimenti “Search with WebLibre…”.
- [ ] Verificare che About/licenze citino AGPL-3.0 e upstream FaFre/WebLibre.
- [ ] Smoke: install side-by-side con WebLibre ufficiale (ID diversi) senza conflitto.
- [ ] Non pubblicare asset trademarked (logo WebLibre, Firefox, Tor onion ufficiale come brand app, ecc.).

## Nota AGPL

Il cambio identità **non** rimuove gli obblighi AGPL-3.0 sul codice derivato: source disponibile, notice di licenza, attribution upstream.
