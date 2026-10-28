---
title: Router VMs
lang: "en"
permalink: /openstack/networking/router_vm/
parent: Networking
nav_order: 2700
---

Router VMs
==========

If you want to route traffic through a router VM, you have to disable port security on the affected ports. Otherwise, packets are dropped when neither the source nor the destination IP address in the packet matches the IP address of the port itself or an IP address in the allowed address pairs.

For operating the router VMs in HA mode (VIP port, allowed address pairs, real MAC addresses), the notes on the [HA Clusters](/openstack/networking/ha_cluster/) page apply.

If all you need is a site-to-site VPN between two networks, [VPN as a Service](/openstack/networking/vpnaas/) is an alternative that does not require dedicated router VMs.

The following diagram shows a typical dual-stack setup with two router VMs, each with a public, an internal and an HA interface:

<svg width="0" height="0" style="position:absolute"><defs><pattern id="hatch-light" width="6" height="6" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><rect width="6" height="6" fill="#f7fafc"/><line x1="0" y1="0" x2="0" y2="6" stroke="#aeb4bd" stroke-width="1"/></pattern></defs></svg>

<style>
.mermaid .cluster rect { fill: url(#hatch-light) !important; }
</style>

```mermaid
flowchart TB
    subgraph PUB["Public network"]
        VIP4P["VIP port IPv4<br/>192.0.2.100"]
        VIP6P["VIP port IPv6<br/>2001:db8:1::100"]
        PPA["Public port A<br/>IP: 192.0.2.11 / 2001:db8:1::11<br/>AAP: 192.0.2.100 / 2001:db8:1::100<br/>(MAC: fa:16:3e:aa:00:01)"]
        PPB["Public port B<br/>IP: 192.0.2.12 / 2001:db8:1::12<br/>AAP: 192.0.2.100 / 2001:db8:1::100<br/>(MAC: fa:16:3e:bb:00:01)"]
    end
    RA["Router VM A"]
    RB["Router VM B"]
    subgraph HAN["HA network (heartbeat)"]
        PHA["HA port A<br/>IP: 10.99.0.11 / 2001:db8:3::11"]
        PHB["HA port B<br/>IP: 10.99.0.12 / 2001:db8:3::12"]
    end
    subgraph INT["Private network"]
        PIA["Internal port A<br/>IP: 10.0.0.11 / 2001:db8:2::11<br/>AAP: 10.0.0.100 / 2001:db8:2::100<br/>(MAC: fa:16:3e:aa:00:02)<br/>Port security: disabled"]
        PIB["Internal port B<br/>IP: 10.0.0.12 / 2001:db8:2::12<br/>AAP: 10.0.0.100 / 2001:db8:2::100<br/>(MAC: fa:16:3e:bb:00:02)<br/>Port security: disabled"]
        VIP4I["VIP port IPv4<br/>10.0.0.100"]
        VIP6I["VIP port IPv6<br/>2001:db8:2::100"]
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

Disable port security
---------------

A typical HA setup with two router VM instances consists of a public interface, a private (internal) interface and, optionally, an HA interface per instance.

In this scenario, port security must be disabled on the internal interface, as this is where traffic is routed. The HA interface and the public interface usually do not route any traffic, so disabling port security is not necessary there:

```bash
openstack port set --disable-port-security <PORT_UUID>
```

Note: With port security disabled, the security groups of the port are no longer applied.
