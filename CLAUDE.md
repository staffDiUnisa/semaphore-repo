# 🤖 CLAUDE.md - Claude Code Development Guide

Questo file contiene le istruzioni per continuare lo sviluppo di questo repository con **Claude Code**.

## 📋 Contesto del Progetto

**Nome**: Ansible Automation - SemaphoreUI Repository  
**Descrizione**: Repository Ansible ottimizzato per SemaphoreUI con automazione aggiornamenti Ubuntu/Debian  
**Linguaggio**: YAML (Ansible) + Bash (Script)  
**Status**: Production Ready  
**Versione**: 1.0.0

## 🎯 Obiettivo Principale

Repository pronto per l'utilizzo con **SemaphoreUI** - interfaccia web moderna per Ansible.

Fornisce:
- ✅ Playbook per aggiornamento pacchetti Ubuntu con reboot intelligente
- ✅ Inventari multi-ambiente (production, staging, development)
- ✅ Pronto da collegare a un'istanza SemaphoreUI già installata
- ✅ Documentazione completa in italiano

## 📁 Struttura Progetto

```
ansible-semaphore/
├── playbooks/              # Playbook Ansible
│   └── update-packages.yml
├── inventory/              # Inventari multi-ambiente
│   ├── production.yml
│   ├── staging.yml
│   └── development.yml
├── group_vars/             # Variabili globali
│   └── all.yml
├── host_vars/              # Variabili per singoli host
├── roles/                  # Ruoli Ansible (espandibile)
├── files/                  # File statici
├── templates/              # Template Jinja2
├── docs/                   # Documentazione
├── .gitignore              # Esclusioni git
├── README.md               # Documentazione principale
├── REPOSITORY_STRUCTURE.md # Struttura del repository
└── CLAUDE.md              # Questo file
```

> ℹ️ Questo repository contiene solo i playbook/inventari/variabili da collegare a un'istanza SemaphoreUI **già installata**. Non contiene script o configurazioni per installare SemaphoreUI stesso.

## 🔧 Stack Tecnologico

| Componente | Versione | Note |
|-----------|----------|------|
| Ansible | 2.9+ | Core automation |
| SemaphoreUI | Latest | Web UI (già installata, esterna a questo repo) |
| Bash | 4+ | Scripting |

## 🚀 Come Continuare lo Sviluppo

### 1. Carica il Progetto in Claude Code

```bash
# Dentro Claude Code, importa il file zip
# o clona il repository
git clone https://github.com/yourorg/ansible-semaphore.git
cd ansible-semaphore
```

### 2. Comprendi la Struttura

Leggi questi file in ordine:
1. **README.md** - Overview progetto
2. **docs/QUICKSTART.md** - Collegamento a SemaphoreUI e primo task
3. **playbooks/update-packages.yml** - Playbook principale
4. **inventory/production.yml** - Inventari

### 3. Task di Sviluppo Comuni

#### Aggiungere un Nuovo Playbook

```bash
# 1. Creare il file
touch playbooks/my-new-playbook.yml

# 2. Struttura di base
---
- name: Descrizione del playbook
  hosts: all
  become: true
  gather_facts: true
  
  tasks:
    - name: Primo task
      debug:
        msg: "Ciao!"
```

#### Aggiungere un Nuovo Inventario

```yaml
# inventory/my-environment.yml
---
all:
  vars:
    ansible_user: ansible
    ansible_connection: ssh
  children:
    my_group:
      hosts:
        host1.example.com:
          ansible_host: 192.168.1.1
```

#### Aggiungere un Nuovo Ruolo

```bash
# 1. Creare la struttura
mkdir -p roles/my-role/{tasks,handlers,templates,files,vars}

# 2. Creare tasks/main.yml
cat > roles/my-role/tasks/main.yml << 'EOF'
---
- name: Primo task del ruolo
  debug:
    msg: "Ruolo in esecuzione"
EOF

# 3. Usare il ruolo in un playbook
---
- hosts: all
  roles:
    - my-role
```

## 📝 File Importanti da Modificare

### Playbook Principale
**File**: `playbooks/update-packages.yml`

Modificare per:
- Aggiungere nuovi task
- Cambiare handlers
- Aggiungere variabili
- Ottimizzare performance

### Inventari
**File**: `inventory/production.yml`, `inventory/staging.yml`, etc.

