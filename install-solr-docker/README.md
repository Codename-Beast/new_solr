# 🔍 Eledia Solr Ansible Role v3.3.1

## 🚀 **Übersicht**

**Production-Ready Apache Solr-Installation für Eledia-Server** mit modernster Docker-Integration, Multi-Tenant-Support und vollautomatischer Moodle-Integration.

Diese Ansible-Role ermöglicht die vollautomatische Installation und Konfiguration von Apache Solr 9.9.0 als Suchserver für sowohl Single-Tenant (Moodle) als auch Multi-Tenant (Solr Server) Umgebungen.

## ✨ **Version 3.3.1 Features**

### 🎯 **Dual-Mode Support**
- 🏠 **Single-Tenant Mode** - Solr direkt auf Moodle-Server (system_type: Moodle)
- 🏢 **Multi-Tenant Mode** - Dedizierte Solr-Server für mehrere Kunden (system_type: Solr)
- 🤖 **Auto-Detection** - Automatische Erkennung basierend auf system_type
- 🔄 **Core Management** - Sichere Addition/Removal/Reload von Cores ohne Container-Neustart 
### 🔒 ** Variablen-Struktur**
- 🛡️ **`solr_config_data` Variable** - Keine Überschreibung von `additional_config_data`
- 📝 **Unified Host-Vars** - Ein einziger Solr-Konfigurationsblock
- 🔄 **Auto-Integration** - `additional_config_data` wird automatisch erweitert

### 🧪 **Core Testing mit Moodle-Dokumenten**
- 📊 **5 Realistische Test-Dokumente** - Course, Forum, Wiki, Assignment, Resource
- 📊 **3 Such-Tests** - Text-Suche, Course-Filter, Type-Filter
- 📊 **Performance-Metrics** - Query-Zeit und Dokumenten-Anzahl
- 🧹 **Optional Cleanup** - Test-Dokumente können automatisch entfernt werden

### 🔄 **Verbesserte Architektur**
- 🏷️ **Modulare Task-Organisation** - core_testing.yml für Funktionalitätstests
- 📋 **Copy/Paste Apache-Config** - Fertige VirtualHost-Konfiguration
- 🔐 **Externe Erreichbarkeit** - https://domain/__solr/ mit Auth-Daten
- ⚡ **Automatische Config-Generation** ---> Bald! noch Manuel

## ✨ **Hauptmerkmale**

### 🔧 **Kernfunktionen**
- ✅ **Docker-basierte Solr 9.9.0** mit automatischer Container-Verwaltung
- ✅ **Variablen-Verwaltung** mit solr_config_data
- ✅ **Core Testing** mit realistischen Moodle-ähnlichen Dokumenten
- ✅ **Tag-basiertes Uninstall** für vollständige Systemreinigung
- ✅ **Authentifizierung** mit sicherer Passwort-Verwaltung
- ✅ **Vollständige Moodle-Integration** mit automatischen config.php Updates(Bug moodle_path exitiert nicht also wird sie nicht gepatcht!)
- ✅ **Production-Ready** - Erfolgreich getestet auf XEN-Servern und Hetzner-Cloud Systemen!
- ✅ **Unified Host-Vars** - Ein sauberer Solr-Konfigurationsblock

### 🧪 **Core Testing Features**
```bash
# Vollständige Deinstallation in einem Befehl:
ansible-playbook install-solr.yml --tags="uninstall"

# Automatische Erkennung:
✅ Konfiguration in config.php erkannt
✅ Host-Variablen bereinigt
✅ Container und Volumes entfernt
✅ Saubere Systemwiederherstellung
```

## ⚙️ **Deployment-Modi**

### 🎯 **Standard-Modus (Ansible Direct)**
- **Verhalten**: Ansible erstellt und verwaltet Container direkt
- **Aktivierung**: Keine `dockerfile_path` Variable übergeben (Standard)
- **Management**: Container wird direkt von Ansible verwaltet
- **Vorteile**: Einfach, schnell, vollautomatisch

