# Ansible Multi-Role Engine

Ein modulares Ansible-Playbook zur Server-Konfiguration, Sicherheitsüberprüfung und -härtung.

## Funktionen

Das Playbook umfasst acht Rollen:

1. **audit**: Erfasst System-Facts (Hostname, IP, OS, Hardware, Dateisysteme, Pakete)
2. **update**: Führt System- und Sicherheitsupdates durch
3. **base**: Richtet grundlegende Systemdienste und -konfigurationen ein
4. **web_service**: Installiert und konfiguriert Webserver (Nginx oder Apache)
5. **docker_host**: Installiert Docker und konfiguriert Docker-Nutzer
6. **security_checks**: Führt Sicherheitsüberprüfungen durch
7. **hardening**: Wendet CIS-Benchmark-Empfehlungen an
8. **security_tools**: Installiert und konfiguriert Sicherheitstools (fail2ban, PortSentry, psad)

## Voraussetzungen

- Ansible 2.9+
- SSH-Zugriff auf Zielserver
- Sudo-Rechte auf Zielserver

## Struktur

```
ansible-multi-role-engine/
├── inventory.ini         # Hostkonfiguration
├── site.yml              # Hauptplaybook
├── group_vars/           # Variablen für alle Hosts
│   └── all.yml
└── roles/                # Die 8 Hauptrollen
    ├── audit/
    ├── update/
    ├── base/
    ├── web_service/
    ├── docker_host/
    ├── security_checks/
    ├── hardening/
    └── security_tools/   # Neue Rolle für Sicherheitstools
```

## Verwendung

### Grundlegende Verwendung

```bash
# Alle Rollen ausführen
ansible-playbook -i inventory.ini site.yml

# Spezifische Rolle(n) mit Tags ausführen
ansible-playbook -i inventory.ini site.yml --tags audit,harden

# Dry-Run (Änderungen werden nicht angewendet)
ansible-playbook -i inventory.ini site.yml --check
```

### Verfügbare Tags

- `audit`: System-Informationen erfassen
- `update`: System-Updates durchführen
- `base`, `setup`: Basis-Setup (NTP, Benutzer, SSH)
- `web`, `services`: Webserver-Konfiguration
- `docker`, `services`: Docker-Host-Konfiguration
- `security`: Sicherheitsüberprüfungen
- `harden`: System härten nach CIS-Benchmarks
- `tools`: Sicherheitstools installieren und konfigurieren

### Auf bestimmte Hosts beschränken

```bash
# Nur auf Webservern ausführen
ansible-playbook -i inventory.ini site.yml --limit webservers

# Nur auf Docker-Hosts ausführen
ansible-playbook -i inventory.ini site.yml --limit dockerhosts
```

## Anpassung

Die zentralen Konfigurationen befinden sich in `group_vars/all.yml`. Hier können Sie folgende Einstellungen anpassen:

- NTP-Server
- Benutzerkonten und SSH-Keys
- SSH-Konfiguration (Port, Protokoll, etc.)
- Webserver-Typ (Nginx/Apache)
- Docker-Benutzer
- Sicherheitsscanning-Parameter
- Härtungsoptionen
- Sicherheitstools-Konfiguration (fail2ban, PortSentry, psad)

## Unterstützte Betriebssysteme

- Debian/Ubuntu
- RedHat/CentOS

## Sicherheit

Das Playbook führt mehrere Sicherheitsmaßnahmen durch:
- Deaktivierung nicht benötigter Dienste
- Einrichtung sicherer SSH-Konfiguration
- Dateisystem-Härtung
- Netzwerkhärtung via sysctl
- SELinux/AppArmor-Konfiguration
- Sichere Passwortrichtlinien
- Installation und Konfiguration von Sicherheitstools:
  - fail2ban: Schutz vor Brute-Force-Angriffen
  - PortSentry: Erkennung und Blockierung von Port-Scans
  - psad: Angriffserkennung basierend auf Firewall-Logs

## Erweiterung

Um eine neue Rolle hinzuzufügen:

1. Erstellen Sie ein neues Verzeichnis unter `roles/`
2. Erstellen Sie die Verzeichnisstruktur (tasks, handlers, defaults, etc.)
3. Fügen Sie die Rolle in `site.yml` hinzu

## Fehlerbehebung

- Prüfen Sie die Verbindung mit `ansible -i inventory.ini all -m ping`
- Verwenden Sie `--verbose` für detaillierte Ausgaben
- Führen Sie einen Dry-Run mit `--check` durch, um Änderungen zu prüfen 