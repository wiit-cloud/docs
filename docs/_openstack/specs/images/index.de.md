---
title: Images
lang: "de"
permalink: /openstack/specs/images/
parent: Spezifikationen
nav_order: 9500
---

# Images

Es gibt vier Arten von Images in OpenStack:

- **Public Images:** Diese Images werden von uns gepflegt, sind für alle Benutzer verfügbar, werden regelmäßig aktualisiert und zur Verwendung empfohlen.
- **Community Images:** Ehemals öffentliche Images, die durch neuere Versionen ersetzt wurden. Wir behalten diese Images, bis sie nicht mehr verwendet werden, um Ihre Deployments nicht zu gefährden.
- **Private Images:** Von Ihnen hochgeladene Images, die nur für Ihr Projekt verfügbar sind.
- **Shared Images:** Private Images, die entweder durch Sie oder mit Ihnen in mehreren Projekten gemeinsam genutzt werden.

Nur die ersten beiden Typen werden von uns verwaltet.

## Public und Community Images

Um Ihren Aufwand so gering wie möglich zu halten, stellen wir Ihnen eine Reihe ausgewählter Images zur Verfügung.

Aktuell enthält diese Liste:

- Ubuntu 26.04 LTS (Resolute Raccoon)
- Ubuntu Minimal 26.04 LTS (Resolute Raccoon)
- Ubuntu 24.04 LTS (Noble Numbat)
- Ubuntu Minimal 24.04 LTS (Noble Numbat)
- Ubuntu 22.04 LTS (Jammy Jellyfish)
- Ubuntu Minimal 22.04 LTS (Jammy Jellyfish)
- Debian 13 (Trixie)
- Debian 12 (Bookworm)
- Rocky Linux 10 (Red Quartz)
- Rocky Linux 9 (Blue Onyx)
- Flatcar Linux

Diese Images werden täglich auf neue Versionen überprüft. Die neueste verfügbare Version ist immer ein "public image" und endet auf `Latest`. Alle vorherigen Versionen eines Images werden durch unseren Automatismus in "community images" umgewandelt, umbenannt (`latest` wird durch das Datum des ersten Uploads ersetzt) und bei ausbleibender Verwendung (keinerlei Nutzung) schlussendlich gelöscht.

OpenStack und viele Deployment-Tools unterstützen die Verwendung dieser Images entweder über den Namen oder über ihre UUID. Durch die Verwendung eines Namens, z. B. `Ubuntu 24.04 Noble Numbat- Latest`, erhalten Sie jeweils die aktuellste Version des jeweiligen Images, indem Sie Ihre Instanzen neu bereitstellen oder neu aufbauen, selbst wenn wir das Image zwischendurch ersetzen. Sie können dieses Verhalten vermeiden, indem Sie stattdessen die UUID verwenden. Dies kann für Cluster-Einsätze nützlich sein, bei denen Sie sicherstellen wollen, dass auf allen Instanzen die gleiche Version des Images läuft.

## Linux Images

Alle von uns bereitgestellten Linux-Images sind unverändert und stammen direkt von den offiziellen Entwicklern. Wir testen sie während des Upload-Prozesses, um ihre Einsatzfähigkeit sicherzustellen. Diese Images werden ausschließlich zu Ihrer Bequemlichkeit und ohne Gewährleistung bereitgestellt. Bitte beachten Sie, dass wir keinen technischen Support, keine Wartung und keine Fehlerbehebung anbieten können. Laden Sie bitte Ihre eigenen, benutzerdefinierten Images hoch, falls Sie spezielle Anforderungen haben.

## Windows-Images

Wir stellen folgende Windows-Images zur Verfügung:

- Windows Server 2022 GUI
- Windows Server 2025 GUI

### Verwendung von Windows-Images:

{: .warning }

> Unsere Windows-Images funktionieren nur mit **RSA**-SSH-Key-Pairs (`ssh-rsa`). Wenn Sie eine Instanz mit einem anderen Schlüsseltyp wie ed25519 erstellen, schlägt die Initialisierung der Instanz durch cloudbase-init fehl.

