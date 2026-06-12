# 🚀 Ansible Automation - SemaphoreUI Repository

Repository **Ansible** da collegare a un progetto **SemaphoreUI** già esistente, per l'automazione degli aggiornamenti di sistema su server Ubuntu/Debian.

## 📋 Contenuti

```text
semaphore-repo/
├── playbooks/                 # Playbook Ansible
│   ├── update-packages.yml   # Aggiornamento pacchetti Ubuntu
│   └── enroll-ansible-user.yml # Configura l'utente ansible su nuovi server
├── inventory/                 # Inventari per diversi ambienti
│   ├── production.yml        # Server di produzione
│   ├── staging.yml           # Server di staging
│   ├── development.yml       # Server di development
│   └── enrollment.yml        # Nuovi server da configurare
├── group_vars/               # Variabili per gruppi di host
│   └── all.yml              # Variabili globali
├── host_vars/               # Variabili specifiche per singoli host
├── roles/                   # Ruoli Ansible (espandibile)
├── files/                   # File statici
├── templates/               # Template Jinja2
└── docs/                    # Documentazione
```

## ⚡ Quick Start

Questo repository **non installa SemaphoreUI** (si presuppone già installato e funzionante), ma fornisce i playbook, gli inventari e le variabili da usare nei Template di un progetto SemaphoreUI.

### Collegare il repository a SemaphoreUI

1. **Project** → **Repositories** → **New Repository**
   - **Name**: `semaphore-repo`
   - **Git URL**: URL del repository (locale o remoto)
   - **Branch**: `main`
   - **Authentication**: secondo il metodo scelto (SSH/HTTPS)
2. **Inventories** → **New Inventory** per ciascun ambiente:
   - `inventory/production.yml`
   - `inventory/staging.yml`
   - `inventory/development.yml`
3. **Credentials** → aggiungere la SSH key dell'utente `ansible`
4. **Templates** → **New Template** usando il playbook desiderato

Vedi **[docs/QUICKSTART.md](docs/QUICKSTART.md)** per la guida passo-passo.

## 🎯 Features

✅ **Aggiornamento Automatico** - apt update, upgrade, autoremove
✅ **Riavvio Intelligente** - Solo quando necessario
✅ **Multi-Ambiente** - Production, Staging, Development
✅ **Credenziali Sicure** - SSH keys e password gestite da SemaphoreUI
✅ **Scheduling** - Aggiornamenti programmati
✅ **Audit Logging** - Traccia completa di tutte le operazioni tramite SemaphoreUI

## 🔧 Playbook Principale

### `playbooks/update-packages.yml`

Aggiorna i pacchetti Ubuntu con:
- ✅ apt update (aggiornamento lista pacchetti)
- ✅ apt upgrade (aggiornamento pacchetti)
- ✅ apt autoremove (pulizia)
- ✅ Reboot condizionato (solo se necessario)

**Utilizzo in SemaphoreUI:**
1. Templates → New Template
2. Playbook: `playbooks/update-packages.yml`
3. Inventory: `production.yml` (o staging/development)
4. Credentials: SSH key dell'utente ansible
5. Extra Variables:
   ```yaml
   dry_run: false
   force_reboot: false
   ```
6. Run

## 🛠️ Playbook di Enrollment

### `playbooks/enroll-ansible-user.yml`

Da usare sui server su cui l'utente `ansible` **non è ancora configurato**. Crea l'utente (se non esiste), lo configura in sudoers con `NOPASSWD` e autorizza la chiave pubblica SSH usata dagli altri playbook.

**Prerequisiti:**

- Una Credential **"Login With Password"** in SemaphoreUI per un utente amministrativo già presente sul server, con permessi sudo
- La variabile `ansible_authorized_key` in `group_vars/all.yml` impostata con la chiave pubblica SSH (la chiave privata corrispondente è la Credential SSH usata dagli altri playbook)

**Utilizzo in SemaphoreUI:**

1. Templates → New Template
2. Playbook: `playbooks/enroll-ansible-user.yml`
3. Inventory: `enrollment.yml` (con i nuovi server da configurare)
4. Credentials: utente amministrativo con permessi sudo (Login With Password)
5. Run

Dopo l'enrollment, spostare i nuovi host in `inventory/production.yml` (o staging/development): potranno essere gestiti con la Credential SSH dell'utente `ansible` come gli altri server.

