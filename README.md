<div align="center">

# 🔥 FLARE-VM — Setup Guide

**Reverse Engineering & Malware Analysis Environment**

[![FLARE-VM](https://img.shields.io/badge/FLARE--VM-by%20Mandiant-orange?style=for-the-badge&logo=google&logoColor=white)](https://github.com/mandiant/flare-vm)
[![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)](https://www.virtualbox.org/)
[![VMware](https://img.shields.io/badge/VMware-607078?style=for-the-badge&logo=vmware&logoColor=white)](https://www.vmware.com/)
[![Windows 10](https://img.shields.io/badge/Windows%2010-0078D6?style=for-the-badge&logo=windows&logoColor=white)](https://www.microsoft.com/software-download/windows10)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue?style=for-the-badge)](https://github.com/mandiant/flare-vm/blob/main/LICENSE.txt)

</div>

---

<details>
<summary>🇮🇹 <strong>Leggi in Italiano</strong></summary>

## Indice

- [Requisiti](#-requisiti)
- [Metodo 1 — Installazione da ISO](#️-metodo-1--installazione-da-iso)
- [Metodo 2 — OVA pre-costruita (alternativa rapida)](#-metodo-2--ova-pre-costruita-alternativa-rapida)
- [Fonti](#-fonti)

---

## 📋 Requisiti

| Componente | Minimo | Consigliato |
|:---|:---|:---|
| **OS Guest** | Windows 10 (22H2) | Windows 10 Pro (22H2) |
| **RAM** | 4 GB | 8 GB |
| **Disco** | 60 GB | 80 GB+ (SSD consigliato) |
| **PowerShell** | ≥ 5 | — |
| **Internet** | ✅ Necessaria durante l'installazione | — |
| **Hypervisor** | VirtualBox **o** VMware | — |

> [!WARNING]
> Il nome utente Windows **non deve** contenere spazi o caratteri speciali. Questo può causare errori durante l'installazione di FLARE-VM.

---

## 🛠️ Metodo 1 — Installazione da ISO

### Step 1 · Download Windows 10 ISO

Scarica l'ISO ufficiale di Windows 10 dal sito Microsoft:

🔗 **https://www.microsoft.com/software-download/windows10**

> [!TIP]
> Se visiti la pagina da un PC Windows, il sito propone il **Media Creation Tool** invece del download diretto.
> Per aggirarlo:
> 1. Apri **DevTools** nel browser (`F12`)
> 2. Vai su **Network Conditions** (o "Condizioni di rete")
> 3. Cambia lo **User Agent** in `Chrome — Android`
> 4. Ricarica la pagina con `Ctrl + F5`
>
> Ora vedrai il menu per scaricare direttamente l'ISO.
>
> In caso contrario, si costruisca una ISO con il media creation tool

---

### Step 2 · Creare la VM

Questi passaggi sono validi sia per **VirtualBox** che per **VMware** ma possono differire in parte:

| # | Azione | Dettagli |
|:---:|:---|:---|
| 1 | **Crea una nuova VM** | Seleziona `Windows 10 (64-bit)` come OS guest |
| 2 | **RAM** | Assegna almeno 4 GB (meglio 8 GB) + 2/3 processori o core |
| 3 | **Disco virtuale** | Crea un disco da almeno 80 GB — dinamico va bene |
| 4 | **Rete** | Usa **NAT** per l'installazione (cambierai dopo) |
| 5 | **Installa Windows** | Monta l'ISO e segui il setup. Scegli **Pro** se possibile |
| 6 | **Aggiorna Windows** | Dopo l'installazione, ESEGUI TUTTI GLI AGGIORNAMENTI <- Mandatorio |

> [!NOTE]
> Installa le **Guest Additions** (VirtualBox - Potenzialmente già installate) -> [Guest Addition](https://www.virtualbox.org/manual/topics/guestadditions.html)
> 
> Installa i **VMware Tools** per clipboard condivisa, drag-and-drop e risoluzione dinamica -> Click su "VM" nella barra strumenti in alto, nel menu' a tendina seleziona "Install VMware tools"

---

### Step 3 · Disattivare Windows Defender

FLARE-VM richiede che Windows Defender e la Tamper Protection siano **completamente disattivati**. L'antivirus interferisce con i tool di analisi malware e può bloccare l'installazione.

#### ✅ Opzione consigliata: Windows Defender Remover - Autonoma

🔗 **https://github.com/ionuttbara/windows-defender-remover**

**Procedura:**

> [!CAUTION]
> Crea un **punto di ripristino** prima di eseguire lo script. La rimozione di Defender è permanente e difficile da revertire.

1. Nella VM, vai alla pagina [**Download**](https://github.com/ionuttbara/windows-defender-remover) del progetto
2. Scendi e scarica il **Source Code (.zip)** oppure il **l'eseguibile (.exe)** dell'ultima versione
3. **Disabilita manualmente la Tamper Protection:**
   ```
   Impostazioni → Sicurezza di Windows → Protezione da virus e minacce
   → Gestisci impostazioni → Protezione antimanomissione → OFF
   ```
4. Estrai l'archivio in una cartella
5. Esegui il remover come **Amministratore**
6. Alla domanda, scegli **`Y`** — rimozione completa di Defender + Windows Security App
7. Il sistema si riavvierà. Verifica che l'icona scudo nella tray sia sparita
8. Al riavvio, o a fine installazione finale, eseguire i seguenti script per bloccare aggiornamenti futuri da windows
```Powershell
Add-Content "$env:windir\System32\drivers\etc\hosts" "`n127.0.0.1 sls.microsoft.com" 
Add-Content "$env:windir\System32\drivers\etc\hosts" "`n127.0.0.1 kms.microsoft.com" 
Add-Content "$env:windir\System32\drivers\etc\hosts" "`n127.0.0.1 activation.sls.microsoft.com"
```

#### Opzioni alternative - Manuali

| Metodo | Requisiti | Link |
|:---|:---|:---|
| **GPO (Group Policy)** | Solo edizioni Pro/Enterprise | [SuperUser — GPO](https://superuser.com/a/1757341) |
| **ToggleDefender.ps1** | Disattivare prima la Tamper Protection | [AveYo/LeanAndMean](https://github.com/AveYo/LeanAndMean/blob/main/ToggleDefender.ps1) |

---

### Step 4 · Installare FLARE-VM

Una volta che Defender è stato **rimosso con certezza** e il sistema è stato riavviato:

1. Apri **PowerShell** come **Amministratore**
2. Se necessario, sblocca l'execution policy:
   ```powershell
   Set-ExecutionPolicy Unrestricted -Force
   ```
3. Esegui il comando di installazione:

```powershell
(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/mandiant/flare-vm/master/install.ps1') | Set-Content -Path "$env:USERPROFILE\Desktop\install.ps1"; Unblock-File "$env:USERPROFILE\Desktop\install.ps1"; & "$env:USERPROFILE\Desktop\install.ps1"
```
4. Quando verrà richiesto di inserire una **PASSWORD** bisognerà inserire la password di accesso all'utente
5. Verrà chiesto quali plugin installare. Lasciare quelli presenti e in fondo nel form cercare e aggiungere i seguenti (Scrivendoli singolarmente e cercandoli): **cutter** - **fiddler** - **Capa** - **dnspyE** 

<details>
<summary>📖 Cosa fa questo comando?</summary>

| Azione | Descrizione |
|:---|:---|
| `DownloadString(...)` | Scarica `install.ps1` dal repo ufficiale FLARE-VM |
| `Set-Content -Path ...` | Salva lo script sul Desktop |
| `Unblock-File` | Rimuove il flag "scaricato da internet" |
| `& "...\install.ps1"` | Esegue lo script immediatamente |

</details>

> [!IMPORTANT]
> L'installazione richiede **FINO A 4/5 ORE** a seconda della connessione e della macchina. La VM si riavvierà più volte automaticamente. **Non interrompere il processo.**

---

### Step 5 · Post-installazione

| # | Azione | Dettagli |
|:---:|:---|:---|
| 1 | 📸 **Snapshot** | Crea uno snapshot subito dopo il completamento — sarà il tuo stato "pulito" |
| 2 | 🔒 **Rete** | Passa a **Host-Only** o **Rete-interna** per isolare la VM durante le analisi |
| 3 | 🔄 **Aggiorna i tool** | Apri PowerShell (admin) → `cup all -y` per aggiornare i pacchetti Chocolatey |
| 4 | 📂 **Scaricare la repo con i Malware** | Eseguendo il codice del professore disponbile nelle slide o di seguito, per poi runnarlo con PowerShell
```powershell
(New-Object net.webclient).DownloadFile('https://raw.githubusercontent.com/Akir4d/utility/refs/heads/main/malwareRepo.ps1',"$([Environment]::GetFolderPath("Desktop"))\malwareRepo.ps1")
```

---

## 📦 Metodo 2 — OVA pre-costruita (alternativa rapida)

Se preferisci evitare l'installazione manuale, puoi scaricare un'immagine OVA già pronta:

🔗 **https://portal.cloud.hashicorp.com/vagrant/discover/figment/**

**Procedura:**

| # | Azione |
|:---:|:---|
| 1 | Visita il link e scegli l'immagine per il tuo hypervisor |
| 2 | Scarica il file `.ova` o il Vagrant box |
| 3 | **VirtualBox:** `File → Importa Appliance → seleziona il .ova` |
| 4 | **VMware:** `File → Open → seleziona il .ova` |
| 5 | Avvia la VM e verifica che tutto funzioni |
| 6 | Crea uno **snapshot** prima di iniziare qualsiasi analisi |

> [!NOTE]
> Le OVA della community potrebbero non essere aggiornate all'ultima versione di FLARE-VM. Dopo l'importazione, aggiorna i tool con `cup all -y`.

---

## 📚 Fonti

| Risorsa | Link |
|:---|:---|
| Un grazie doveroso ai colleghi **Cherubini**, **Pinto** e **Ruocco** per il contributo | 🔥 |
| FLARE-VM — Repository ufficiale | https://github.com/mandiant/flare-vm |
| Windows 10 ISO — Download Microsoft | https://www.microsoft.com/software-download/windows10 |
| Windows Defender Remover | https://github.com/ionuttbara/windows-defender-remover |
| ToggleDefender.ps1 | https://github.com/AveYo/LeanAndMean/blob/main/ToggleDefender.ps1 |
| Disabilitare Defender via GPO | https://superuser.com/a/1757341 |
| Disabilitare Windows Update | https://www.windowscentral.com/how-stop-updates-installing-automatically-windows-10 |
| OVA pre-costruite (Vagrant) | https://portal.cloud.hashicorp.com/vagrant/discover/figment/ |
| Video tutorial FLARE-VM | https://www.youtube.com/watch?v=i8dCyy8WMKY |

---

> ⚠️ Questa guida è fornita a **scopo educativo** per attività di cybersecurity, reverse engineering e malware analysis in ambienti controllati. Utilizzala in modo responsabile.

</details>

---

<details open>
<summary>🇬🇧 <strong>Read in English</strong></summary>

## Table of Contents

- [Requirements](#-requirements)
- [Method 1 — Install from ISO](#️-method-1--install-from-iso)
- [Method 2 — Pre-built OVA (quick alternative)](#-method-2--pre-built-ova-quick-alternative)
- [Sources](#-sources)

---

## 📋 Requirements

| Component | Minimum | Recommended |
|:---|:---|:---|
| **Guest OS** | Windows 10 (22H2) | Windows 10 Pro (22H2) |
| **RAM** | 4 GB | 8 GB |
| **Disk** | 60 GB | 80 GB+ (SSD recommended) |
| **PowerShell** | ≥ 5 | — |
| **Internet** | ✅ Required during installation | — |
| **Hypervisor** | VirtualBox **or** VMware | — |

> [!WARNING]
> The Windows username **must not** contain spaces or special characters. This can cause errors during FLARE-VM installation.

---

## 🛠️ Method 1 — Install from ISO

### Step 1 · Download Windows 10 ISO

Download the official Windows 10 ISO from Microsoft:

🔗 **https://www.microsoft.com/software-download/windows10**

> [!TIP]
> If you visit the page from a Windows PC, the site pushes the **Media Creation Tool** instead of a direct download.
> To bypass it:
> 1. Open **DevTools** in your browser (`F12`)
> 2. Go to **Network Conditions**
> 3. Change the **User Agent** to `Chrome — Android`
> 4. Reload the page with `Ctrl + F5`
>
> You'll now see the dropdown to download the ISO directly.

---

### Step 2 · Create the VM

These steps apply to both **VirtualBox** and **VMware**:

| # | Action | Details |
|:---:|:---|:---|
| 1 | **Create a new VM** | Select `Windows 10 (64-bit)` as the guest OS |
| 2 | **RAM** | Assign at least 4 GB (8 GB recommended) |
| 3 | **Virtual disk** | Create a disk of at least 60 GB — dynamically allocated is fine |
| 4 | **Network** | Use **NAT** for installation (you'll change this later) |
| 5 | **Install Windows** | Mount the ISO and follow the setup. Choose **Pro** if possible |
| 6 | **Disable Windows Update** | After installation, disable automatic updates |

> [!NOTE]
> Install **Guest Additions** (VirtualBox) or **VMware Tools** for shared clipboard, drag-and-drop, and dynamic resolution.

---

### Step 3 · Disable Windows Defender

FLARE-VM requires Windows Defender and Tamper Protection to be **fully disabled**. The antivirus interferes with malware analysis tools and can block installation.

#### ✅ Recommended: Windows Defender Remover

🔗 **https://github.com/ionuttbara/windows-defender-remover**

**Procedure:**

1. Inside the VM, go to the project's [**Releases**](https://github.com/ionuttbara/windows-defender-remover/releases) page
2. Download the **Source Code (.zip)** of the latest version
3. Extract the archive into a folder
4. **Manually disable Tamper Protection:**
   ```
   Settings → Windows Security → Virus & threat protection
   → Manage settings → Tamper Protection → OFF
   ```
5. Run `Script_Run.bat` as **Administrator**
6. When prompted, choose **`Y`** — full removal of Defender + Windows Security App
7. The system will reboot. Verify the shield icon in the tray is gone

> [!CAUTION]
> Create a **system restore point** before running the script. Defender removal is permanent and difficult to revert.

#### Alternative options

| Method | Requirements | Link |
|:---|:---|:---|
| **GPO (Group Policy)** | Pro/Enterprise editions only | [SuperUser — GPO](https://superuser.com/a/1757341) |
| **ToggleDefender.ps1** | Disable Tamper Protection first | [AveYo/LeanAndMean](https://github.com/AveYo/LeanAndMean/blob/main/ToggleDefender.ps1) |

---

### Step 4 · Install FLARE-VM

Once Defender has been **confirmed removed** and the system has been rebooted:

1. Open **PowerShell** as **Administrator**
2. If needed, unblock the execution policy:
   ```powershell
   Set-ExecutionPolicy Unrestricted -Force
   ```
3. Run the installation command:

```powershell
(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/mandiant/flare-vm/master/install.ps1') | Set-Content -Path "$env:USERPROFILE\Desktop\install.ps1"; Unblock-File "$env:USERPROFILE\Desktop\install.ps1"; & "$env:USERPROFILE\Desktop\install.ps1"
```

<details>
<summary>📖 What does this command do?</summary>

| Action | Description |
|:---|:---|
| `DownloadString(...)` | Downloads `install.ps1` from the official FLARE-VM repo |
| `Set-Content -Path ...` | Saves the script to the Desktop |
| `Unblock-File` | Removes the "downloaded from internet" flag |
| `& "...\install.ps1"` | Executes the script immediately |

</details>

> [!IMPORTANT]
> Installation takes **30–90 minutes** depending on your connection. The VM will reboot multiple times automatically. **Do not interrupt the process.**

---

### Step 5 · Post-install

| # | Action | Details |
|:---:|:---|:---|
| 1 | 📸 **Snapshot** | Take a snapshot right after completion — this is your "clean" state |
| 2 | 🔒 **Network** | Switch to **Host-Only** or **Internal Network** to isolate the VM during analysis |
| 3 | 📂 **Shared Folders** | Set up a shared folder to transfer samples from host → VM |
| 4 | 🔄 **Update tools** | Open PowerShell (admin) → `cup all -y` to update all Chocolatey packages |

---

## 📦 Method 2 — Pre-built OVA (quick alternative)

If you prefer to skip the manual installation, download a pre-built OVA image:

🔗 **https://portal.cloud.hashicorp.com/vagrant/discover/figment/**

**Procedure:**

| # | Action |
|:---:|:---|
| 1 | Visit the link and choose the image for your hypervisor |
| 2 | Download the `.ova` file or Vagrant box |
| 3 | **VirtualBox:** `File → Import Appliance → select the .ova` |
| 4 | **VMware:** `File → Open → select the .ova` |
| 5 | Start the VM and verify everything works |
| 6 | Take a **snapshot** before starting any analysis |

> [!NOTE]
> Community OVAs may not always be up to date with the latest FLARE-VM version. After importing, update tools with `cup all -y`.

---

## 📚 Sources

| Resource | Link |
|:---|:---|
| FLARE-VM — Official repository | https://github.com/mandiant/flare-vm |
| Windows 10 ISO — Microsoft download | https://www.microsoft.com/software-download/windows10 |
| Windows Defender Remover | https://github.com/ionuttbara/windows-defender-remover |
| ToggleDefender.ps1 | https://github.com/AveYo/LeanAndMean/blob/main/ToggleDefender.ps1 |
| Disable Defender via GPO | https://superuser.com/a/1757341 |
| Disable Windows Update | https://www.windowscentral.com/how-stop-updates-installing-automatically-windows-10 |
| Pre-built OVAs (Vagrant) | https://portal.cloud.hashicorp.com/vagrant/discover/figment/ |
| FLARE-VM video tutorial | https://www.youtube.com/watch?v=i8dCyy8WMKY |

---

> ⚠️ This guide is provided for **educational purposes** for cybersecurity, reverse engineering, and malware analysis activities in controlled environments. Use responsibly.

</details>
