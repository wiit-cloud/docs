---
title: QoS
lang: "de"
permalink: /openstack/networking/qos/
nav_order: 2400
parent: Networking
---

# Netzwerk-Quality of Service (QoS)

{: .warning }
Diese Seite beschreibt eine Funktion, die zwar angekündigt, aber noch nicht aktiv ist.

Um die Stabilität unserer Plattform zu gewährleisten und allen Kunden zuverlässige Dienste bereitzustellen, setzen wir in unserem Netzwerk Quality of Service (QoS) ein.

Dies bedeutet, dass jeder Port unserer Infrastruktur auf eine bestimmte maximale Bandbreite begrenzt ist.
Die Begrenzung erfolgt auf Port-Ebene und gilt somit gleichermaßen für alle Flavor-Typen.

Die aktuellen Grenzwerte lauten:

- Maximale Ingress-/Egress-Begrenzung für **interne Ports**: 10 Gbit/s
- Maximale Ingress-/Egress-Begrenzung für **Ports im Provider-Netzwerk**: 5 Gbit/s

Sobald der Netzwerkverkehr die Bandbreitenbegrenzung überschreitet, werden überschüssige Pakete verworfen; bei TCP-Verbindungen äußert sich dies typischerweise durch Paketwiederholungen (Retransmissions) und erhöhte Latenzzeiten.
Dies entspricht dem Verhalten bei Auslastung einer physischen Netzwerkkarte (NIC).
