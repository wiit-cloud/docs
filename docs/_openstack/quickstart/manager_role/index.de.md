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

{: .warning }
Manager-Benutzern steht das Dashboard nicht zur Verfügung. Eine Anmeldung ist zwar möglich, aber alle Operationen stehen ausschließlich über CLI oder API zur Verfügung, wie unten beschrieben.
Für Benutzer ohne Projektzuordnung (wie der vordefinierte Manager-User) können keine Application Credentials erzeugt werden.
Application Credentials können nur für Operationen innerhalb der Projekte genutzt werden, nicht für Operationen auf Domain-Ebene, wie bspw. die Anlage von Projekten.

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

**Wichtig:** Wenn Sie die Verwendung einer RC Datei oder einer clouds.yaml bevorzugen, dann finden Sie weiter unten im [Templates](#templates) Bereich passende Beispiele.

Als Domain-Manager können Sie Benutzer, Projekte, Gruppen und Rollen mit den folgenden Befehlen verwalten, nutzen Sie dazu die Befehle in Kombination mit den obenstehenden Parametern:

### Abfragen der Domain ID 
```bash
# Abfragen der Domain ID
domain show $DOMAIN_NAME
```

{: .warning }
Verwenden Sie für die folgenden Befehle die Domain ID und nicht den Domain Namen. 
Sollte der Domain Name aus Zahlen bestehen kann dieser von der OpenStack CLI fälschlicherweise als Domain ID interpretiert werden und die Anfrage schlägt fehl.

### Benutzerverwaltung

```bash
# Benutzer erstellen
user create --domain $DOMAIN_ID --password-prompt $USER

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
  --multi-factor-auth-rule application_credential \
  $USER

# TOTP-Secret für Benutzer anlegen (als Benutzer ausführen, vor Anwendung der 2FA-Regeln durch den Manager)
openstack credential create --type totp $USER_ID $BASE_32_2FA_SECRET
```

### Projektverwaltung

```bash
# Neues Projekt erstellen
project create $PROJECT --domain $DOMAIN_ID

# Alle Projekte auflisten
project list

# Projekt löschen
project delete $PROJECT
```

**Wichtig:** Vor dem Löschen eines Projekts müssen alle Ressourcen darin bereinigt werden. Andernfalls werden diese weiterhin berechnet, bis unsere automatisierten Cleanup-Jobs ausgeführt wurden.

### Gruppenverwaltung

```bash
# Gruppe erstellen
group create $GROUP --domain $DOMAIN_ID

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

## Templates

{: .warning }
Die RC Datei und die clouds.yaml welche man von Horizon herunterladen kann, funktionieren leider nicht mit einem Manager Nutzer.
Stattdessen können die untenstehenden Templates verwendet werden, in denen $MANAGER_USERNAME und $DOMAIN_NAME entsprechend angepasst werden müssen.

### OpenStack RC File
```bash
#!/usr/bin/env bash
export OS_AUTH_URL=https://identity.openstack.de-west-01.wiit-cloud.io/v3
export OS_USERNAME="$MANAGER_USERNAME"
export OS_USER_DOMAIN_NAME="$DOMAIN_NAME"
export OS_DOMAIN_NAME="$DOMAIN_NAME"
export OS_REGION_NAME="de-west-01"
export OS_INTERFACE=public
export OS_IDENTITY_API_VERSION=3

unset OS_TENANT_ID
unset OS_TENANT_NAME

unset OS_PROJECT_ID
unset OS_PROJECT_NAME
unset OS_PROJECT_DOMAIN_ID

# With Keystone you pass the keystone password.
echo "Please enter your OpenStack Password for domain $OS_DOMAIN_NAME as user $OS_USERNAME: "
read -sr OS_PASSWORD_INPUT
export OS_PASSWORD=$OS_PASSWORD_INPUT
```

### OpenStack clouds.yaml
```bash
clouds:
  de-west-01-manager:
    auth:
      auth_url: https://identity.openstack.de-west-01.wiit-cloud.io/v3
      username: "$MANAGER_USERNAME"
      user_domain_name: "$DOMAIN_NAME"
      domain_name: "$DOMAIN_NAME"
    region_name: "de-west-01"
    interface: "public"
    identity_api_version: 3
```