{: .warning }

> Die Initialisierung unserer Windows-Images durch cloudbase-init funktioniert derzeit nicht bei Instanzen, die nur eine **IPv6**-Adresse haben. Stellen Sie sicher, dass die Instanz auch eine IPv4-Adresse hat.

Windows-Images unterstützen das Setzen des initialen Administrator-Passworts über Instanz-Metadaten:

- Fügen Sie beim Erstellen der Instanz den Metadaten-Schlüssel `admin_pass` mit dem gewünschten Passwort hinzu.
- Das Passwort wird einmalig beim ersten Start der Instanz gesetzt. Ein späteres Hinzufügen oder Ändern von `admin_pass` hat keine Auswirkung.

Beispiel:

```
openstack server create \
  --image IMAGE_NAME \
  --flavor FLAVOR_NAME \
  --network NETWORK_NAME \
  --key-name KEY_NAME \
  --property admin_pass='MeinSicheresPasswort123' \
  INSTANCE_NAME
```

{: .warning }

Die Metadaten sind **nicht verschlüsselt** und bleiben auch nach dem ersten Start lesbar. Entfernen Sie sie, sobald die Instanz läuft:

```
openstack server unset --property admin_pass INSTANCE_NAME
```

Da das über `admin_pass` gesetzte Passwort im Klartext in den Metadaten übertragen wird, verlangt Windows bei der ersten Anmeldung über die GUI-Konsole (z. B. die Konsole im OpenStack Dashboard), dass Sie es ändern. Eine Verbindung per RDP ist erst möglich, nachdem das initiale Passwort geändert wurde.

Wenn Sie die GUI-Konsole nicht verwenden möchten, können Sie sich alternativ über SSH und die Floating IP mit dem öffentlichen Schlüssel des der Instanz zugewiesenen Key Pairs (`--key-name`) verbinden und das initiale Passwort direkt innerhalb der Instanz ändern:

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

### Empfohlenes Vorgehen für Automatisierungstools

Wenn Sie Windows-Instanzen mit Automatisierungstools (z. B. Terraform oder Ansible) erstellen, empfehlen wir, `admin_pass` nicht zu setzen. In diesem Fall generiert cloudbase-init beim ersten Start ein zufälliges Passwort für das Administrator-Konto. Das Passwort wird mit dem öffentlichen Schlüssel des der Instanz zugewiesenen Key Pairs (`--key-name`) verschlüsselt und im Metadaten-Service gespeichert. Nur wer den passenden privaten Schlüssel besitzt, kann es entschlüsseln.

```
openstack server create \
  --image IMAGE_NAME \
  --flavor FLAVOR_NAME \
  --network NETWORK_NAME \
  --key-name KEY_NAME \
  INSTANCE_NAME
```

Da das generierte Passwort nie im Klartext sichtbar ist, ist keine Passwortänderung erforderlich: Sie können sich mit dem abgerufenen Passwort direkt per RDP verbinden.

Das verschlüsselte Passwort ist einige Minuten nach dem ersten Start verfügbar, sobald cloudbase-init abgeschlossen ist. Sie können es mit der veralteten nova-CLI (Teil von `python-novaclient`, benötigt `openssl` und einen privaten Schlüssel im PEM-Format) abrufen und entschlüsseln:

```
nova get-password INSTANCE_NAME ~/.ssh/id_rsa
```