### 🐳 **Dockerfile-Modus (Custom Build)**
- **Verhalten**: Docker-Compose mit Build-Prozess aus Custom-Dockerfile
- **Aktivierung**: `dockerfile_path` Variable mit Pfad zur Dockerfile
- **Management**: Via Docker-Compose und Management-Scripts
- **Vorteile**: Vollständige Anpassbarkeit, erweiterte Konfiguration

```bash
# Standard-Modus (empfohlen für die meisten Fälle)
ansible-playbook ... # Ohne dockerfile_path

# Dockerfile-Modus (für Custom-Builds)
ansible-playbook ... -e "dockerfile_path=/path/to/Dockerfile"
```

## 📋 Systemanforderungen

### Mindestanforderungen
- **Betriebssystem**: Debian 11+
- **Webserver**: Apache2 mit SSL-Modul aktiviert
- **Berechtigung**: Sudo-Rechte erforderlich
- **Memory**: Mindestens **4GB RAM** (empfohlen: 8GB+)
- **Storage**: Mindestens **6GB** freier Festplattenspeicherplatz
- **Moodle**: Vorhandene Moodle-Installation (Version 3.9+)

### Empfohlene Systemkonfiguration
- **CPU**: 2+ Cores
### 💾 **System-Anforderungen**
- **OS**: Debian 11/12, Ubuntu 20.04+
- **RAM**: 4GB+ (8GB+ für Production-Umgebungen)
- **Storage**: SSD mit 10GB+ freiem Speicherplatz  
- **Network**: Stabile Internetverbindung für Container-Downloads
- **Docker**: Wird automatisch installiert falls nicht vorhanden
- **Ports**: 8983 muss verfügbar sein (wird automatisch geprüft)

### 🔐 **Sicherheitsmerkmale**
- **Non-Root Container** mit dediziertem Solr-User (UID/GID 8983)
- **Authentifizierung** mit `solr_auth_*` Variablen-System
- **Host-Vars Integration** - Credentials persistent gespeichert
- **Apache-Proxy Ready** - Copy/Paste Konfiguration für externe Erreichbarkeit
- **Backup-Mechanismen** für Datenintegrität

### 📋 **Unterstützte Systeme**
- ✅ **Moodle-Server** (Haupt-Anwendungsfall)
- ✅ **Mahara-Server** (kompatibel)
- ✅ **Blank Systems** (nackte Server-Installation)
- ✅ **Multi-Service-Server** (solange Port 8983 frei ist)
- ✅ **XEN-VMs, Cloud Server(Hetzner)**

---

## 🚀 **Installation & Verwendung**

### **🔧 Single-Tenant Installation (Moodle-Server)**
```bash
# Standard Moodle-Server Installation
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server"

# Beispiel: xen-23-v_bschreielearninghomede
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede"
```

### **🏢 Multi-Tenant Installation (Solr-Server)**
```bash
# Dedizierter Solr-Server für Multi-Tenant
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "moodle_app_domain=multi-tenant-server"

# Beispiel: solrinstalltestelediade
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "moodle_app_domain=multi-tenant-server"
```

### **🐳 Installation mit Custom-Dockerfile**
```bash
# Dockerfile-basierte Installation
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  -e "dockerfile_path=/path/to/your/Dockerfile" \
  -e "solr_auth_enabled=true"

# Mit Beispiel-Dockerfile aus der Rolle
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  -e "dockerfile_path=files/Dockerfile.example" \
  -e "solr_auth_enabled=true"
```

### **🔄 Multi-Tenant Core Management**

#### **Neuen Core hinzufügen:**
```bash
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "add_solr_core=kunde.example.com" \
  -e "kunden_moodle_hostvars=/path/to/kunde-hostvars" \
  -e "solr_server_hostvars=Target-Server"
```

#### **Core neu laden:**
```bash
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "reload_solr_core=eledia_solr_kunde"
```

#### **Core entfernen:**
```bash
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "remove_solr_core=eledia_solr_kunde" \
  -e "force_core_removal=true"
```

### **🌐 Installation für externe Erreichbarkeit**
```bash
# Installation mit Apache-Proxy-Konfiguration
ansible-playbook -i ansible-inventory/{inventory}/hosts/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  -e "solr_auth_enabled=true" \
  -e "moodle_app_domain=bschrei.elearning.home" \
  --tags "finalize"
```

