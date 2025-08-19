---
title: "Benutzer-, Projekt- & Gruppenverwaltung (Manager-Rolle)"
lang: "de"
permalink: /openstack/quickstart/manager_role/
nav_order: 1010
parent: Quickstart
---

# Projekt-, Benutzer- & Rollenverwaltung

## Dedizierte OpenStack-Domain & Identity Self-Service

Als Kunde verfügen Sie über eine dedizierte OpenStack-Domain inklusive einem oder mehreren Domain-Manager-Accounts, mit denen Sie eigenständig Projekte, Benutzer, Gruppen und Rollenzuweisungen über die OpenStack CLI verwalten können.  
Domain-Manager-Benutzer besitzen ausschließlich Berechtigungen zur Identitätsverwaltung und sollten nicht einzelnen Projekten innerhalb der Domain zugewiesen werden.  
Die Domains sind in OpenStack isoliert und verfügen über einen eigenen Namensraum für Projekte, Gruppen und Benutzer.  
Auch die Einrichtung von 2FA (Zwei-Faktor-Authentifizierung) für die Nutzer kann durch den Domain-Manager konfiguriert werden.

Folgende Keystone-Compliance-Richtlinien gelten:
- **Passwortänderung beim ersten Login:** Kann mit `--ignore-change-password-upon-first-use` bei der Benutzererstellung umgangen werden  
- **Passwort-Richtlinie:** Mindestens 18 Zeichen, mindestens ein Großbuchstabe, Kleinbuchstabe, Zahl und Sonderzeichen

Neue Projekte erhalten standardmäßig Quotas, individuelle Anpassungen können über den [WIIT Support](mailto:helpdesk.de@wiit.one) angefragt werden.
Domains und Domain-Manager-Konten werden von der WIIT AG verwaltet. Änderungen können ebenfalls über den [WIIT Support](mailto:helpdesk.de@wiit.one) beantragt werden.

**Wichtig:** Manager-Benutzer können sich nicht am Dashboard anmelden. Alle Operationen stehen ausschließlich über CLI oder API zur Verfügung, wie unten beschrieben.

## Domain Manager CLI-Konfiguration

Zur Authentifizierung als Domain-Manager verwenden Sie folgende CLI-Parameter:

```bash
openstack --os-username $MANAGER_USERNAME \
          --os-password $PASSWORD \
          --os-auth-url https://identity.openstack.de-west-01.wiit-cloud.io/v3 \
          --os-user-domain-name $DOMAIN_NAME \
          --os-domain-name $DOMAIN_NAME \
          --os-auth-type password \
$COMMAND
```

Als Domain-Manager können Sie Benutzer, Projekte, Gruppen und Rollen mit folgenden Befehlen verwalten:

### Benutzerverwaltung

```bash
# Benutzer erstellen
user create --domain $DOMAIN_NAME --password-prompt $USER

# Alle Benutzer in der Domain auflisten
user list

# Benutzerdetails anzeigen
user show $USER

# Benutzer löschen
user delete $USER

# 2FA für Benutzer aktivieren
user set --enable-multi-factor-auth $USER

# 2FA-Regeln setzen (z. B. Passwort + TOTP oder App-Credential)
user set \
  --enable-multi-factor-auth \
  --multi-factor-auth-rule password,totp \
  --multi-factor-auth-rule v3applicationcredential \
  $USER

# TOTP-Secret für Benutzer anlegen (als Benutzer ausführen, vor Anwendung der 2FA-Regeln durch den Manager)
openstack credential create --type totp --secret $BASE_32_2FA_SECRET $USER_ID
```

### Projektverwaltung

```bash
# Neues Projekt erstellen
project create $PROJECT --domain $DOMAIN_NAME

# Alle Projekte auflisten
project list

# Projekt löschen
project delete $PROJECT
```

**Wichtig:** Vor dem Löschen eines Projekts müssen alle Ressourcen darin bereinigt werden. Andernfalls werden diese weiterhin berechnet, bis unsere automatisierten Cleanup-Jobs ausgeführt wurden.

### Gruppenverwaltung

```bash
# Gruppe erstellen
group create $GROUP --domain $DOMAIN_NAME

# Gruppen auflisten
group list

# Gruppe löschen
group delete $GROUP

# Benutzer zur Gruppe hinzufügen
group add user $GROUP $USER

# Benutzer aus Gruppe entfernen
group remove user $GROUP $USER
```

### Rollenzuweisungen

**Wichtig:** Vergeben sie ausschließlich die Rolle "member" - die Rolle "reader" steht im System zur Verfügung, die effektiven Berechtigungen sind jedoch nicht read-only. 

```bash
# Rolle einem Benutzer in einem Projekt zuweisen
role add --user $USER --project $PROJECT member

# Rolle einer Gruppe in einem Projekt zuweisen
role add --group $GROUP --project $PROJECT member

# Rolle von einem Benutzer entfernen
role remove --user $USER --project $PROJECT member

# Rolle von einer Gruppe entfernen
role remove --group $GROUP --project $PROJECT member

# Rollen eines Benutzers auflisten
role assignment list --names --user $USER

# Rollen eines Projekts auflisten
role assignment list --names --project $PROJECT

# Rollen einer Gruppe auflisten
role assignment list --names --group $GROUP
```