## 📊 Inventari

### Production (`inventory/production.yml`)
- web1-3.prod.example.com
- db1-2.prod.example.com
- monitor.prod.example.com

### Staging (`inventory/staging.yml`)
- web-staging.example.com
- db-staging.example.com

### Development (`inventory/development.yml`)
- test-server-1.local
- test-server-2.local
- localhost (locale)

### Enrollment (`inventory/enrollment.yml`)

- Nuovi server non ancora configurati con l'utente `ansible`
- Usato solo con `playbooks/enroll-ansible-user.yml`

> ⚠️ Gli host elencati sono placeholder di esempio: modificarli con i server reali prima dell'uso.

## 🔐 Sicurezza

### Configurazione Server Ansible

Modo consigliato: eseguire `playbooks/enroll-ansible-user.yml` (sezione "Playbook di Enrollment" sopra), che crea l'utente, configura sudo `NOPASSWD` e autorizza la chiave SSH automaticamente.

In alternativa, manualmente su ogni server gestito, come root:

```bash
# Creare utente ansible
useradd -m -s /bin/bash ansible

# Aggiungere a sudoers (senza password)
echo "ansible ALL=(ALL) NOPASSWD:ALL" | tee /etc/sudoers.d/ansible

# Aggiungere chiave SSH pubblica (consigliato)
mkdir -p /home/ansible/.ssh
echo "ssh-rsa AAAA... your_public_key" >> /home/ansible/.ssh/authorized_keys
chmod 600 /home/ansible/.ssh/authorized_keys
chown ansible:ansible /home/ansible/.ssh -R
```

### Credenziali in SemaphoreUI

1. **SSH Key** (consigliato) - chiave privata aggiunta in SemaphoreUI, chiave pubblica sui server
2. **Password** - crittografata nel database, disponibile solo agli admin
3. Non committare mai segreti/password nel repository (vedi `.gitignore`)

## 📅 Scheduling

Configurare aggiornamenti programmati da SemaphoreUI → **Schedules**:

```cron
# Ogni domenica alle 2 AM
0 2 * * 0

# Ogni primo lunedì del mese alle 3 AM
0 3 ? * MON#1
```

## 📝 Variabili Disponibili

### Globali (`group_vars/all.yml`)

```yaml
update_packages_enabled: true    # Abilitare aggiornamenti
force_reboot: false               # Forzare riavvio
dry_run: false                    # Test mode
backup_before_update: false       # Backup pre-update
deploy_environment: production    # Ambiente
```

### Per Host (`inventory/XXXXX.yml`)

```yaml
server_role: web_primary          # Ruolo del server
update_schedule: "sunday 2:00"    # Schedule personalizzato
mysql_port: 3306                  # Porte custom
db_backup_before_update: true     # Backup DB prima update
```

## 🧪 Testing

### Dry-Run (nessuna modifica)

In SemaphoreUI, Extra Variables:
```yaml
dry_run: true
```

### Limitare ai server specifici

Nel template, **Inventory → Limit to hosts**: `webservers` oppure `db1.prod.example.com`

### Validazione locale

```bash
# Verificare sintassi
ansible-playbook playbooks/update-packages.yml --syntax-check

# Verificare inventario
ansible-inventory -i inventory/production.yml --list
```

## 🆘 Troubleshooting

### Connessione SSH fallisce
```bash
ssh -v ansible@hostname
```

### Repository non sincronizza in SemaphoreUI

1. Verificare URL e branch del repository
2. Verificare le credenziali Git configurate in SemaphoreUI
3. Forzare un refresh da **Repositories**

Vedi **[docs/QUICKSTART.md](docs/QUICKSTART.md)** per la guida completa.

## 🔄 Git Workflow

```bash
# Fare modifiche
nano playbooks/update-packages.yml

# Committare e pushare
git add .
git commit -m "feat: aggiunto nuovo task"
git push origin main

# SemaphoreUI sincronizza il repository al prossimo task/refresh
```

## 📚 Documentazione Correlata

- [Ansible Documentation](https://docs.ansible.com/)
- [SemaphoreUI Official](https://docs.semaphoreui.com/)
- [APT Module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)
- [Reboot Module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/reboot_module.html)

---

**Happy Automation! 🚀**
