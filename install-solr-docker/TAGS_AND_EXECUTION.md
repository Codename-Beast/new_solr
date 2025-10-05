# 🏷️ Solr Role - Tags & Execution Guide

## 📋 **Verfügbare Tags**

### 🔧 **Hauptinstallations-Tags**
```bash
# Vollständige Installation (Standard)
ansible-playbook install-solr.yml -i inventory/ -e "hosts=hostname"

# Nur bestimmte Phasen
ansible-playbook install-solr.yml -i inventory/ -e "hosts=hostname" --tags "facts,detection"
ansible-playbook install-solr.yml -i inventory/ -e "hosts=hostname" --tags "docker,install"
ansible-playbook install-solr.yml -i inventory/ -e "hosts=hostname" --tags "core,moodle"
```

### 🏷️ **Tag-Kategorien**

#### **System-Tags**
- `facts` - Systemfakten sammeln
- `detection` - Vorhandene Installation erkennen
- `preflight` - Systemvoraussetzungen prüfen
- `packages` - Pakete installieren

#### **Docker-Tags**
- `docker` - Docker Engine installieren
- `docker-install` - Docker-Komponenten
- `containers` - Container-Management

#### **Solr-Tags**
- `solr-install` - Solr-Installation
- `core` - Core-Management
- `auth` - Authentifizierung
- `ssl` - SSL-Konfiguration

#### **Integration-Tags**
- `moodle` - Moodle-Integration
- `config` - Konfiguration
- `validation` - Tests und Validierung

#### **Wartungs-Tags**
- `status` - Status-Check
- `health` - Health-Check
- `monitoring` - Monitoring-Setup
- `backup` - Backup-Funktionen

#### **Spezial-Tags**
- `dry-run` - Simulation ohne Änderungen
- `rollback` - System-Rollback
- `uninstall` - Deinstallation

## 🎯 **Häufige Execution-Patterns**

### **🚀 Neue Installation**
```bash
# Standard-Installation
cd /home/bschrei
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" -v

# Mit DNS-Skip (bei Problemen)
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --skip-tags dns -v
```

### **🔍 System-Check ohne Installation**
```bash
# Nur Fakten sammeln
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "facts,detection" -v

# Status prüfen
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "status" -v

# Health-Check
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "health" -v
```

### **🐳 Docker-Only Installation**
```bash
# Nur Docker installieren
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "docker" -v

# Docker + Container
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "docker,containers" -v
```

### **⚡ Partial Execution (ab bestimmtem Task)**
```bash
# Ab Docker-Installation starten
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --start-at-task="Install Docker Python SDK" -v

# Ab Container-Start
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --start-at-task="Start Solr container" -v

# Ab Core-Management
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --start-at-task="[CORE] Check if Solr core exists" -v
```

### **🧪 Testing & Simulation**
```bash
# Dry-Run (keine Änderungen)
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" -e "solr_dry_run_mode=true" --tags "dry-run" -v

# Nur Moodle-Tests
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "validation" -v

# Check-Mode (Ansible built-in)
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --check -v
```

## 🛠️ **Troubleshooting Commands**

### **🔧 Reparatur-Befehle**
```bash
# Core-Recovery
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "core" -v

# Moodle-Integration reparieren
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "moodle,config" -v

# Authentifizierung zurücksetzen
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "auth" -v
```

### **🗑️ Cleanup & Deinstallation**
```bash
# Vollständige Deinstallation
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "uninstall" -v

# System-Rollback
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --tags "rollback" -v
```

## 🎭 **Tag-Kombinationen**

### **Häufige Kombinationen**
```bash
# System + Docker
--tags "facts,detection,docker"

# Core + Moodle
--tags "core,moodle,validation"

# Monitoring + Health
--tags "monitoring,health,status"

# Installation ohne Tests
--skip-tags "validation,health"

# Schnelle Installation (ohne Monitoring)
--skip-tags "monitoring,backup"
```

## 🚨 **Wichtige Hinweise**

### **⚠️ Reihenfolge beachten**
- `facts` sollte immer zuerst laufen
- `docker` vor `containers`
- `core` vor `moodle`
- `validation` am Ende

### **🔄 Wiederholbare Tags**
Diese Tags können sicher mehrfach ausgeführt werden:
- `status`, `health`, `validation`
- `facts`, `detection`
- `monitoring`, `backup`

### **⚡ Ein-mal-Tags**
Diese sollten nur einmal pro System laufen:
- `packages`, `docker-install`
- `ssl` (bei bereits konfigurierten Zertifikaten)

## 📊 **Performance-Tipps**

### **🚀 Schnelle Ausführung**
```bash
# Ohne Verbose für schnellere Ausführung
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede"

# Parallel auf mehreren Hosts
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=solr_group" --forks=5
```

### **🔍 Debug-Modus**
```bash
# Maximaler Debug-Output
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" -vvv

# Mit Diff-Output
ansible-playbook -i ansible-inventory/prod/hosts ansible/install-solr.yml \
  -e "hosts=xen-23-v_bschreielearninghomede" --diff -v
```