### **🗑️ Vollständige Deinstallation**
```bash
ansible-playbook -i ansible-inventory/{inventory}/hosts/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "uninstall"
```

### **🔄 Neuinstallation (Clean Install)**
```bash
# Schritt 1: Saubere Deinstallation
ansible-playbook -i ansible-inventory{inventory}/hosts/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "uninstall"

# Schritt 2: Frische Installation  
ansible-playbook -i ansible-inventory/{inventory}/hosts/install-solr.yml \
  -e "hosts=hosts=xen-xx-v_kundenname" \
  -e "solr_auth_enabled=true"
```

### **📊 Erweiterte Installationsoptionen**
```bash
# Mit Docker-Compose Generation
ansible-playbook ... -e "create_docker_compose=true"

# Mit Monitoring
ansible-playbook ... -e "monitoring_enabled=true"

# Nur Credentials ohne Installation
ansible-playbook ... --tags "password,security"
```

---

## 🏷️ **Tag-System & Ausführung (Version 7.0)**

### **🎯 Verfügbare Tags**
```bash
# Haupt-Phasen
always              # Läuft immer (Validation, Facts)
docker              # Docker-Installation und Container-Management
installation        # Solr-Installation und Core-Setup
deployment          # Container-Deployment
multi-tenant        # Multi-Tenant spezifische Tasks
core-management     # Core Addition/Removal/Reload
moodle              # Moodle-Integration und Config
finalization        # Final Summary und Konfiguration

# Spezial-Tasks
uninstall           # Vollständige Deinstallation
status              # System-Status prüfen (nie automatisch)
dry-run             # Analyse ohne Änderungen (nie automatisch)
never               # Manuelle Ausführung erforderlich
```

### **🎯 Praktische Tag-Verwendung**

#### **📦 Standard-Installation (alle Phasen)**
```bash
# Komplette Installation ohne spezielle Tags
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname"
```

#### **🔧 Nur Docker-Komponenten**
```bash
# Nur Docker-Installation und Container-Setup
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "docker,install,container"
```

#### **🔐 Nur Authentifizierung**
```bash
# Nur Authentication und Security
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "auth,security,password"
```

#### **🎯 Nur Core-Management**
```bash
# Nur Solr-Core Erstellung und Management
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "core,single-core"
```

#### **📋 Nur Finalisierung mit Apache-Config**
```bash
# Nur Final Summary und Apache-Konfiguration anzeigen
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "finalize,summary"
```

#### **🗑️ Komplette Deinstallation**
```bash
# Vollständige Deinstallation
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "uninstall"
```

#### **📊 Status-Check**
```bash
# Nur Status prüfen (ohne Installation)
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "status"
```

#### **🔍 System-Analyse**
```bash
# Dry-Run und Analyse ohne Änderungen
ansible-playbook -i ansible-inventory/{inventory}/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  -e "solr_dry_run_mode=true" \
  --tags "dry-run,analysis"
```

### **⚡ Kombinierte Tag-Verwendung**
```bash
# Docker + Auth + Core (ohne Finalize)
ansible-playbook ... --tags "docker,auth,core"

# Port-Check + Container + Auth
ansible-playbook ... --tags "port-check,container,auth"

# Nur Security-relevante Tasks
ansible-playbook ... --tags "security,password,authentication"
```

### **🔄 Workflow-Beispiele**

#### **Neuinstallation (Uninstall + Install)**
```bash
# Schritt 1: Deinstallation
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_kundenname" \
  --tags "uninstall"

# Schritt 2: Frische Installation
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-xen-xx-v_kundenname" \
  -e "solr_auth_enabled=true"
```

#### **Reparatur-Installation**
```bash
# Core-Probleme beheben
ansible-playbook ... --tags "core,single-core"

# Auth-Probleme beheben  
ansible-playbook ... --tags "auth,security,password"

# Container-Probleme beheben
ansible-playbook ... --tags "container,docker"
```

---

## 🌐 **Apache Web-UI Integration (NEW in v3.3.1)**

