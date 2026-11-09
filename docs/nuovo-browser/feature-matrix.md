# Matrice funzionalità (Fase 1 — analisi)

Legenda: **P** = già presente in WebLibre · **M** = da modificare per Nuovo Browser · **D** = da sviluppare · **N** = non in scope / non fare ora

| Area | Stato | Note |
|---|---|---|
| Motore Gecko (Mozilla Components) | **P** | Tenere; aggiornamenti via upstream |
| Runtime sing-box (gomobile AAR + plugin) | **P** | Pin in `native/go_mobile_runtime/pins.env` (sing-box v1.14.4) |
| Tor integrato (`flutter_tor` / IPtProxy) | **P** | Modalità Tor opzionale pianificata; già usabile |
| Proxy SOCKS/HTTP/SS/VMess/VLESS/Trojan/Hysteria/TUIC/WG/… | **P** | Editor profili + import URI/QR/file WireGuard |
| Routing per container / tab isolati | **P** | `proxy_settings_replication` |
| Profili, container, tab tree, PWA | **P** | Non rimuovere |
| Tracking protection, HTTPS-only, DoH, GPC, URL cleaner | **P** | Default privacy già strict |
| Intent gatekeeper | **P** | Hardening ingresso intent |
| Indice full-text on-device, feed RSS, bangs | **P** | Nessuna telemetria proprietaria dichiarata |
| Estensioni Firefox-compatibili / uBlock Quick Start | **P** | |
| Sync Firefox / WebLibre Supporters / Search credits | **P** / **M** | Dipendenze servizi upstream; valutare disaccoppio o leave-as-is senza Pro |
| Identità Android (nome, icona, applicationId, signing) | **M** | Solo piano in Fase 1; vedi `identity-change-plan.md` |
| About / store metadata brand | **M** | Attribution AGPL + nuovo brand |
| Modalità “direct protected” (senza proxy, hardening on) | **P** / **M** | Base già presente; formalizzare come mode UX |
| Free Proxy Manager (fonti pubbliche) | **D** | Solo design Fase 1; no silent fallback a IP reale |
| Privacy Monitor (dashboard rischi/leak) | **D** | Nuovo; on-device |
| Security hardening aggiuntivo | **M** / **D** | Estendere gatekeeper, policy rete, UI leak-proof |
| Modalità Tor esplicita (UX unificata 3 mode) | **M** | Tor già c’è; unificare switch mode |
| Pro / Play Billing | **N** | Esplicitamente fuori scope Fase 1 |
| VPS / proxy a pagamento / API a pagamento obbligatorie | **N** | Vietato dai vincoli utente |
| Ads / telemetria identificante | **N** | Non introdurre |

## Tre modalità di rete (design successivo)

1. **Direct protected** — traffico diretto; protezioni browser on; nessun proxy.  
2. **Free proxy** — utente sceglie fonte/profilo; se il proxy cade → **errore esplicito**, mai fallback silenzioso all’IP reale.  
3. **Tor** — percorso Tor esplicito (già supportato a livello feature).

## Free proxy sources (solo analisi)

| Fonte | Licenza / ToS (snapshot Fase 1) | Affidabilità | Uso consigliato |
|---|---|---|---|
| [monosans/proxy-list](https://github.com/monosans/proxy-list) | MIT sulle liste; campi ASN/geo MaxMind → CC BY-SA 4.0 se riusati | Aggiornamento orario; qualità variabile | **Preferita** per lista HTTP/SOCKS; evitare di redistribuire campi GeoLite senza attribution |
| [proxifly/free-proxy-list](https://github.com/proxifly/free-proxy-list) | GPL-3.0 sul repo; API Proxifly con key opzionale (limiti) | Refresh ~5 min; lista grande | Usabile come fetch pubblico CDN; attenzione copyleft se si incorpora codice/tooling del repo; API key = rischio “paid/mandatory” da evitare |
| [nodesradar](https://nodesradar.com/) / `nodesradar/nodesradar-en` | **Nessuna licenza** sul repo GitHub (`license: null`); aggregatore di nodi pubblici | Nodi VLESS/SS/… instabili; operatori sconosciuti | **Alto rischio** legale/ToS e sicurezza; solo se policy esplicita + warning utente; non default |

Vincolo UX sicurezza: qualsiasi manager di proxy gratuiti deve mostrare che l’operatore del nodo può vedere metadati/destinatari; HTTPS non protegge SNI/IP di destinazione verso il proxy.
