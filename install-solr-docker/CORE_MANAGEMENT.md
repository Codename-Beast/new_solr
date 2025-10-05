# 🏢 Multi-Tenant Core Management v3.3.1

## 📋 Übersicht

Die **v3.3.1 Rolle** bietet vollständiges **API-basiertes Multi-Tenant Core Management** mit direkten Solr-API-Aufrufen für maximale Sicherheit und Performance - Core-Operations ohne Container-Neustarts.

## ✨ **Neue v3.3.1 Features**

### 🔧 **API-First Architecture**
- 🚀 **Direkte Solr API** - Shell-Commands statt docker_container Module
- 🛡️ **Permission-aware** - Automatische User-Switching für Container-Operationen  
- ⚡ **Zero-Downtime** - Alle Operationen ohne Container-Neustart
- 🔄 **Live Reload** - Neue `reload_solr_core` Variable für Core-Neuladen

### 🎯 **Kern-Funktionen**

#### ✅ **Core Hinzufügen**
```bash
# Neuen Customer Core hinzufügen (Target-Server Beispiel)
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "add_solr_core=kunde.example.com" \
  -e "kunden_moodle_hostvars=/path/to/kunde-hostvars"
```

#### � **Core Reload (NEU!)**
```bash
# Core-Konfiguration neu laden ohne Neustart
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "reload_solr_core=eledia_solr_kunde"
```

#### 🗑️ **Core Entfernen**
```bash
# Customer Core sicher entfernen
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "remove_solr_core=eledia_solr_kunde"

# Force removal ohne Bestätigung (Vorsicht!)
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "remove_solr_core=eledia_solr_kunde" \
  -e "force_core_removal=true"
```

## 🔒 **Sicherheitsfeatures v3.3.1**

### 🛡️ **API-basierte Validierung**
- ✅ **System Type Check**: Automatische Erkennung von Multi-Tenant vs Single-Tenant
- ✅ **Container Health**: Validiert dass Solr Container aktiv und API erreichbar
- ✅ **Core Existence**: Prüft Core-Existenz über Solr API vor Operationen
- ✅ **Permission Management**: Automatisches User-Switching für Container-interne Operationen
- ✅ **XEN Protection**: Multi-Tenant auf xen-* Systemen wird verhindert

### 🔐 **Sichere Core-Operationen**
- ✅ **Direct API Calls**: `curl` und `docker exec` Commands statt docker_container Module
- ✅ **Solr User Context**: Operationen laufen als dedizierter solr-User (UID 8983)
- ✅ **Error Recovery**: Intelligente Fehlerbehandlung mit aussagekräftigen Meldungen
- ✅ **Atomic Operations**: Operationen sind atomar - entweder komplett erfolgreich oder rückgängig

### � **Erweiterte Validierung**
```bash
# System Status mit Core-Details
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  --tags "status"
```

**Status-Output zeigt:**
```
🌐 MULTI-TENANT CORE STATUS:
   Container: eledia-solr-multi-tenant ✅ Running
   API Status: ✅ Responsive (8983)
   
📦 ACTIVE CORES:
   main_core: ✅ Active (5 docs, 2.1MB)
   eledia_solr_testkunde: ✅ Active (12 docs, 4.7MB)
   
� MANAGEMENT READY:
   Add Core: Available
   Remove Core: Available  
   Reload Core: Available
```

## 📋 **Praktische Workflow-Beispiele**

### 🎯 **Scenario 1: Neuer Kunde hinzufügen**
```bash
# 1. Status prüfen
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" --tags "status"

# 2. Neuen Core hinzufügen
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "add_solr_core=newcustomer.example.com" \
  -e "kunden_moodle_hostvars=/path/to/newcustomer-hostvars"

# 3. Core-Health validieren
curl -s "http://localhost:8983/solr/eledia_solr_newcustomer_example_com/admin/ping"
```

### 🔄 **Scenario 2: Core-Konfiguration aktualisieren**
```bash
# 1. Core reload (für Konfigurationsänderungen)
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "reload_solr_core=eledia_solr_kunde"

# 2. Erfolg validieren
curl -s "http://localhost:8983/solr/eledia_solr_kunde/admin/ping" | jq '.status'
```

### 🗑️ **Scenario 3: Kunden-Migration (Entfernung)**
```bash
# 1. Backup erstellen (falls gewünscht)
docker exec eledia-solr-multi-tenant solr create_backup \
  -c eledia_solr_oldcustomer -b customer_migration_backup

# 2. Core sicher entfernen
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" \
  -e "remove_solr_core=eledia_solr_oldcustomer"

# 3. Verifikation
ansible-playbook -i ansible-inventory/hc-moodle/hosts ansible/install-solr.yml \
  -e "hosts=Target-Server" --tags "status"
```

## 🎛️ **Konfigurationsvariablen v3.3.1**

### **In `defaults/main.yml`:**
```yaml
# Multi-Tenant Core Management (v3.3.1)
add_solr_core: ""           # Core hinzufügen: Domain-Name (kunde.example.com)
remove_solr_core: ""        # Core entfernen: Core-Name (eledia_solr_kunde)
reload_solr_core: ""        # Core neu laden: Core-Name
force_core_removal: false   # Ohne Bestätigung entfernen
solr_is_multi_tenant: false # Multi-Tenant Modus aktivieren

# Docker-Container Namen
solr_docker_container_name: "{{ 'eledia-solr-multi-tenant' if solr_is_multi_tenant else 'eledia-solr-' + inventory_hostname_short }}"
solr_docker_image_name: "eledia-solr"
solr_docker_tag: "v3.3.1"
```