Modificare per:
- Aggiungere/rimuovere host
- Cambiare variabili per host
- Aggiungere nuovi gruppi
- Aggiungere configurazioni specifiche

### Variabili Globali
**File**: `group_vars/all.yml`

Modificare per:
- Aggiungere variabili globali
- Cambiare timeout/configurazioni
- Aggiungere template per email/notifiche

### Documentazione
**File**: `README.md`, `docs/QUICKSTART.md`, `REPOSITORY_STRUCTURE.md`

Mantenere aggiornata la documentazione quando:
- Aggiungi nuove features
- Cambi il workflow
- Aggiungi nuovi inventari/playbook

## 🔍 Validazione & Testing

### Verificare la Sintassi Ansible

```bash
# Controllare il playbook
ansible-playbook playbooks/update-packages.yml --syntax-check

# Validare l'inventario
ansible-inventory -i inventory/production.yml --list

# Dry-run del playbook
ansible-playbook playbooks/update-packages.yml \
  -i inventory/production.yml \
  --check \
  -e dry_run=true
```

### Testare con SemaphoreUI (già installato)

```bash
# Aggiungere/aggiornare il repository in SemaphoreUI (Repositories → Refresh)
# Creare o aggiornare un template
# Eseguire un task di test con dry_run: true
```

## 📚 Componenti Principali

### 1. Playbook: `update-packages.yml`

**Scopo**: Aggiornare pacchetti Ubuntu con reboot intelligente

**Features**:
- apt update
- apt upgrade
- apt autoremove
- Reboot condizionato
- Logging e riepilogo

**Variabili principali**:
- `dry_run`: Modalità test (true/false)
- `force_reboot`: Forzare riavvio (true/false)

**Handlers**: Controllano quando riavviare

### 2. Inventari

**production.yml** - 3 webserver, 2 database, 1 monitoring  
**staging.yml** - 1 webserver, 1 database  
**development.yml** - 2 test server + localhost

Tutti contengono variabili per configurare il comportamento.

> ⚠️ Gli host elencati negli inventari sono placeholder di esempio (`*.example.com`): vanno sostituiti con i server reali prima dell'uso.

## 🎯 Roadmap di Sviluppo (Suggerito)

### Phase 1: Enhancement (Prima)
- [ ] Aggiungere logging dettagliato
- [ ] Aggiungere notifiche email
- [ ] Aggiungere backup pre-update
- [ ] Aggiungere verifiche di salute del sistema

### Phase 2: Nuovi Playbook
- [ ] Playbook per patch security only
- [ ] Playbook per kernel update
- [ ] Playbook per cleanup disco
- [ ] Playbook per service restart

### Phase 3: Ruoli Riutilizzabili
- [ ] Ruolo per pre-update checks
- [ ] Ruolo per backup automation
- [ ] Ruolo per health monitoring
- [ ] Ruolo per email notifications

### Phase 4: Integrazioni
- [ ] Webhook per Slack/Discord
- [ ] API per monitoraggio
- [ ] Metrics per Prometheus
- [ ] LDAP/AD authentication

## 🔐 Considerazioni di Sicurezza

Quando modifichi il codice, considera:

✅ **Non salvare password in plain text**
- Usare le Credentials di SemaphoreUI per SSH key/password
- Usare Ansible Vault per dati sensibili nei playbook

✅ **SSH key per autenticazione**
- Non usare password quando possibile
- Ruotare le chiavi regolarmente

✅ **Audit logging**
- Loggare tutte le operazioni
- Conservare i log per audit

✅ **Least privilege**
- Usare sudoers con NOPASSWD solo se necessario
- Limitare i permessi dei file

✅ **Validazione input**
- Validare le variabili nei playbook
- Usare handlers per error handling

## 💡 Best Practices

### Ansible

```yaml
# ✅ BON
- name: Descrizione chiara del task
  apt:
    name: "{{ package_name }}"
    state: present
  become: yes
  tags:
    - packages

# ❌ MALE
- name: update
  shell: apt-get install -y something
```

### Script Bash

```bash
# ✅ BON
#!/bin/bash
set -e  # Exit on error
print_error() {
    echo "ERROR: $1" >&2
}

# ❌ MALE
#!/bin/bash
apt-get install -y something
```

### Documentazione