Alternativ können Sie das Tool [nova-password](https://github.com/sapcc/nova-password) verwenden, das auch private Schlüssel im OpenSSH- und PuTTY-Format unterstützt:

```
nova-password --private-key-path ~/.ssh/id_rsa INSTANCE_NAME
```

Beide Tools verwenden die OpenStack-Zugangsdaten aus Ihrer Umgebung (z. B. aus einer geladenen `openrc`-Datei).

#### Passwort manuell entschlüsseln

Die Compute-API liefert das Passwort Base64-kodiert und mit RSA (PKCS #1 v1.5) verschlüsselt. Ersetzen Sie `COMPUTE_URL` durch den öffentlichen Compute-Endpunkt, den `openstack catalog show compute` anzeigt:

```
TOKEN=$(openstack token issue -f value -c id)
SERVER_ID=$(openstack server show INSTANCE_NAME -f value -c id)

curl -s -H "X-Auth-Token: $TOKEN" "COMPUTE_URL/servers/$SERVER_ID/os-server-password" \
  | jq -r .password \
  | base64 -d \
  | openssl pkeyutl -decrypt -inkey ~/.ssh/id_rsa
```

OpenSSL benötigt den privaten Schlüssel im PEM-Format. Liegt Ihr Schlüssel im OpenSSH-Format vor (`-----BEGIN OPENSSH PRIVATE KEY-----`), konvertieren Sie zuerst eine Kopie davon:

```
cp ~/.ssh/id_rsa ~/.ssh/id_rsa.pem
ssh-keygen -p -m PEM -f ~/.ssh/id_rsa.pem
```

#### Automatisierung

Für eine saubere Automatisierung empfehlen wir ein eigenes Skript, das direkt mit der Compute-API arbeitet:

1. Das verschlüsselte Passwort mit `GET /servers/{server_id}/os-server-password` abrufen.
2. Es mit dem privaten Schlüssel des Key Pairs entschlüsseln.
3. Es in einem Secrets-Management-Tool wie HashiCorp Vault ablegen.

Anschließend können Sie das verschlüsselte Passwort mit `DELETE /servers/{server_id}/os-server-password` (oder `nova clear-password INSTANCE_NAME`) aus dem Metadaten-Service löschen. Dadurch wird nur die gespeicherte Kopie entfernt; das Administrator-Passwort der Instanz bleibt unverändert.

## Upload von eigenen Images

Sie können jederzeit Ihre eigenen Images hochladen, anstatt die von uns bereitgestellten zu nutzen. Am einfachsten funktioniert das über die OpenStack-CLI.

```bash
openstack image create \
  --property hw_disk_bus=scsi \
  --property hw_qemu_guest_agent=True \
  --property hw_scsi_model=virtio-scsi \
  --property os_require_quiesce=True \
  --property hw_firmware_type=uefi \
  --property hw_machine_type=q35 \
  --private \
  --disk-format raw \
  --container-format bare \
  my-image

openstack image stage \
  --file ~/my-image.img \
  --progress \
  my-image

openstack image import \
  --method glance-direct \
  --all-stores \
  my-image
```

Dabei müssen mindestens folgende Parameter spezifiziert werden:

- `--disk-format`: Das Format Ihres Quell-Images, z. B. `raw`
- `--file`: Das Quell-Image auf Ihrem System
- Name des Images: `my-image` als Beispiel.

Wir empfehlen die Nutzung des RAW-Formats, hierdurch wird Copy-on-Write bei der Erstellung von Instanzen und Volumes möglich, was diesen Prozess deutlich beschleunigt.

Weiterhin empfehlen wir die Nutzung der oben genannten Befehle. Zwar ließe sich ein Image auch in einem einzigen Aufruf erstellen, hierbei würde dann aber nicht die modernere Interoperable Image API genutzt.
Diese bietet die Möglichkeit, Images direkt bei Erstellung zu konvertieren und auf alle AZs zu verteilen.

Um die Erstellung von Snapshots für laufende Instanzen zu ermöglichen, ist es notwendig, dass Sie das Property `--property hw_qemu_guest_agent=True` an den von Ihnen genutzten Images setzen und `qemu-guest-agent` auf dem System installieren.

Weitere Details finden Sie in unseren [FAQ](https://docs.wiit-cloud.io/de/openstack/faq/#warum-kann-ich-keinen-snapshot-einer-laufenden-instance-erstellen).

Das Gleiche funktioniert auch über das Dashboard. Achten Sie hier darauf, alle der obigen Parameter anzugeben.

Die maximale Größe eines Images beträgt 2 TB in der Region de-west-01, in der Region ch-north-01 beträgt die maximale Größe 100 GB.
