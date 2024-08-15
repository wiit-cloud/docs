---
title: Flavor Spezifikationen
lang: "de"
permalink: /optimist/specs/flavor_specification/
parent: Spezifikationen
nav_order: 9100
last_modified_date: 2022-03-29
---

# Flavor Spezifikationen

"Flavor" bezeichnet im OpenStack-Kontext ein Hardware-Profil, das eine virtuelle Maschine nutzt bzw. nutzen kann. Im Optimist
sind diverse Standard-Hardwareprofile (Flavors) eingerichtet. Diese haben unterschiedliche Limits und Begrenzungen, welche hier für alle
verfügbaren Flavors aufgelistet sind.

## Migration zwischen Flavor-Typen

Um die Flavors bestehender Instanzen zu ändern, kann die OpenStack-Option [„Resize Instance“](/optimist/faq/#wie-kann-ich-den-flavor-einer-instanz-ändern-instance-resize) entweder über das Dashboard oder die CLI verwendet werden. Dies führt zu einem Neustart der Instanz, aber der Inhalt der Instanz bleibt erhalten. Bitte beachten Sie, dass ein Wechsel der Flavors von den großen Root-Disk-Typen zu einem Flavor mit einer kleineren Root-Disk nicht möglich ist.

{: .warning-title }
>Warnung
>
>Dies gilt nicht für l1 (localstorage) Flavors.
>Weitere Informationen  finden Sie unter [Storage → Localstorage](/optimist/storage/localstorage/#openstack-features).

## Flavor-Typen

### Standard-Flavors

| Bezeichnung | Kerne |   RAM |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) | Network Bandwidth |
| :---------- | ----: | ----: | ----: | -----------------------: | ------------------------------: | ----------------: |
| s1.micro    |     1 |  2 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          1 Gbit/s |
| s1.small    |     2 |  4 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          2 Gbit/s |
| s1.medium   |     4 |  8 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          3 Gbit/s |
| s1.large    |     8 | 16 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          4 Gbit/s |
| s1.xlarge   |    16 | 32 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          4 Gbit/s |
| s1.2xlarge  |    30 | 64 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          4 Gbit/s |

### Standard CPU Large Disk Flavors

| Bezeichnung   | Kerne |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) | Network Bandwidth |
| :----------   | ----: | ----: | ----:  | -----------------------: | ------------------------------: | ----------------: |
| s1.micro.d    |     1 |  2 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          1 Gbit/s |
| s1.small.d    |     2 |  4 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          2 Gbit/s |
| s1.medium.d   |     4 |  8 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          3 Gbit/s |
| s1.large.d    |     8 | 16 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |
| s1.xlarge.d   |    16 | 32 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |
| s1.2xlarge.d  |    30 | 64 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |

### Dedicated CPU Flavors

| Bezeichnung | Kerne |   RAM  |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) | Network Bandwidth |
| :---------- | -----:| -----: | ----: | -----------------------: | ------------------------------: | ----------------: |
| d1.micro    |    1  |  8 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          1 Gbit/s |
| d1.small    |    2  | 16 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          2 Gbit/s |
| d1.medium   |    4  | 32 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          3 Gbit/s |
| d1.large    |    8  | 64 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          4 Gbit/s |
| d1.xlarge   |   16  | 128 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          4 Gbit/s |
| d1.2xlarge  |   30  | 256 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |          4 Gbit/s |

### Dedicated CPU Large Disk Flavors

| Bezeichnung   | Kerne |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) | Network Bandwidth |
| :----------   | ----: | ----: | ----:  | -----------------------: | ------------------------------: | ----------------: |
| d1.micro.d    |     1 |  8 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          1 Gbit/s |
| d1.small.d    |     2 | 16 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          2 Gbit/s |
| d1.medium.d   |     4 | 32 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          3 Gbit/s |
| d1.large.d    |     8 | 64 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |
| d1.xlarge.d   |    16 |128 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |
| d1.2xlarge.d  |    30 |256 GB | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |

### Localstorage Flavors

| Bezeichnung | Kerne  |   RAM  |  Disk    | IOPS Limits (read/write) | IO throughput rate (read/write) | Network Bandwidth |
| :---------- | -----: | -----: | -------: | -----------------------: | ------------------------------: | ----------------: |
| l1.micro    |    1   |   8 GB |   300 GB |            25000 / 10000 |             125 MB/s / 60 MB/s  |          1 Gbit/s |
| l1.small    |    2   |  16 GB |   600 GB |            50000 / 25000 |             250 MB/s / 125 MB/s |          2 Gbit/s |
| l1.medium   |    4   |  32 GB |  1200 GB |           100000 / 50000 |             500 MB/s / 250 MB/s |          3 Gbit/s |
| l1.large    |    8   |  64 GB |  2500 GB |          100000 / 100000 |            1000 MB/s / 500 MB/s |          4 Gbit/s |
| l1.xlarge   |   16   | 128 GB |  5000 GB |          100000 / 100000 |           2000 MB/s / 1125 MB/s |          4 Gbit/s |
| l1.2xlarge  |   30   | 256 GB | 10000 GB |          100000 / 100000 |           2000 MB/s / 2000 MB/s |          4 Gbit/s |

L1 / Localstorage-Flavours können bei <support@gec.io> angefordert werden.
