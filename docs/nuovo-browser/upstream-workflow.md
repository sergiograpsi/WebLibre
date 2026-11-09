# Workflow upstream (mantenere i pull da FaFre/WebLibre)

## Remoti

Sul fork `sergiograpsi/WebLibre`:

```bash
git remote add upstream https://github.com/FaFre/WebLibre.git   # se manca
git remote -v
# origin   → sergiograpsi/WebLibre (push del lavoro Nuovo Browser)
# upstream → FaFre/WebLibre (solo fetch)
```

Non pushare su `upstream`.

## Branch di sviluppo

Branch Fase 1: `cursor/weblibre-fase1-prep-3b37` (basato su `main` allineato a upstream al momento del fork sync).

Per feature successive: branch `cursor/<tema>-3b37` da un `main` aggiornato, oppure stack su questo branch di prep se ancora aperto.

## Portare aggiornamenti upstream

```bash
git fetch upstream
git checkout main
git merge --ff-only upstream/main   # preferito se main è pulito
# oppure: git rebase upstream/main  (solo su branch non condivisi)
git push origin main

git checkout cursor/weblibre-fase1-prep-3b37
git merge main   # o rebase, a scelta del maintainer
```

Se compaiono conflitti su file di identità (quando la Fase 2 avrà cambiato brand), risolvere **privilegiando** le scelte Nuovo Browser su brand/`applicationId`, e **privilegiando** upstream su engine/Gecko/sing-box/bugfix.

## Cosa non fare

- Non riscrivere la storia di `upstream/main`.
- Non rimuovere il remote `upstream`.
- Non fare mass refactor “per pulizia” che rende impossibile il merge (vincolo Fase 1).
- Non commitare keystore, `.aar` nativi grandi, o `fase1-local.jks`.
