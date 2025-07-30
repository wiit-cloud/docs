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

## Upload von eigenen Images

Sie können jederzeit Ihre eigenen Images hochladen, anstatt die von uns bereit gestellten zu nutzen. Am einfachsten funktioniert das über die OpenStack-CLI.

```bash
openstack image create \
  --property hw_disk_bus=scsi \
  --property hw_qemu_guest_agent=True \
  --property hw_scsi_model=virtio-scsi \
  --property os_require_quiesce=True \
  --private \
  --disk-format qcow2 \
  --container-format bare \
  --file ~/my-image.qcow2 \
  my-image
```

Dabei müssen mindestens folgende Parameter spezifiziert werden:

- `--disk-format`: Das Format Ihres Quell-Images, z.B. `qcow2`
- `--file`: Das Quell-Image auf Ihrem System
- Name des Abbilds: `my-image` als Beispiel.

Um die Erstellung von Snapshots für laufende Instanzen zu ermöglichen ist es notwendig, dass Sie das Property `--property hw_qemu_guest_agent=True` an den von Ihnen genutzten Images setzen und `qemu-guest-agent` auf dem System installieren.

Weitere Details finden Sie in unseren [FAQ](https://docs.wiit-cloud.io/de/openstack/faq/#warum-kann-ich-keinen-snapshot-einer-laufenden-instance-erstellen).

Das gleiche funktioniert auch über das Dashboard. Achten Sie hier darauf, alle der obigen Parameter anzugeben.
