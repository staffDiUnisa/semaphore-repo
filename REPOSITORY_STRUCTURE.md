# 📦 Struttura Repository SemaphoreUI

```text
semaphore-repo/
│
├── 📄 README.md                          # Documentazione principale
├── 📄 REPOSITORY_STRUCTURE.md            # Questo file
├── 📄 .gitignore                         # File da escludere da git
│
├── 📁 playbooks/
│   └── update-packages.yml              # Playbook principale
│
├── 📁 inventory/
│   ├── production.yml                   # Inventario produzione
│   ├── staging.yml                      # Inventario staging
│   └── development.yml                  # Inventario development
│
├── 📁 group_vars/
│   └── all.yml                          # Variabili globali
│
├── 📁 host_vars/                        # (Directory per variabili host)
│   └── .gitkeep
│
├── 📁 roles/                            # (Directory per ruoli futuri)
│   └── .gitkeep
│
├── 📁 files/                            # (Directory per file statici)
│   └── .gitkeep
│
├── 📁 templates/                        # (Directory per template Jinja2)
│   └── .gitkeep
│
└── 📁 docs/
    └── QUICKSTART.md                    # Quick start guide
```

## 📋 File Principali

### 1. README.md (LEGGI QUESTO PRIMO!)

- Overview del progetto
- Features principali
- Quick start
- Link alla documentazione

### 2. docs/QUICKSTART.md (INIZIO RAPIDO)

- Collegare il repository a un progetto SemaphoreUI esistente
- Configurare inventari e credenziali
- Creare il primo template
- Testing e monitoraggio

### 3. .gitignore

- Esclude file sensibili
- Password e chiavi SSH
- Log e file temporanei

## 🎮 Playbook

### playbooks/update-packages.yml

Esegue:

1. ✅ apt update (aggiornamento lista pacchetti)
2. ✅ apt upgrade (aggiornamento pacchetti)
3. ✅ apt autoremove (pulizia)
4. ✅ Reboot condizionato (solo se necessario)
5. ✅ Riepilogo operazioni

**Variabili:**

- `dry_run: true/false` - Modalità test
- `force_reboot: true/false` - Forzare riavvio

## 📦 Inventari

### inventory/production.yml

- webservers (3 server)
- dbservers (2 server)
- monitoring (1 server)
- Variabili per environment production

### inventory/staging.yml

- Singolo server per ambiente di staging
- Variabili per test

### inventory/development.yml

- Server di test locali
- localhost per sviluppo

> ⚠️ Gli host elencati sono placeholder di esempio: aggiornarli con i server reali prima dell'uso.

## 🔧 Variabili

### group_vars/all.yml

Variabili globali disponibili a tutti i playbook:

- `update_packages_enabled` - Abilita aggiornamenti
- `force_reboot` - Forza riavvio
- `dry_run` - Modalità test
- `environment` - Nome ambiente

## 🎯 Prossimi Passi

1. **Aggiornare gli inventari** con i server reali (`inventory/*.yml`)
2. **Collegare il repository** al progetto SemaphoreUI (Repositories → New Repository)
3. **Configurare le credenziali SSH** in SemaphoreUI
4. **Creare il primo template**
   - Playbook: `playbooks/update-packages.yml`
   - Inventory: `production.yml` (o staging/development)
   - Credentials: SSH key
5. **Eseguire il primo task**
   - Test: `dry_run: true`
   - Produzione: `dry_run: false`

## 📞 Support

- **Documentazione**: Vedi README.md
- **Quick Start**: Vedi docs/QUICKSTART.md
- **Ufficiale**: [docs.semaphoreui.com](https://docs.semaphoreui.com/)

---

**Pronto per iniziare? Leggi il README.md!** 🚀
