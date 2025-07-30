---
title: Flavor Spezifikationen
lang: "de"
permalink: /openstack/specs/flavor_specification/
parent: Spezifikationen
nav_order: 9100
last_modified_date: 2025-07-21
---

# Flavor Spezifikationen

"Flavor" bezeichnet im OpenStack-Kontext ein Hardware-Profil, das eine virtuelle Maschine nutzt bzw. nutzen kann. Im der OpenStack-Plattform
sind diverse Standard-Hardwareprofile (Flavors) eingerichtet. Diese haben unterschiedliche Limits und Begrenzungen, welche hier für alle
verfügbaren Flavors aufgelistet sind.

## Migration zwischen Flavor-Typen

Um die Flavors bestehender Instanzen zu ändern, kann die OpenStack-Option [„Resize Instance“](/openstack/faq/#wie-kann-ich-den-flavor-einer-instanz-ändern-instance-resize) entweder über das Dashboard oder die CLI verwendet werden. Dies führt zu einem Neustart der Instanz, aber der Inhalt der Instanz bleibt erhalten. Bitte beachten Sie, dass ein Wechsel der Flavors von den großen Root-Disk-Typen zu einem Flavor mit einer kleineren Root-Disk nicht möglich ist.

## Flavor-Typen

- s1 (Standard) Flavors: shared CPU, 1:2 CPU / memory ratio  
- m1 (Memory-Optimized) Flavors: shared CPU, 1:4 CPU / memory ratio
- d1 (Dedicated) Flavors: pinned CPU, 1:8 CPU / memory ratio
- g1 (GPU) Flavors: pinned CPU, A100 80GB GPU(s)  - nur verfügbar in SZ1 und keine Live Migrationen möglich


Alle Flavors sind standardmäßig sofern es nicht anderweitig angegeben ist in allen Verfügbarkeitszonen sowie in der SZ1 verfügbar.  
Neben der Standard Variante mit 20GB Root Disk bieten wir eine .d Variante mit 100GB Root Disk an.  
Alternativ kann jeder Flavor in Kombination mit einem Volume als Root Disk erstellt werden.

### s1 (Standard) Flavors

| Bezeichnung | Kerne |   RAM |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | ----: | ----: | ----: | -----------------------: | ------------------------------: |
| s1.micro    |     1 |  2 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.small    |     2 |  4 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.medium   |     4 |  8 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.large    |     8 | 16 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.xlarge   |    16 | 32 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.2xlarge  |    32 | 64 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### s1 (Standard) Flavors - Large Root Disk

| Bezeichnung   | Kerne |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :----------   | ----: | ----: | ----:  | -----------------------: | ------------------------------: |
| s1.micro.d    |     1 |  2 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.small.d    |     2 |  4 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.medium.d   |     4 |  8 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.large.d    |     8 | 16 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.xlarge.d   |    16 | 32 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.2xlarge.d  |    32 | 64 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### m1 (Memory-Optimized) Flavors

| Bezeichnung | Kerne |   RAM |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | ----: | ----: | ----: | -----------------------: | ------------------------------: |
| m1.micro    |     1 |  4 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.small    |     2 |  8 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.medium   |     4 | 16 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.large    |     8 | 32 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.xlarge   |    16 | 64 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.2xlarge  |    32 |128 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### m1 (Memory-Optimized) Flavors - Large Root Disk

| Bezeichnung | Kerne |   RAM |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | ----: | ----: | ----: | -----------------------: | ------------------------------: |
| m1.micro.d  |     1 |  4 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.small.d  |     2 |  8 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.medium.d |     4 | 16 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.large.d  |     8 | 32 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.xlarge.d |    16 | 64 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.2xlarge.d|    32 |128 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### d1 (Dedicated) Flavors

| Bezeichnung | Kerne |   RAM  |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | -----:| -----: | ----: | -----------------------: | ------------------------------: |
| d1.micro    |    1  |  8 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.small    |    2  | 16 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.medium   |    4  | 32 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.large    |    8  | 64 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.xlarge   |   16  | 128 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.2xlarge  |   30  | 256 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### d1 (Dedicated) Flavors - Large Root Disk

| Bezeichnung   | Kerne |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :----------   | ----: | ----: | ----:  | -----------------------: | ------------------------------: |
| d1.micro.d    |     1 |  8 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.small.d    |     2 | 16 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.medium.d   |     4 | 32 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.large.d    |     8 | 64 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.xlarge.d   |    16 |128 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.2xlarge.d  |    30 |256 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### g1 (GPU) Flavors - nur verfügbar in SZ1

| Bezeichnung   |GPU         | Kerne |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :----------   |-----------:| ----: | ----: | ----:  | -----------------------: | ------------------------------: |
| g1.xlarge-1g  |1x A100 80GB|    30 |480 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| g1.2xlarge-2g |2x A100 80GB|    60 |960 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