### **Host-Vars Beispiel für Multi-Tenant:**
```yaml
# /ansible-inventory/hc-moodle/host_vars/Target-Server
system_type:
  - Solr  # OHNE Moodle!

solr_is_multi_tenant: true
solr_app_domain: "multi-tenant-server"

# Automatisch generierte Container-Namen:
# eledia-solr-multi-tenant
```

### **Host-Vars Beispiel für Single-Tenant:**
```yaml
# /ansible-inventory/prod/host_vars/Target-Server  
system_type:
  - Moodle

moodle_app_domain: "kunde.elearning-home.de"

# Automatisch generierte Container-Namen:
# eledia-solr-kunde (basierend auf Domain)
```

## 🛡️ **Fehlerbehandlung v3.3.1**

### **Permission-Fehler (GELÖST in v3.3.1)**
```
# Vorher (v3.3.0): docker_container Module-Probleme
TASK [CORE-MANAGEMENT] Create core via Docker
fatal: [server]: FAILED! => 
  msg: Docker module connection failed

# Jetzt (v3.3.1): Direct API + Shell Commands
TASK [CORE-MANAGEMENT] Create core via API
✅ SUCCESS: Core 'eledia_solr_kunde' created successfully
```

### **Core existiert nicht**
```
❌ CORE NOT FOUND: 'nonexistent_core'
================================================================
Target Server: Target-Server
Available cores on this Multi-Tenant server:
  - eledia_solr_kunde1 (Active)
  - eledia_solr_kunde2 (Active)

To remove an existing core, use:
ansible-playbook install-solr.yml -e "hosts=Target-Server" -e "remove_solr_core=<existing_core_name>"
```

### **Container nicht verfügbar**
```
TASK [CORE-MANAGEMENT] Validate container availability
fatal: [Target-Server]: FAILED! => 
  msg: Solr container 'eledia-solr-multi-tenant' is not running. Cannot perform core operations.
  
SOLUTION: Check container status
docker ps | grep eledia-solr
```

### **Single-Tenant Fehler**
```
TASK [CORE-MANAGEMENT] Validate Multi-Tenant mode  
fatal: [Target-Server]: FAILED! => 
  msg: Multi-Tenant core management is only available when solr_is_multi_tenant=true
  
SOLUTION: This server is configured for Single-Tenant mode
Use standard installation: ansible-playbook install-solr.yml -e "hosts=Target-Server"
```

## 🔄 **API-Integration Details v3.3.1**

### **Core Creation API Call:**
```bash
# Direkte API-Aufrufe (statt docker_container Module)
docker exec -u solr eledia-solr-multi-tenant solr create_core \
  -c eledia_solr_kunde \
  -d /opt/solr/server/solr/configsets/_default

# Validation
curl -s "http://localhost:8983/solr/eledia_solr_kunde/admin/ping"
```

### **Core Reload API Call:**
```bash
# Core-Konfiguration neu laden
docker exec -u solr eledia-solr-multi-tenant \
  curl -s "http://localhost:8983/solr/admin/cores?action=RELOAD&core=eledia_solr_kunde"
```

### **Core Removal API Call:**
```bash
# Sichere Core-Entfernung
docker exec -u solr eledia-solr-multi-tenant solr delete \
  -c eledia_solr_kunde

# Filesystem cleanup
docker exec -u solr eledia-solr-multi-tenant \
  rm -rf /var/solr/data/eledia_solr_kunde
```

## 🎯 **Best Practices v3.3.1**

### **🔒 Sicherheit**
1. **Immer Status prüfen** vor Core-Operationen
2. **Multi-Tenant Mode validieren** - keine Core-Ops in Single-Tenant
3. **Container Health checken** - API muss erreichbar sein
4. **XEN-Systeme beachten** - Multi-Tenant wird automatisch verhindert

### **📊 Monitoring**
1. **Container Logs überwachen**: `docker logs eledia-solr-multi-tenant`
2. **API Health checks**: `curl localhost:8983/solr/admin/cores?action=STATUS`
3. **Resource Usage**: `docker stats eledia-solr-multi-tenant`

### **🔄 Operations**
1. **reload_solr_core nutzen** für Konfigurationsänderungen
2. **force_core_removal sparsam** einsetzen
3. **Backups vor Entfernung** wenn Daten wichtig sind
4. **Andere Cores testen** nach größeren Änderungen

## 🚨 **Wichtige Hinweise v3.3.1**

- **API-First**: Alle Operationen nutzen direkte Solr API-Calls
- **Permission-aware**: Automatisches User-Switching verhindert Permission-Probleme  
- **Zero-Downtime**: Keine Container-Neustarts bei Core-Operationen
- **Unumkehrbar**: Core-Entfernung kann nicht rückgängig gemacht werden
- **Nur Multi-Tenant**: Core-Management nur bei `solr_is_multi_tenant=true`
- **Testeing Ready**: Getestet auf xen und auf Hetzner Clound! 