# ⚡ Quickstart - Collegare il repository a SemaphoreUI

Guida rapida per collegare **questo repository** a un progetto **SemaphoreUI già installato e funzionante**, e creare il primo task.

## 📝 Prima di Iniziare

### 1. Configurare i Server Ansible

Se l'utente `ansible` **non è ancora presente** sui server, usare il playbook `playbooks/enroll-ansible-user.yml` (vedi sezione "Enrollment di Nuovi Server" più sotto) per crearlo e configurarlo automaticamente.

Se invece l'utente `ansible` è già configurato su tutti i server, passare direttamente al punto 2.

### 2. Aggiornare gli Inventari

Modificare gli host placeholder in `inventory/production.yml`, `inventory/staging.yml` e `inventory/development.yml` con i server reali.

## 🔌 Collegare il Repository a SemaphoreUI

### Menu → Repositories → New Repository

1. **Name**: `semaphore-repo`
2. **Git URL**: URL del repository (locale o remoto)
3. **Branch**: `main`
4. **Authentication**: secondo il metodo scelto (SSH/HTTPS)
5. Salvare e, se necessario, cliccare **Refresh** per sincronizzare i file

Verificare che SemaphoreUI veda `playbooks/` e `inventory/`.

## 📋 Configurare Inventari e Credenziali

### Menu → Inventories → New Inventory

1. **Name**: `Production`
2. **Type**: `File`
3. **File**: `inventory/production.yml`
4. Salvare

Ripetere per `inventory/staging.yml` (Staging) e `inventory/development.yml` (Development).

### Menu → Credentials → New Credential

1. **Type**: `SSH Key / Pair`
2. **Name**: `ansible-production`
3. **Username**: `ansible`
4. **Private Key**: incollare la chiave privata SSH
5. Salvare

## 🆕 Enrollment di Nuovi Server

Da fare **prima** di gestire un server con gli altri playbook, se l'utente `ansible` non è ancora configurato su quel server.

### 1. Impostare la chiave pubblica SSH

In `group_vars/all.yml`, impostare `ansible_authorized_key` con la chiave pubblica SSH corrispondente alla `ansible-production` creata sopra (il contenuto del file `.pub`).

### 2. Creare la Credential dell'utente amministrativo

**Menu → Credentials → New Credential**:

1. **Type**: `Login With Password`
2. **Name**: `bootstrap-admin`
3. **Username** / **Password**: utente amministrativo già presente sul server, con permessi sudo
4. Salvare

### 3. Aggiungere i nuovi server all'inventario di enrollment

Modificare `inventory/enrollment.yml` aggiungendo i nuovi host, poi in SemaphoreUI:

**Menu → Inventories → New Inventory**:

1. **Name**: `Enrollment`
2. **Type**: `File`
3. **File**: `inventory/enrollment.yml`
4. Salvare

### 4. Creare ed eseguire il Template di enrollment

**Menu → Templates → New Template**:

1. **Name**: `Enroll Ansible User`
2. **Playbook**: `playbooks/enroll-ansible-user.yml`
3. **Inventory**: `Enrollment`
4. **Credentials**: `bootstrap-admin`
5. **Become Method**: `sudo`
6. **Create**, poi **Run** → **Start Task**

### 5. Spostare i server enrollati

Dopo l'esecuzione, spostare gli host da `inventory/enrollment.yml` a `inventory/production.yml` (o staging/development): da questo momento sono gestibili con la Credential SSH `ansible-production`.

## 🎯 Primo Task

### Creare un Template

#### Menu → Templates → New Template

1. **Name**: `Test Update - Production`
2. **Playbook**: `playbooks/update-packages.yml`
3. **Inventory**: `Production`
4. **Credentials**: `ansible-production`
5. **Become Method**: `sudo`
6. **Extra Variables**:

   ```yaml
   dry_run: true
   force_reboot: false
   ```

7. **Create**

### Eseguire il Task

1. Cliccare il template creato
2. Pulsante **Run**
3. **Start Task**
4. Osservare l'output in tempo reale
5. Al termine, verificare il riepilogo finale del playbook

## 📅 Programmazione Automatica

### Menu → Schedules → New Schedule

1. **Name**: `Update Production Weekly`
2. **Template**: il template creato sopra
3. **Frequency**: `Weekly`
4. **Day**: `Sunday`
5. **Time**: `02:00`
6. **Variables**:

   ```yaml
   dry_run: false
   force_reboot: false
   ```

7. **Active**: ON
8. **Create**

## 🔍 Monitorare l'Esecuzione

- **Dashboard** - panoramica di tutti i task e relativo stato
- **Task History** - log cronologico, filtrabile per data/stato/template
- **Task Output** - output Ansible in tempo reale e riepilogo finale

## 🧪 Testing

### Dry-Run (nessuna modifica)

Extra Variables del template:

```yaml
dry_run: true
```

Eseguire il task e verificare che **non** vengano applicate modifiche al sistema.

### Su un singolo server

Nel template, in **Inventory → Limit to hosts**, indicare l'host singolo (es. `web1.prod.example.com`).

## 🚨 Troubleshooting Rapido

### "Connection refused" / SSH

```bash
ssh -i /path/to/key ansible@server-ip
```

Verificare in SemaphoreUI:

- **Credentials**: chiave/password corretta
- **Inventory**: IP/hostname corretto

### "Permission denied (sudo)"

Verificare sul server, con `sudo visudo`, che sia presente:

```text
ansible ALL=(ALL) NOPASSWD:ALL
```

### Il task non parte

Verificare in SemaphoreUI:

1. Template configurato correttamente (playbook, inventory, credentials)
2. Sintassi YAML corretta

```bash
ansible-playbook playbooks/update-packages.yml --syntax-check
```

### Il repository non sincronizza

**Menu → Repositories** → selezionare il repository → **Refresh**

## 💡 Consigli

- ✅ Fare sempre un dry-run prima
- ✅ Testare su staging prima di production
- ✅ Usare SSH key per sicurezza
- ✅ Documentare le modifiche nel repository (commit + push)

## 📚 Link Utili

- [README.md](../README.md) - Documentazione principale del repository
- [SemaphoreUI Official](https://docs.semaphoreui.com/) - Documentazione ufficiale
