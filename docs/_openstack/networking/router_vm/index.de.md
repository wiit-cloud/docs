---
title: Router-VMs
lang: "de"
permalink: /openstack/networking/router_vm/
parent: Netzwerk
nav_order: 2700
---

Router-VMs
==========

Wenn Sie Datenverkehr über eine Router-VM routen möchten, müssen Sie Port Security auf den betroffenen Ports deaktivieren. Andernfalls werden Pakete verworfen, wenn weder die Quell- noch die Ziel-IP-Adresse des Pakets der IP-Adresse des Ports selbst oder einer IP-Adresse aus den Allowed Address Pairs entspricht.

Für den HA-Betrieb der Router-VMs (VIP-Port, Allowed Address Pairs, echte MAC-Adressen) gelten die Hinweise auf der Seite [HA-Cluster](/openstack/networking/ha_cluster/).

Wenn ausschließlich ein Site-to-Site-VPN zwischen zwei Netzwerken benötigt wird, ist [VPN as a Service](/openstack/networking/vpnaas/) eine Alternative, die ohne eigene Router-VMs auskommt.

Das folgende Diagramm zeigt ein typisches Dual-Stack-Setup mit zwei Router-VMs, die jeweils ein öffentliches, ein internes und ein HA-Interface haben:

<svg width="0" height="0" style="position:absolute"><defs><pattern id="hatch-light" width="6" height="6" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><rect width="6" height="6" fill="#f7fafc"/><line x1="0" y1="0" x2="0" y2="6" stroke="#aeb4bd" stroke-width="1"/></pattern></defs></svg>

<style>
.mermaid .cluster rect { fill: url(#hatch-light) !important; }
</style>

```mermaid
flowchart TB
    subgraph PUB["Öffentliches Netz"]
        VIP4P["VIP-Port IPv4<br/>192.0.2.100<br/>Port Security: aktiviert"]
        VIP6P["VIP-Port IPv6<br/>2001:db8:1::100<br/>Port Security: aktiviert"]
        PPA["Port öffentlich A<br/>IP: 192.0.2.11 / 2001:db8:1::11<br/>AAP: 192.0.2.100 / 2001:db8:1::100<br/>(MAC: fa:16:3e:aa:00:01)<br/>Port Security: aktiviert"]
        PPB["Port öffentlich B<br/>IP: 192.0.2.12 / 2001:db8:1::12<br/>AAP: 192.0.2.100 / 2001:db8:1::100<br/>(MAC: fa:16:3e:bb:00:01)<br/>Port Security: aktiviert"]
    end
    RA["Router-VM A"]
    RB["Router-VM B"]
    subgraph HAN["HA-Netz (Heartbeat)"]
        PHA["Port HA A<br/>IP: 10.99.0.11 / 2001:db8:3::11<br/>Port Security: aktiviert"]
        PHB["Port HA B<br/>IP: 10.99.0.12 / 2001:db8:3::12<br/>Port Security: aktiviert"]
    end
    subgraph INT["Privates Netz"]
        PIA["Port intern A<br/>IP: 10.0.0.11 / 2001:db8:2::11<br/>AAP: 10.0.0.100 / 2001:db8:2::100<br/>(MAC: fa:16:3e:aa:00:02)<br/>Port Security: deaktiviert"]
        PIB["Port intern B<br/>IP: 10.0.0.12 / 2001:db8:2::12<br/>AAP: 10.0.0.100 / 2001:db8:2::100<br/>(MAC: fa:16:3e:bb:00:02)<br/>Port Security: deaktiviert"]
        VIP4I["VIP-Port IPv4<br/>10.0.0.100<br/>Port Security: deaktiviert"]
        VIP6I["VIP-Port IPv6<br/>2001:db8:2::100<br/>Port Security: deaktiviert"]
    end
    VIP4P --- PPA
    VIP4P --- PPB
    VIP6P --- PPA
    VIP6P --- PPB
    PPA --- RA
    PPB --- RB
    RA --- PIA
    RA --- PHA
    RB --- PIB
    RB --- PHB
    PIA --- VIP4I
    PIA --- VIP6I
    PIB --- VIP4I
    PIB --- VIP6I
    PHA --- PIA
    PHB --- PIB
    linkStyle 0,1,2,3,10,11,12,13,14,15 stroke:transparent,stroke-width:0px
    style PUB fill:#f7fafc,stroke:#676b6d
    style HAN fill:#f7fafc,stroke:#676b6d
    style INT fill:#f7fafc,stroke:#676b6d
    classDef port fill:#f0f4f7,stroke:#676b6d
    classDef vip fill:#d7dfee,stroke:#0b194a,stroke-dasharray: 5 3
    classDef vm fill:#0b194a,stroke:#0b194a,color:#ffffff
    class PPA,PPB,PHA,PHB,PIA,PIB port
    class VIP4P,VIP6P,VIP4I,VIP6I vip

    class RA,RB vm
```

Port Security deaktivieren
---------------

Ein typisches HA-Setup mit zwei Router-VM-Instanzen besteht pro Instanz aus einem öffentlichen Interface, einem privaten (internen) Interface und gegebenenfalls einem HA-Interface.

In diesem Szenario muss Port Security auf dem internen Interface deaktiviert werden, da dieses den Datenverkehr routet. Auf dem HA-Interface und dem öffentlichen Interface wird üblicherweise nicht geroutet, daher ist eine Deaktivierung dort nicht notwendig:

```bash
openstack port set --disable-port-security <PORT_UUID>
```

Hinweis: Bei deaktivierter Port Security werden die Security Groups des Ports nicht mehr angewendet.
