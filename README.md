# FLARE-VM — Guida Pratica all'Installazione / Practical Setup Guide

> **IT** | Guida step-by-step per configurare un ambiente di reverse engineering e malware analysis con [FLARE-VM](https://github.com/mandiant/flare-vm) su macchina virtuale (VirtualBox o VMware).
>
> **EN** | Step-by-step guide to set up a reverse engineering and malware analysis environment with [FLARE-VM](https://github.com/mandiant/flare-vm) on a virtual machine (VirtualBox or VMware).

---

## Sommario / Table of Contents

- [Requisiti / Requirements](#requisiti--requirements)
- [Metodo 1 — Installazione da ISO / Method 1 — Install from ISO](#metodo-1--installazione-da-iso--method-1--install-from-iso)
  - [Step 1 — Download Windows 10 ISO](#step-1--download-windows-10-iso)
  - [Step 2 — Creare la VM / Create the VM](#step-2--creare-la-vm--create-the-vm)
  - [Step 3 — Disattivare Windows Defender / Disable Windows Defender](#step-3--disattivare-windows-defender--disable-windows-defender)
  - [Step 4 — Installare FLARE-VM / Install FLARE-VM](#step-4--installare-flare-vm--install-flare-vm)
  - [Step 5 — Post-installazione / Post-install](#step-5--post-installazione--post-install)
- [Metodo 2 — OVA pronta (alternativa rapida) / Method 2 — Pre-built OVA (quick alternative)](#metodo-2--ova-pronta-alternativa-rapida--method-2--pre-built-ova-quick-alternative)
- [Fonti / Sources](#fonti--sources)

---

## Requisiti / Requirements

| | Minimo / Minimum |
|---|---|
| **OS guest** | Windows 10 (22H2) o superiore / or later |
| **RAM** | 4 GB (consigliati 8 GB / 8 GB recommended) |
| **Disco / Disk** | 60 GB+ |
| **PowerShell** | ≥ 5 |
| **Connessione / Internet** | Necessaria durante l'installazione / Required during install |
| **Hypervisor** | VirtualBox **o** VMware (Workstation, Player, Fusion) |

> **IT** — Il nome utente Windows non deve contenere spazi o caratteri speciali.
>
> **EN** — The Windows username must not contain spaces or special characters.

---

## Metodo 1 — Installazione da ISO / Method 1 — Install from ISO

### Step 1 — Download Windows 10 ISO

**IT** — Scarica l'ISO ufficiale di Windows 10 dal sito Microsoft:

**EN** — Download the official Windows 10 ISO from Microsoft's website:

**Link:** <https://www.microsoft.com/software-download/windows10>

> **Nota / Note:**
>
> **IT** — Se visiti la pagina da un PC Windows, il sito ti proporrà il Media Creation Tool. Per ottenere il download diretto dell'ISO, cambia lo User Agent del browser in "Chrome — Android" (tramite DevTools → Network Conditions) e ricarica la pagina.
>
> **EN** — If you visit from a Windows PC, the site will push the Media Creation Tool. To get the direct ISO download, change your browser's User Agent to "Chrome — Android" (via DevTools → Network Conditions) and reload the page.

---

### Step 2 — Creare la VM / Create the VM

**IT** — Questi passaggi valgono sia per VirtualBox che per VMware:

**EN** — These steps apply to both VirtualBox and VMware:

1. **Crea una nuova VM / Create a new VM** — Seleziona "Windows 10 (64-bit)" come tipo di OS guest.
2. **RAM** — Assegna almeno 4 GB (meglio 8 GB). / Assign at least 4 GB (8 GB recommended).
3. **Disco virtuale / Virtual disk** — Crea un disco da almeno 60 GB (dinamico va bene). / Create a disk of at least 60 GB (dynamically allocated is fine).
4. **Rete / Network** — Lascia NAT per l'installazione; potrai passare a Host-Only dopo. / Use NAT for installation; you can switch to Host-Only later.
5. **Installa Windows / Install Windows** — Monta l'ISO e segui l'installazione standard. Scegli l'edizione **Pro** se disponibile (include utili feature come Hyper-V e GPO). / Mount the ISO and follow the standard installation. Choose the **Pro** edition if available (it includes useful features like Hyper-V and GPO).
6. **Disabilita Windows Update / Disable Windows Update** — Dopo l'installazione, disabilita gli aggiornamenti automatici (almeno fino al termine del setup di FLARE-VM). / After installation, disable automatic updates (at least until FLARE-VM setup is complete).

> **IT** — Installa le Guest Additions (VirtualBox) o i VMware Tools per avere clipboard condivisa, drag-and-drop e risoluzione dinamica.
>
> **EN** — Install Guest Additions (VirtualBox) or VMware Tools for shared clipboard, drag-and-drop, and dynamic resolution.

---

### Step 3 — Disattivare Windows Defender / Disable Windows Defender

**IT** — FLARE-VM richiede che Windows Defender (e la Tamper Protection) siano completamente disattivati. L'antivirus interferisce con molti tool di analisi malware.

**EN** — FLARE-VM requires Windows Defender (and Tamper Protection) to be fully disabled. The antivirus interferes with many malware analysis tools.

#### Opzione consigliata / Recommended option: Windows Defender Remover

Usa il tool automatizzato **Windows Defender Remover** di ionuttbara:

**Repo:** <https://github.com/ionuttbara/windows-defender-remover>

**IT — Come usarlo:**

1. Nella VM, apri il browser e vai alla pagina [Releases](https://github.com/ionuttbara/windows-defender-remover/releases) del progetto.
2. Scarica il **Source Code (.zip)** dell'ultima versione.
3. Estrai l'archivio in una cartella.
4. **Disabilita manualmente la Tamper Protection** prima di procedere: `Impostazioni → Sicurezza di Windows → Protezione da virus e minacce → Gestisci impostazioni → Protezione antimanomissione → OFF`.
5. Esegui `Script_Run.bat` come **Amministratore**.
6. Alla domanda, scegli **Y** per rimuovere completamente Defender + Windows Security App.
7. Il sistema si riavvierà. Dopo il riavvio, verifica che Defender sia effettivamente rimosso (l'icona scudo nella tray non dovrebbe più comparire).

**EN — How to use it:**

1. Inside the VM, open the browser and go to the project's [Releases](https://github.com/ionuttbara/windows-defender-remover/releases) page.
2. Download the **Source Code (.zip)** of the latest version.
3. Extract the archive into a folder.
4. **Manually disable Tamper Protection** before proceeding: `Settings → Windows Security → Virus & threat protection → Manage settings → Tamper Protection → OFF`.
5. Run `Script_Run.bat` as **Administrator**.
6. When prompted, choose **Y** to fully remove Defender + Windows Security App.
7. The system will reboot. After reboot, verify Defender is actually removed (the shield icon in the tray should no longer appear).

> **IT** — Si consiglia di creare un punto di ripristino prima di eseguire lo script.
>
> **EN** — It is recommended to create a system restore point before running the script.

#### Opzioni alternative / Alternative options

- **GPO (Group Policy):** solo per edizioni Pro/Enterprise — disattiva Defender tramite `gpedit.msc`. Vedi: [SuperUser — disable Defender via GPO](https://superuser.com/a/1757341)
- **Script semi-automatico / Semi-automated script:** [ToggleDefender.ps1](https://github.com/AveYo/LeanAndMean/blob/main/ToggleDefender.ps1) — richiede di disattivare manualmente la Tamper Protection / requires manually disabling Tamper Protection first.

---

### Step 4 — Installare FLARE-VM / Install FLARE-VM

**IT** — Una volta che Defender è stato rimosso con certezza e il sistema è stato riavviato, esegui il seguente comando in una finestra **PowerShell** aperta come **Amministratore**:

**EN** — Once Defender has been confirmed removed and the system has been rebooted, run the following command in a **PowerShell** window opened as **Administrator**:

```powershell
(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/mandiant/flare-vm/master/install.ps1') | Set-Content -Path "$env:USERPROFILE\Desktop\install.ps1"; Unblock-File "$env:USERPROFILE\Desktop\install.ps1"; & "$env:USERPROFILE\Desktop\install.ps1"
```

**IT — Cosa fa questo comando:**

1. Scarica lo script `install.ps1` dal repository ufficiale FLARE-VM.
2. Lo salva sul Desktop dell'utente corrente.
3. Lo sblocca (rimuove il flag "scaricato da internet").
4. Lo esegue immediatamente.

**EN — What this command does:**

1. Downloads the `install.ps1` script from the official FLARE-VM repository.
2. Saves it to the current user's Desktop.
3. Unblocks it (removes the "downloaded from internet" flag).
4. Executes it immediately.

> **IT** — L'installazione richiede tempo (30–90 minuti a seconda della connessione). La VM si riavvierà più volte automaticamente. Non interrompere il processo.
>
> **EN** — The installation takes time (30–90 minutes depending on your connection). The VM will reboot multiple times automatically. Do not interrupt the process.

Se l'execution policy blocca lo script, esegui prima / If the execution policy blocks the script, run first:

```powershell
Set-ExecutionPolicy Unrestricted -Force
```

---

### Step 5 — Post-installazione / Post-install

**IT:**

1. **Snapshot** — Crea uno snapshot/clone della VM appena completata l'installazione. Sarà il tuo stato "pulito" a cui tornare dopo ogni analisi.
2. **Rete** — Passa la scheda di rete a **Host-Only** o **Internal Network** per isolare la VM durante le analisi.
3. **Shared Folders** — Configura una cartella condivisa per trasferire campioni dall'host alla VM.
4. **Aggiorna i tool** — Apri una PowerShell da amministratore e lancia `cup all -y` (Chocolatey Update) per aggiornare tutti i pacchetti installati.

**EN:**

1. **Snapshot** — Take a snapshot/clone of the VM right after installation completes. This will be your "clean" state to revert to after each analysis.
2. **Network** — Switch the network adapter to **Host-Only** or **Internal Network** to isolate the VM during analysis.
3. **Shared Folders** — Set up a shared folder to transfer samples from host to VM.
4. **Update tools** — Open an admin PowerShell and run `cup all -y` (Chocolatey Update) to update all installed packages.

---

## Metodo 2 — OVA pronta (alternativa rapida) / Method 2 — Pre-built OVA (quick alternative)

**IT** — Se preferisci evitare l'installazione manuale, puoi scaricare un'immagine OVA già pronta per VirtualBox o VMware dalla community Vagrant di HashiCorp:

**EN** — If you prefer to skip the manual installation, you can download a pre-built OVA image ready for VirtualBox or VMware from the HashiCorp Vagrant community:

**Link:** <https://portal.cloud.hashicorp.com/vagrant/discover/figment/>

**IT — Come usarla:**

1. Visita il link e scegli l'immagine per il tuo hypervisor (VirtualBox o VMware).
2. Scarica il file `.ova` o il Vagrant box.
3. **VirtualBox:** `File → Importa Appliance → seleziona il file .ova`.
4. **VMware:** `File → Open → seleziona il file .ova`.
5. Avvia la VM e verifica che tutto funzioni correttamente.
6. Crea uno snapshot prima di iniziare qualsiasi analisi.

**EN — How to use it:**

1. Visit the link and choose the image for your hypervisor (VirtualBox or VMware).
2. Download the `.ova` file or the Vagrant box.
3. **VirtualBox:** `File → Import Appliance → select the .ova file`.
4. **VMware:** `File → Open → select the .ova file`.
5. Start the VM and verify everything works correctly.
6. Take a snapshot before starting any analysis.

> **IT** — Le OVA della community potrebbero non essere sempre aggiornate all'ultima versione di FLARE-VM. Dopo l'importazione, considera di aggiornare i tool con `cup all -y`.
>
> **EN** — Community OVAs may not always be up to date with the latest FLARE-VM version. After importing, consider updating tools with `cup all -y`.

---

## Fonti / Sources

| Risorsa / Resource | Link |
|---|---|
| **FLARE-VM** — Repository ufficiale / Official repository | <https://github.com/mandiant/flare-vm> |
| **Windows 10 ISO** — Download ufficiale Microsoft / Official Microsoft download | <https://www.microsoft.com/software-download/windows10> |
| **Windows Defender Remover** — Tool di rimozione automatica / Automated removal tool | <https://github.com/ionuttbara/windows-defender-remover> |
| **ToggleDefender.ps1** — Script semi-automatico / Semi-automated script | <https://github.com/AveYo/LeanAndMean/blob/main/ToggleDefender.ps1> |
| **Disabilitare Defender via GPO / Disable Defender via GPO** | <https://superuser.com/a/1757341> |
| **Disabilitare Windows Update / Disable Windows Update** | <https://www.windowscentral.com/how-stop-updates-installing-automatically-windows-10> |
| **OVA pre-costruite / Pre-built OVAs (Vagrant)** | <https://portal.cloud.hashicorp.com/vagrant/discover/figment/> |
| **Video tutorial FLARE-VM** (YouTube) | <https://www.youtube.com/watch?v=i8dCyy8WMKY> |

---

> **IT** — Questa guida è fornita a scopo educativo per attività di cybersecurity, reverse engineering e malware analysis in ambienti controllati. Utilizzala in modo responsabile.
>
> **EN** — This guide is provided for educational purposes for cybersecurity, reverse engineering, and malware analysis activities in controlled environments. Use responsibly.