```markdown
# ✅ BON
## Collegare il repository a SemaphoreUI

Prerequisiti:
- Istanza SemaphoreUI già installata
- Accesso SSH ai server da gestire

Istruzioni:
1. Repositories → New Repository (URL di questo repo)
2. Inventories → New Inventory (inventory/production.yml)
3. Templates → New Template (playbooks/update-packages.yml)

# ❌ MALE
# Setup
fai partire il playbook
```

## 🐛 Debug & Troubleshooting

### Verbose Mode Ansible

```bash
ansible-playbook playbooks/update-packages.yml \
  -i inventory/production.yml \
  -vvv  # Extra verbosity
```

### SSH Testing

```bash
# Test SSH connection
ssh -v ansible@hostname

# Test con specific key
ssh -i ~/.ssh/key.pem ansible@hostname
```

## 📖 Risorse Esterne

- **Ansible Docs**: https://docs.ansible.com/
- **SemaphoreUI Docs**: https://docs.semaphoreui.com/
- **YAML Syntax**: https://yaml.org/
- **Bash Guide**: https://mywiki.wooledge.org/BashGuide

## 🔄 Git Workflow

```bash
# Creare un branch
git checkout -b feature/my-feature

# Fare modifiche
# ...

# Commit
git add .
git commit -m "feat: aggiunto mio feature"

# Push
git push origin feature/my-feature

# Pull request
# (su GitHub/GitLab)

# Merge a main
git checkout main
git merge feature/my-feature
git push origin main
```

## 📝 Convenzioni del Codice

### Naming Convention

```yaml
# Playbook files: lowercase-with-hyphens.yml
playbooks/update-packages.yml
playbooks/my-new-playbook.yml

# Inventory files: environment.yml
inventory/production.yml
inventory/staging.yml

# Group names: lowercase_with_underscores
all:
  children:
    web_servers:
    db_servers:

# Variables: lowercase_with_underscores
update_packages_enabled: true
force_reboot: false
```

### Task Descriptions

```yaml
# Descrizione chiara e concisa
- name: Aggiornare cache pacchetti APT
  apt:
    update_cache: yes

# Evitare descrizioni generiche
- name: update  # ❌ Non chiaro
- name: do stuff  # ❌ Non informativo
```

## 🚀 Deployment

### Su Produzione

```bash
# 1. Fare test su staging
ansible-playbook playbooks/update-packages.yml \
  -i inventory/staging.yml \
  -e dry_run=true

# 2. Fare test dry-run su production
ansible-playbook playbooks/update-packages.yml \
  -i inventory/production.yml \
  -e dry_run=true

# 3. Esecuzione reale
ansible-playbook playbooks/update-packages.yml \
  -i inventory/production.yml
```

### Con SemaphoreUI

1. Aggiungere repository
2. Creare template
3. Schedulare esecuzione
4. Monitorare da dashboard

## 📊 Metriche di Successo

Per valutare lo sviluppo:

- ✅ Tutti i playbook passano il syntax check
- ✅ Tutti gli inventari sono validi
- ✅ Documentazione aggiornata
- ✅ Test su staging prima di production
- ✅ Zero downtime nelle esecuzioni
- ✅ Tempo di esecuzione < 10 minuti

## ❓ FAQ Sviluppo

**D: Come aggiungere una nuova variabile?**  
R: Aggiungerla in `group_vars/all.yml` o `host_vars/hostname.yml`

**D: Come testare un playbook localmente?**  
R: Usare `--check` flag o `dry_run: true`

**D: Come aggiungere un nuovo host?**  
R: Modificare l'inventario appropriato in `inventory/`

**D: Come aggiungere un ruolo?**  
R: Creare directory in `roles/` con struttura standard

**D: Come abilitare il debug?**  
R: Aggiungere `-vvv` flag a ansible-playbook

## 📧 Contatti & Support

- **Documentazione**: Vedi README.md e docs/QUICKSTART.md
- **Issues**: GitHub Issues nel repository
- **Slack Community**: (Se disponibile)

---

## ✅ Checklist Prima di Commitare

Prima di fare un commit, verifica:

- [ ] Sintassi YAML valida (`--syntax-check`)
- [ ] Inventari validi (`--list-hosts`)
- [ ] Documentazione aggiornata
- [ ] Variabili naming corrette
- [ ] No password/secret in plain text
- [ ] Commenti utili nel codice
- [ ] Git message descrittivo
- [ ] Test su staging prima di production

---

**Buon sviluppo con Claude Code! 🚀**

Usa questo file come punto di riferimento durante lo sviluppo.
