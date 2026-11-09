# Rischi sicurezza e incompatibilità (Fase 1)

## Licenza progetto e dipendenze

| Componente | Licenza | Implicazione |
|---|---|---|
| WebLibre (albero app) | **AGPL-3.0** (`COPYING`) | Derivati devono restare AGPL; offrire source agli utenti della versione modificata; notice |
| `flutter_mozilla_components` | MPL-2.0 | Compatibile; file MPL restano MPL |
| `simple_intent_receiver` | MIT | OK |
| Altri package path (`flutter_tor`, `flutter_singbox_proxy`, …) | Placeholder `TODO: Add your license here` | **Gap**: chiarire che ereditano AGPL del monorepo o aggiungere LICENSE esplicite |
| sing-box (nativo) | Licenza upstream SagerNet (verificare ad ogni bump pin) | Obbligo compliance binary notice |
| IPtProxy / Tor pluggable transports | Licenze rispettive upstream | Attribution |
| Dipendenze Dart hosted (~236) | Miste (BSD/MIT/Apache…) | Inventario completo prima del Play release |
| Git deps (`search_client`, `secure_archive`, …) | Progetti FaFre/WebLibre | Rischio accoppiamento servizi “Supporters/Search”; non sono Pro Billing ma possono richiedere backend |

### Obblighi AGPL rilevanti per un browser Play

- Distribuzione dell’APK/AAB → mettere a disposizione il **corresponding source** della versione shippata.
- Modifiche server-side che dialogano con l’app (se un giorno ci fossero) cadono sotto AGPL network clause; con vincolo “no paid API obbligatorie” e on-device, preferire architetture senza backend proprietario chiuso.
- Pro commerciale via Play Billing **non** elude AGPL sul client.

## Play Store / policy

- VPN/proxy in-app: dichiarare correttamente le permission (`INTERNET`, eventuali VPNService se usate a livello device — WebLibre oggi isola proxy al browser via SOCKS locali sing-box, non VPN system-wide per default: verificare packaging prima della submission).
- Privacy policy obbligatoria; **no ads**, no telemetria identificante (allineato a WebLibre: telemetria Gecko disabilitata/locked).
- Brand: non usare marchi Mozilla/Firefox/WebLibre come nome app.
- User-generated / proxy lists: rischio di abuso; serve disclosure e, se necessario, gating geografico/policy.

## Proxy gratuiti — rischi tecnici

1. **Exit node malevolo**: MITM su HTTP, downgrade, logging credenziali.  
2. **Leak IP reale**: DNS leak, WebRTC, fallback automatico — da vietare in design Free Proxy mode.  
3. **Liste GPL / senza licenza**: redistribuire o vendere bundling può creare obblighi o assenza di diritto.  
4. **ToS di terzi / GitHub AUP**: uso “responsabile” richiesto da Proxifly; scraping aggressivo può violare ToS.  
5. **NodesRadar**: nodi di provenienza eterogenea, nessun LICENSE chiaro → default **opt-in avanzato** o esclusione.

## Incompatibilità / accoppiamenti da gestire

| Tema | Rischio | Mitigazione pianificata |
|---|---|---|
| `applicationId` invariato | Collisione con WebLibre su device / Play | Cambio ID in Fase 2 |
| Servizi Supporters / Search | Dipendenza da infrastruttura upstream | Lasciare opzionale o staccare; non costruire Pro sopra |
| Privacy Pass / Rust (`privacypass_client`) | Toolchain Rust+NDK allunga build | Documentato in build-notes; CI come upstream |
| `compileSdk = 37` con check disabilitati | Fragilità AGP/Flutter | Seguire fix upstream; non “fixare” DSL |
| Package LICENSE TODO nei plugin | Incompletezza compliance | Fix documentale/legale in fase identity |
| Keystore CI upstream secrets | Non disponibili sul fork | Keystore proprio Sergio |

## Vincoli utente (non negoziabili in questo progetto)

- No VPS a pagamento, no proxy a pagamento, no API a pagamento obbligatorie.  
- No ads / telemetria identificante.  
- Controlli on-device.  
- Identità app indipendente.  
- Compliance AGPL + licenze dipendenze.  
- Pro solo più avanti via Play Billing.  
- Tenere Gecko + sing-box.