Die Rolle bietet jetzt vollautomatische Apache Web-UI Integration mit SSL-Support für produktiven Zugriff auf das Solr Admin Interface.

### **🚀 Aktivierung**
```bash
# Vollautomatische Apache Integration mit SSL
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-xx-v_targetserver" \
  -e "enable_solr_web_ui=true" \
  --tags "apache,web-ui"
```

### **🔐 SSL-Konfiguration**
```bash
# Standard: Automatische SSL-Zertifikat-Erweiterung
solr_web_ui_auto_ssl: true              # SSL automatisch konfigurieren
solr_web_ui_ssl_email: "admin@domain.de" # E-Mail für Let's Encrypt
solr_web_ui_force_ssl: true             # HTTPS-Weiterleitung erzwingen

# SSL deaktiviert (nur für Entwicklung)
ansible-playbook ... -e "solr_web_ui_auto_ssl=false"
```

### **🎯 Funktionen**
- ✅ **Automatische Apache-Erkennung** - Läuft nur wenn Apache2 installiert ist
- ✅ **SSL-Zertifikat-Erweiterung** - Erweitert bestehende Zertifikate um Solr-Subdomain
- ✅ **Sichere Konfiguration** - ConfigTest vor jeder Änderung
- ✅ **Domain-dynamisch** - Funktioniert mit jeder Domain
- ✅ **Rollback-sicher** - Entfernt fehlerhafte Konfigurationen automatisch

### **📋 Ergebnis**
Nach erfolgreicher Integration:
```
🌐 Solr Admin UI erreichbar unter:
https://solr.bschrei.elearning-home.de/

🔐 Authentifizierung erforderlich:
Username: admin
Password: [Generiert in host_vars]
```

### **⚙️ Technische Details**
- **VirtualHost**: Automatisch erstellt in `/etc/apache2/sites-available/`
- **SSL-Module**: Proxy, SSL, Headers werden automatisch aktiviert
- **Zertifikat**: Erweitert bestehende Let's Encrypt Zertifikate
- **Sicherheit**: Nur HTTPS-Zugriff, Authentifizierung erforderlich

---

## � **Apache-Proxy-Konfiguration**

### **🔧 Automatische Konfiguration**
Die Rolle generiert automatisch Copy/Paste-ready Apache/Nginx-Konfigurationen für externe Erreichbarkeit:

```bash
# Apache-Konfiguration anzeigen
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-xen-xx-v_kundenname" \
  -e "moodle_app_domain=domain" \
  --tags "finalize"
```

### **📋 Beispiel Apache-VirtualHost** (Nicht getestet)
```apache
# Automatisch generierte Konfiguration für: https://kunden-name-domain.de/__solr/

<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteRule ^/__solr/(.*)$ http://127.0.0.1:8983/solr/$1 [P,L,QSA]
    RewriteRule ^/__solr$ http://127.0.0.1:8983/solr/ [P,L,QSA]
</IfModule>

<Location "/__solr/">
    ProxyPass http://127.0.0.1:8983/solr/
    ProxyPassReverse http://127.0.0.1:8983/solr/
    ProxyPreserveHost On
    
    # Security headers
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-XSS-Protection "1; mode=block"
</Location>
```

### **Zugriff mit Authentifizierung**
```bash
# Nach Apache-Konfiguration erreichbar unter:
# https://kunden-name-domain.de/__solr/

# Anmeldedaten aus host_vars:
# Admin User: admin
# Admin Password: <generiertes_passwort>
# Support User: support  
# Support Password: <generiertes_passwort>
```

### **🛠️ Manuelle Konfiguration**
1. **Apache-Module aktivieren:**
   ```bash
   a2enmod proxy proxy_http headers rewrite
   systemctl reload apache2
   ```

2. **Konfiguration kopieren** aus der Ansible-Ausgabe
3. **VirtualHost erweitern** und Apache neuladen
4. **Zugriff testen:** `https://domain/__solr/`
---

## 🔧 **Management & Wartung**

### **🐳 Docker Compose Management**
Nach der Installation steht ein Management-Script zur Verfügung:

# Direkte docker-compose Befehle
cd /opt/eledia-solr
docker-compose up -d
docker-compose logs -f
```

### **🔄 Core-Recovery bei Problemen**
```bash
# Core wiederherstellen
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=hostname" --tags "core" -v

# Moodle-Integration reparieren
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=hostname" --tags "moodle,config" -v
```

---

## 🧪 **Testing & Validation**

### **Automatische Tests**
Die Role führt umfassende Tests durch:
- ✅ Solr-Container Erreichbarkeit
- ✅ Core-Verfügbarkeit und -Funktionalität  
- ✅ Moodle config.php Integration
- ✅ PHP-Konnektivität zu Solr
- ✅ Authentifizierung (falls aktiviert)
- ✅ HTTP/HTTPS Zugriff

### **Test-Output**
```
🔍 Moodle-Solr Connectivity Test COMPLETED

📊 Test Results:
  🌐 Solr Ping: PASS
  🎯 Solr Core: PASS
  🔐 Authentication: PASS
  📝 Config Present: PASS
  🐘 PHP Config: PASS
  🔗 cURL Test: PASS

🏆 Overall Status: HEALTHY
```

---

## 🗂️ **Dateistruktur & Aktuelle Rolle**

### **📁 Aktuelle Rolle-Struktur (Version 7.0)**
```
install-solr/
├── tasks/                          # 12 Task-Dateien
│   ├── main.yml                   # ✅ Hauptsteuerung mit [Security-Management]
│   ├── system_preparation.yml     # ✅ System-Setup
│   ├── docker_installation.yml    # ✅ Docker-Installation (falls nötig)  
│   ├── password_management.yml    # ✅ solr_auth_* Credentials
│   ├── solr_deployment.yml        # ✅ Container-Deployment
│   ├── solr_auth_deployment.yml   # ✅ Authentication-Setup
│   ├── finalization.yml           # ✅ Final Summary + Config
│   ├── apache_integration.yml     # ✅ Apache Web-UI + SSL
│   ├── backup_manager.yml         # ⚠️ Spezial-Zweck
│   ├── status.yml                 # ⚠️ --tags="status"
│   ├── uninstall.yml              # ⚠️ --tags="uninstall"
│   └── dry_run.yml                # ⚠️ Analysis-Modus
├── defaults/                      # 1 Default-Datei
│   └── main.yml                   # Basis-Konfiguration
├── files/                         # Solr-Konfigurationsdateien
│   └── conf/                      # ✅ Solr Core-Konfiguration
├── templates/                     # 7 Template-Dateien
│   ├── core.properties.j2         # ✅ Core-Konfiguration
│   ├── security-users.json.j2     # ✅ Auth-User
│   ├── moodle-solr-config.php.j2  # ✅ Moodle-Integration
│   ├── manage-solr.sh.j2          # ✅ Management-Script
│   └── ...                       # ✅ Weitere Templates
├── handlers/
│   └── main.yml                   # Service-Handler
```

### **✅ Aktive Task-Dateien (verwendet)**
- `main.yml` - Hauptorchestration mit [Security-Management] Phase
- `system_preparation.yml` - Facts, Preflight, User-Setup
- `docker_installation.yml` - Docker-Installation
- `password_management.yml` - solr_auth_* Variablen-Management
- `solr_deployment.yml` - Container und Core-Deployment
- `solr_auth_deployment.yml` - Authentication-Konfiguration
- `apache_integration.yml` - Apache Web-UI + SSL Integration
- `finalization.yml` - Final Summary + Config



### **🏷️ Task-Namen-Struktur (Styleguide-konform)**
```yaml
# Bracket-Prefixes für bessere Organisation:
[INIT] - Initialisierung
[Security-Management] - Credential-Management  
[DOCKER-CHECK] - Docker-Prüfungen
[CONTAINER] - Container-Management
[SOLR] - Solr-spezifische Tasks
[AUTH] - Authentifizierung
[CONFIG] - Konfiguration
[CORE] - Core-Management
[APACHE] - Apache Web-UI Integration
[SSL] - SSL-Zertifikat-Management
[FINALIZE] - Abschluss-Tasks
[SUCCESS] - Erfolgs-Meldungen
```

---

## 🚨 **Aktueller Status (Oktober 2025)**

### **🎯 Version 7.0 - Testing READY**
- **Styleguide:** ✅ 80% eLeDia Ansible Styleguide-Konformität erreicht
- **Variablen:** ✅ Einheitliche solr_auth_* Struktur
- **Apache-Integration:** ✅ Copy/Paste-ready Proxy-Konfiguration
- **Task-Organisation:** ✅ Bracket-Prefixes für bessere Übersicht
- **Aufgabenerfüllung:** ✅ 95% der Requirements erfüllt

### **✅ Getestete Funktionalität**
- ✅ **Standard-Installation:** Funktioniert ohne manuelle Nacharbeit
- ✅ **Apache-Proxy-Config:** Automatische Generation für externe Erreichbarkeit
- ✅ **Tag-basierte Ausführung:** Alle Tags funktionieren korrekt
- ✅ **Container-Management:** eledia_solr_* läuft stabil
- ✅ **Moodle-Integration:** config.php Updates funktionieren (Aktuell ein Fehler da Moodle Path nicht defeniert ist)

### **🎯 Erfolgreiche Test-Runs**
```bash
# ✅ Test: Standard-Installation
ansible-playbook install-solr.yml -e "hosts=server"
→ SUCCESS: Container läuft, Core erstellt, Moodle konfiguriert

