# Note di build — Fase 1 (verificate sul worker cloud)

Commit base: `1c15f622` (`prepare new alpha`) — allineato a `upstream/main` al momento dell’analisi.

## Toolchain usata

| Tool | Versione |
|---|---|
| Flutter | 3.47.8 (stable, come CI) |
| Dart | 3.13.7 |
| OpenJDK | 17 (obbligatorio per `build-go-runtime`) |
| Go | 1.25.4 |
| Android SDK | platform 35/36 + `platforms;android-37.0`, build-tools 36 |
| NDK | 29.0.14206865 (`weblibre.ndkVersion`) |
| Node | 22.x (componenti JS) |
| melos | 7.8.1 |

## Passi (ordine CI)

```bash
# 1. Dipendenze Dart workspace
dart pub global activate melos 7.8.1
melos bootstrap

# 2. L10n template
python3 scripts/merge_l10n_arb.py --check

# 3. Asset esterni (bangs, bridges, ublock, sites raw, …)
melos run update-assets --no-select

# 4. Componenti JS + DB quotes/sites
melos run build-components --no-select

# 5. Sorgenti native pinate
source native/go_mobile_runtime/pins.env
# clone SagerNet/sing-box @ SING_BOX_TAG/COMMIT
# clone tladesignz/IPtProxy @ IPTPROXY_TAG/COMMIT + submodule dnstt
export SING_BOX_SOURCE=... IPTPROXY_SOURCE=...
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
melos run build-go-runtime --no-select
# → native/go_mobile_runtime/build/weblibre-go.aar (gitignored)

# 6. APK (verifica compilabilità; keystore SOLO locale di test)
export KEY_PATH=apps/weblibre/fase1-local.jks   # gitignored; NON distribuire
export KEY_PASSWORD=... KEY_ALIAS=...
cd apps/weblibre
flutter build apk --release --flavor alpha \
  --target-platform android-arm64 --split-per-abi --no-tree-shake-icons
```

## Esiti intermedi Fase 1

| Step | Esito |
|---|---|
| `melos bootstrap` | SUCCESS (13 packages) |
| `merge_l10n_arb.py --check` | Merged ARB up to date |
| `update-assets` | SUCCESS |
| `build-components` | SUCCESS (webpack container-proxy + readability; quotes.db; sites.db) |
| `build-go-runtime` | SUCCESS — `weblibre-go.aar` ~115 MB |
| Suite test proxy (314) | **All tests passed** |
| `flutter build apk` alpha arm64 | **SUCCESS** — `app-alpha-arm64-v8a-release.apk` ~154 MB (~161 MB path flutter-apk); firmato solo con keystore locale di verifica |

## Avvertenze

- Senza `weblibre-go.aar` Gradle fallisce subito.  
- Java 21 **non** è accettato dallo script gomobile (richiede 17).  
- `compileSdk = 37` con `android.experimental.disableCompileSdkChecks=true`.  
- Plugin `country_codes`, `cryptography_flutter`, `home_widget` applicano ancora KGP (warning Flutter).  
- Privacy Pass (`privacypass_client`) compila Rust `aarch64-linux-android` alla prima build.  
- Il keystore `fase1-local.jks` è solo per dimostrare che il signingConfig release riceve un storeFile; **non** è un certificato di produzione.

## Installazione su device fisico

Da questa VM cloud **non** è disponibile l’USB del PC di Sergio. Dopo la build: copiare l’APK sul PC e `adb install -r …`.
