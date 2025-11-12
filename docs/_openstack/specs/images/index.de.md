---
title: Images
lang: "de"
permalink: /openstack/specs/images/
parent: Spezifikationen
nav_order: 9500
---

# Images

Es gibt 4 Arten von Images in OpenStack:

- **Public Images:** Diese Images werden von uns gepflegt, sind für alle Benutzer verfügbar, werden regelmäßig aktualisiert und zur Verwendung empfohlen.
- **Community Images:** Ehemals öffentliche Images, die durch neuere Versionen ersetzt wurden. Wir behalten diese Images, bis sie nicht mehr verwendet werden, um Ihre Deployments nicht zu gefährden.
- **Private Images:** Von Ihnen hochgeladene Images, die nur für Ihr Projekt verfügbar sind.
- **Shared Images:** Private Images, die entweder durch Sie oder oder mit Ihnen in mehreren Projekten gemeinsam genutzt werden.

Nur die ersten beiden Typen werden von uns verwaltet.

## Public und community images

Um Ihren Aufwand so gering wie möglich zu halten, stellen wir Ihnen eine Reihe ausgewählter Images zur Verfügung.

Aktuell enthält diese Liste:

- Ubuntu 24.04 LTS (Noble Numbat)
- Ubuntu Minimal 24.04 LTS (Noble Numbat)
- Ubuntu 22.04 LTS (Jammy Jellyfish)
- Ubuntu Minimal 22.04 LTS (Jammy Jellyfish)
- Debian 12 (Bookworm)
- Debian 11 (Bullseye)
- CoreOS (stable)
- Rocky Linux 9
- Flatcar Linux

Diese Images werden täglich auf neue Versionen überprüft. Die neueste verfügbare Version ist immer ein "public image" und endet auf `Latest`. Alle vorherigen Versionen eines Images werden durch unseren Automatismus in "community images" umgewandelt, umbenannt (`latest` wird durch das Datum des ersten Uploads ersetzt), und bei ausbleibender Verwendung (keinerlei Nutzung) schlussendlich gelöscht.

OpenStack und viele Deployment-Tools unterstützen die Verwendung dieser Images entweder über den Namen oder über ihre UUID. Durch die Verwendung eines Namens, z.B. `Ubuntu 24.04 Noble Numbat- Latest`, Erhalten sie jeweils die aktuellste Version des jeweiligen Images, indem Sie Ihre Instanzen neu bereitstellen oder neu aufbauen, selbst wenn wir das Image zwischendurch ersetzen. Sie können dieses Verhalten vermeiden, indem Sie stattdessen die UUID verwenden. Dies kann für Cluster-Einsätze nützlich sein, bei denen Sie sicherstellen wollen, dass auf allen Instanzen die gleiche Version des Images läuft.

## Linux Images

Alle von uns zur Verfügung gestellten Linux-Images sind unmodifiziert und kommen direkt von ihren offiziellen Maintainern. Wir testen sie während des Upload-Prozesses auf Kompatibilität.

## Windows-Images

Wir stellen folgende Windows-Images zur Verfügung:

- Windows Server 2022 GUI
- Windows Server 2025 GUI

### Verwendung von Windows-Images:

Windows-Images unterstützen das automatische Zurücksetzen des Administrator-Passworts über Instanz-Metadaten:

- Fügen Sie den Metadaten-Schlüssel `admin_pass` mit einem gewünschten Passwort hinzu.
- Nach dem Neustart der Instanz wird das Administrator-Passwort auf den angegebenen Wert gesetzt.

{: .warning }

Entfernen Sie die Metadaten nach der Erstkonfiguration. Die Metadaten sind **nicht verschlüsselt** und sollten nur für die initiale Passwortvergabe verwendet werden.

Beispiel:

```
openstack server set --property admin_pass='MeinSicheresPasswort123' INSTANCE_NAME
```

- Das Passwort wird nach dem nächsten Neustart angewendet.
- Entfernen Sie anschließend die Metadaten, um die Instanz zu sichern:

```
openstack server unset --property admin_pass INSTANCE_NAME
```

Alternativ können Sie sich über SSH und die Floating IP verbinden und das Administrator-Passwort direkt innerhalb der Instanz setzen:

```
ssh $FLOATING_IP -l Administrator
```

{: .note }

> Wenn Sie beim Verbindungsaufbau per SSH zur Eingabe eines Passworts aufgefordert werden, warten Sie bitte noch einen Moment. Zu diesem Zeitpunkt sind die Hintergrundprozesse der Instanz möglicherweise noch nicht vollständig abgeschlossen.  
>
> Das zu setzende Passwort muss den Standard-Komplexitätsregeln von Windows Server entsprechen. Es muss Zeichen aus mindestens drei der folgenden vier Kategorien enthalten:
>
> - Großbuchstaben (A–Z)  
> - Kleinbuchstaben (a–z)  
> - Ziffern (0–9)  
> - Sonderzeichen (z. B. !, $, #, %)  
>
> Weitere Informationen finden Sie in der [Microsoft-Dokumentation](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh994562(v=ws.11))

```
administrator@win-server C:\Users\Administrator> powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Users\Administrator> $Password = Read-Host -AsSecureString
**********
PS C:\Users\Administrator> Set-LocalUser -Name Administrator -Password $Password
```

Diese Methode vermeidet, dass das Passwort in unverschlüsselten Metadaten gespeichert wird.

## Eigene Images in OpenStack hochladen

Sie können eigene Images hochladen, anstatt die bereitgestellten zu verwenden. Die einfachste Methode ist über die OpenStack CLI.

```bash
openstack image create \
  --property hw_disk_bus=scsi \          # Art des Disk-Busses
  --property hw_qemu_guest_agent=True \  # Ermöglicht Snapshots laufender VMs
  --property hw_scsi_model=virtio-scsi \ # SCSI-Modell
  --property os_require_quiesce=True \   # Erfordert Quiesce für Snapshots
  --private \                             # Image privat halten
  --disk-format qcow2 \                   # Image-Format
  --container-format bare \               # Container-Format
  --file ~/mein-image.qcow2 \             # Quelle des Images auf dem lokalen Rechner
  mein-image                              # Name des Images
```

**Mindestens erforderliche Felder für `openstack image create`:**

- `--disk-format`: z.B. qcow2; hängt vom Image-Format ab.
- `--file`: Pfad zur Quelldatei des Images auf Ihrem Rechner.
- Name des Images: z.B. `mein-image`.

Um Snapshots laufender Instanzen zu erstellen, empfehlen wir, beim Erstellen des Images `--property hw_qemu_guest_agent=True` zu setzen und den `qemu-guest-agent` im Image zu installieren. Weitere Details finden Sie in unserer [FAQ](https://docs.wiit-cloud.io/de/openstack/faq/#warum-kann-ich-keinen-snapshot-einer-laufenden-instance-erstellen).

### UEFI-Images

Wenn Ihr Image einen UEFI-Boot benötigt (z.B. moderne Windows-Versionen oder einige Linux-Distributionen), müssen beim Erstellen des Images folgende Eigenschaften gesetzt werden:

```
--property hw_machine_type='q35' \
--property hw_firmware_type='uefi'
```

Diese Einstellungen stellen sicher, dass die VM den Maschinentyp Q35 und die UEFI-Firmware anstelle des Legacy-BIOS verwendet.

Sie können Images auch über das OpenStack Dashboard hochladen. Stellen Sie sicher, dass Sie dort die gleichen Eigenschaften wie in der CLI setzen.