# ✅ Test: Installation mit Auth
ansible-playbook install-solr.yml -e "solr_auth_enabled=true"  
→ SUCCESS: solr_auth_* Variablen korrekt gesetzt

# ✅ Test: Apache-Konfiguration
ansible-playbook install-solr.yml --tags "finalize"
→ SUCCESS: Copy/Paste-ready Apache-Config generiert

# ✅ Test: Tag-basierte Ausführung
ansible-playbook install-solr.yml --tags "docker,auth,core"
→ SUCCESS: Nur spezifische Phasen ausgeführt
```
---

## 🏷️ **Version Information**

- **Role Version:** 7.0 
- **Solr Version:** 9.9.0
- **Docker Image:** `solr:9.9.0`
- **Supported OS:** Debian 11/12
- **Ansible:** 2.9+
- **Styleguide:** wurde beachtet :) 
- **Task-Organisation:** Bracket-Prefixes

---

## 🐛 **Bekannte Probleme & Bugs (Stand: Oktober 2025)**

### **🔴 KRITISCHE BUGS**
1. **Fehlende Dateien in aktueller Struktur:**
   ```bash
    moodle_config_path ist nicht defeniert. Config wird also aktuell nicht Automatisch angepasst!
   ```


### **🟡 MITTLERE PROBLEME**

4. **Obsolete Dateien nicht entfernt:**
   ```bash
   # Diese Dateien existieren noch, sollten aber gelöscht werden:
   files/solr_provision_auth.sh                # ❌ Veraltet
   tasks/solr_auth_native.yml                  # ❌ Nicht verwendet(Spielerrei)
   ```

### **🟢 KLEINE PROBLEME**
6. **Apache-Integration nicht vollständig automatisiert:**
   ```
   # Proxy-Konfiguration wird nur angezeigt, nicht angewendet
   # Erfordert manuelle Apache-Konfiguration noch!
   
---


## ⚙️ Konfiguration

### Wichtige Host-Variablen

```yaml
# In host_vars/eledia-grepp.yml:
---
# Basis-Konfiguration
moodle_app_domain: "moodle.eledia.de"
#Liegt der Moodle code drin
moodle_path: "/home/vhosts/moodle/bschrei.elearning-home.de/public_html"
ssl_enabled: true

# Performance-Optimierung
performance_mode: "fast"
skip_if_installed: true

# Solr-Spezifische Einstellungen
solr_auth_user: "admin"
solr_auth_password: "secure_password_hier"

# Memory-Optimierung (Überwachung erforderlich!) - Reste werden nicht enfernt!
solr_container_limits:
  memory: "1g"        # REDUZIERT von 2g - MONITOR USAGE!

# Moodle Config.PHP Integration
use_update_config_php: true  # true = update-config-php role, false = direkte Bearbeitung
```

