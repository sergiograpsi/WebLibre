# Piano di implementazione con test verificabili

Scope: dopo Fase 1 (questo branch). **Pro/Play Billing escluso** finché non richiesto.

## Fase 1 — Analisi e prep (questa)

| Deliverable | Test verificabile | Esito atteso |
|---|---|---|
| Repo analizzato + remote `upstream` | `git remote -v` mostra origin fork + upstream FaFre | OK |
| Build APK senza cambio comportamento | `flutter build apk --release --flavor alpha …` exit 0; APK presente | Vedi `build-notes.md` |
| Branch indipendente | Branch `cursor/weblibre-fase1-prep-3b37` pushato | OK |
| Scaffolding identità | File in `docs/nuovo-browser/identity-change-plan.md` | OK |
| Matrice feature / rischi / piano | File docs + rapporto store | OK |
| Test regressione proxy esistenti | `flutter test` su suite proxy (sotto) | 314 passed (Fase 1) |

Comando test Fase 1 eseguito:

```bash
cd apps/weblibre
flutter test \
  test/features/proxy \
  test/features/user/domain/repositories/proxy_routing_settings_test.dart \
  test/core/secure_storage/legacy_proxy_secrets_test.dart \
  test/features/geckoview/features/browser/domain/services/proxy_settings_replication_test.dart
```

## Fase 2 — Identità Android indipendente

| Intervento | Test |
|---|---|
| Nuovo `applicationId` + label | APK installabile side-by-side a WebLibre; `adb shell pm path <nuovoId>` |
| Icone originali | Adaptive icon visibile; nessun asset WebLibre nei mipmap generati |
| Signing proprio | `apksigner verify --print-certs` fingerprint ≠ WebLibre upstream |
| About AGPL + attribution | UI/test golden o check stringa license |
| Metadata store | `metadata/` senza marchio WebLibre come titolo app |

## Fase 3 — UX tre modalità rete

| Intervento | Test |
|---|---|
| Mode Direct protected | Con mode on, nessun SOCKS locale attivo; tracking defaults strict |
| Mode Free proxy | Profilo obbligatorio; kill proxy → navigazione fallisce / banner, **IP reale non usato** (verifica con endpoint echo IP mock in test) |
| Mode Tor | Avvio Tor esplicito; traffico container/tab secondo setting |
| Nessun silent fallback | Unit test su policy router: transition `proxyDown` ⇒ `blocked` not `direct` |

## Fase 4 — Free Proxy Manager (design → implementazione)

| Intervento | Test |
|---|---|
| Provider monosans (MIT) | Parser + refresh; mock HTTP fixture |
| Provider proxifly (opzionale) | Solo endpoint pubblici senza API key obbligatoria |
| NodesRadar | Default off; warning copy; feature flag |
| Health check on-device | Latency/probe senza spedire payload sensibili |
| Attribution licenze in UI | Stringhe MIT/GPL/CC BY-SA dove dovute |

## Fase 5 — Privacy Monitor + hardening

| Intervento | Test |
|---|---|
| Monitor DNS / proxy / Tor / permission | Widget stato da snapshot on-device; test provider |
| Alert WebRTC/IP leak (best-effort Gecko prefs) | Preferenze applicate; test replication settings |
| Gatekeeper intent regressione | Test Kotlin/Dart esistenti `intent_gatekeeper` / `simple_intent_receiver` verdi |
| No telemetria nuova | Grep CI su endpoint analytics; review dipendenze |

## Fase 6 — Prep commerciale Pro (solo più avanti)

Fuori scope fino a richiesta esplicita: Play Billing, feature gating Pro, senza togliere capacità free core.

## Criteri di uscita globali

- Gecko + sing-box sempre presenti.  
- Merge da `upstream` ancora fattibile.  
- Nessuna feature esistente cancellata “per semplificare”.  
- Source AGPL pubblicata per ogni build distribuita.