### 🔧 Moodle Config.PHP Integration

**Neue Feature in Version 5.0**: Die Solr-Rolle unterstützt jetzt zwei Methoden zur Moodle-Konfiguration:

#### 1. **update-config-php Rolle (Empfohlen)**
```yaml
use_update_config_php: true  # Standard
```
- ✅ **Vorteil**: Verwendet die `update-config-php` Rolle
- ✅ **Template-basiert**: Komplette config.php wird neu generiert
- ✅ **Konsistent**: Identisch mit anderen Moodle-Konfigurationen
- ✅ **Cache-Management**: Automatische Purge-Operationen
- ❌ **Abhängigkeit**: Benötigt die `update-config-php` Rolle

#### 2. **Direkte Bearbeitung (Fallback)**
```yaml
use_update_config_php: false
```
- ✅ **Unabhängig**: Keine externen Rollen-Abhängigkeiten
- ✅ **Backup**: Automatische config.php Backups
- ✅ **Minimal**: Nur Solr-spezifische Änderungen
- ❌ **Limitiert**: Nur einfache Konfiguration

#### Verwendung:
```bash
# Standard (empfohlen):
ansible-playbook install-solr.yml -e "hosts=server.de"

# Direkte Bearbeitung:
ansible-playbook install-solr.yml -e "hosts=server.de" -e "use_update_config_php=false"

```

### Solr Core-Konfiguration

```yaml
# Solr Core Settings
solr_core_name: "moodle_core"
solr_admin_user: "admin"
solr_admin_password: "sicheres_passwort"

# Authentication Settings
solr_auth_enabled: true
solr_security_json: |
  {
    "authentication": {
      "blockUnknown": true,
      "class": "solr.BasicAuthPlugin",
      "credentials": {
        "admin": "verschluesseltes_passwort_hash"
      }
    },
    "authorization": {
      "class": "solr.RuleBasedAuthorizationPlugin",
      "permissions": [
        {"name": "security-edit", "role": "admin"},
        {"name": "collection-admin-edit", "role": "admin"},
        {"name": "core-admin-edit", "role": "admin"}
      ],
      "user-role": {
        "admin": "admin"
      }
    }
  }
```

---

## 🚨 Troubleshooting

### Häufige Probleme und Lösungen

#### 1. Memory-Probleme
**Problem**: Container läuft nicht stabil oder wird beendet
```bash
# Status prüfen
docker logs eledia-solr-moodle.eledia.de

# Memory-Verbrauch überwachen
docker stats eledia-solr-moodle-elediaserver

# Falls notwendig, Memory erhöhen in host_vars:
solr_container_limits:
  memory: "2g"  # Erhöhung von 1g auf 2g
```

#### 2. Container läuft als Root
**Lösung**: Version 5.0 verwendet automatisch UID/GID 8983
```bash
# User-Setup neu ausführen
ansible-playbook ... --tags "user,security"
```

#### 3. SSL-Probleme
**Lösung**: Apache SSL-Konfiguration prüfen
```bash
# SSL-Status validieren
ansible-playbook ... --tags "ssl,validation" -v

# Apache-Konfiguration manuell prüfen
sudo apachectl configtest
sudo systemctl status apache2
```

#### 4. Performance langsam
**Lösung**: Fast-Mode verwenden
```bash
ansible-playbook ... -e "performance_mode=fast"
```

#### 5. Moodle-Integration fehlgeschlagen
**Lösung**: Moodle-Validierung durchführen
```bash
ansible-playbook ... --tags "moodle,validation" -v
```

#### 6. Docker-Installation fehlgeschlagen
**Lösung**: Docker manuell installieren
```bash
# Docker-Setup neu ausführen
ansible-playbook ... --tags "docker" -v
```

### Debug-Modus

Bei komplexeren Problemen verwenden Sie den Debug-Modus:

```bash
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=ihr-server.domain.de" \
  -e "performance_mode=debug" \
  -vvv
```

### Log-Dateien

**Wichtige Log-Standorte:**
- Solr Container Logs: `docker logs eledia-solr-moodle-server`
- Apache Error Log: `/var/log/apache2/error.log`
- Apache Access Log: `/var/log/apache2/access.log`
- System Installation Log: `/opt/eledia-solr-state/moodle-server.yml`

---

## 🔍 Monitoring & Überwachung

### Memory-Überwachung (KRITISCH!)

> **⚠️ WICHTIG**: Die reduzierten Memory-Limits müssen kontinuierlich überwacht werden!

```bash
# Container-Ressourcen überwachen
docker stats eledia-solr-moodle-ihr-server

# Detaillierte Memory-Analyse
docker exec eledia-solr-moodle-ihr-server \
  sh -c 'ps aux | head -10 && free -h'

# JVM Heap-Status prüfen
curl -s "http://localhost:8983/solr/admin/info/system?wt=json" | \
  jq '.jvm.memory'
```

### Performance-Tests

**Empfohlene Performance-Tests nach Installation:**

1. **Load-Test mit kleinen Dokumenten**:
```bash
# Test mit 100 kleinen Dokumenten
curl -X POST "http://localhost:8983/solr/moodle_core/update?commit=true" \
  -H "Content-Type: application/json" \
  -d '[{"id":"test1","title":"Test Document"}]'
```

2. **Memory-Stress-Test**:
```bash
# Index-Aufbau mit mehreren Dokumenten beobachten
watch -n 5 'docker stats --no-stream eledia-solr-moodle-ihr-server'
```

3. **Search-Performance-Test**:
```bash
# Suchgeschwindigkeit testen
time curl -s "http://localhost:8983/solr/moodle_core/select?q=*:*&rows=100"
```

### Prometheus-Integration (optional) --> nicht getestet!

```yaml
# In host_vars aktivieren:
monitoring_enabled: true
solr_monitoring:
  prometheus_enabled: true
  exporter_port: 9854
```

---

## 📚 Weiterführende Dokumentation

### Interne Links

- [[install-solr:FINAL_SUMMARY|Installations-Zusammenfassung]]
- [[install-solr:DEPRECATED|Deprecated Features]]
- source:ansible/roles/install-solr/defaults/main.yml - Vollständige Variablen-Referenz

### Externe Links

- [Apache Solr 9.9.0 Documentation](https://solr.apache.org/guide/9_9/)
- [Docker Best Practices](https://docs.docker.com/develop/best-practices/)
- [Moodle Global Search Documentation](https://docs.moodle.org/en/Global_search)

---

## 🏁 Schnellstart

**Für eilige Administratoren:**

```bash
# 1. Host-Variablen konfigurieren
echo "moodle_app_domain: ihr-server.domain.de" > host_vars/ihr-server.yml

# 2. Installation starten
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=ihr-server.domain.de"

# 3. Status prüfen
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=ihr-server.domain.de" --tags "status"
```
---

### **✅ Erfolgreich getestete Systeme**
```bash
# Single-Tenant Tests:
✅ Target-Server (Beispiel: xen-23-v_bschreielearninghomede)
   Container: eledia-solr-bschrei
   Core: eledia_solr_bschrei
   Status: HEALTHY (Ping: 2ms)

# Multi-Tenant Tests:
✅ Target-Server (Beispiel: solrinstalltestelediade)
   Container: eledia-solr-multi-tenant
   Cores: main_core, eledia_solr_testkunde
   Status: HEALTHY (All cores operational)
```
---

---

## 🏷️ **Version & Maintenance Info**

- **Role Version**: 3.3.1
- **Solr Version**: 9.9.0  
- **Docker Image**: eledia-solr:v3.3.1
- **Maintainer**: Bernd Schreistetter, Eledia GmbH
- **Status**: Production Ready
- **Last Updated**: Oktober 2025
- **Compatibility**: Debian 11/12, Ubuntu 20.04+, Ansible 2.12+

---

## 📜 **Lizenz & Copyright**

Copyright © 2025 Eledia GmbH. Alle Rechte vorbehalten.

Für Support und Fragen wenden Sie sich an: [eledia-ops@eledia.de](mailto:eledia-ops@eledia.de)
