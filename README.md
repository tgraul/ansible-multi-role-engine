# Ansible Multi-Role Engine

Ein modulares Ansible-Playbook zur Server-Konfiguration, Sicherheitsüberprüfung und -härtung.

## Funktionen

Das Playbook umfasst acht Rollen:

1. **audit**: Erfasst System-Facts (Hostname, IP, OS, Hardware, Dateisysteme, Pakete)
2. **update**: Führt System- und Sicherheitsupdates durch
3. **base**: Richtet grundlegende Systemdienste ein (Zeitsynchronisation, lokales Logging, Benutzer, SSH)
4. **web_service**: Installiert und konfiguriert Webserver (Nginx oder Apache) mit SSL-Unterstützung
5. **docker_host**: Installiert Docker CE, Docker Compose und konfiguriert Docker-Nutzer
6. **security_checks**: Führt Sicherheitsüberprüfungen durch (offene Ports, Pakete, Benutzer, etc.)
7. **hardening**: Wendet CIS-Benchmark-Empfehlungen für Systemhärtung an
8. **security_tools**: Installiert und konfiguriert Sicherheitstools (fail2ban, PortSentry, psad)

## Voraussetzungen

- Ansible 2.9+
- SSH-Zugriff auf Zielserver
- Sudo-Rechte auf Zielserver
- Python 2.7 oder höher auf den Zielservern

## Struktur

```
ansible-multi-role-engine/
├── inventory.ini         # Hostkonfiguration
├── site.yml              # Hauptplaybook
├── pre_tasks.yml         # Vorbereitende Aufgaben
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
- `web`, `services`: Webserver-Konfiguration (Nginx/Apache)
- `docker`, `services`: Docker-Host-Konfiguration
- `security`: Sicherheitsüberprüfungen und -härtung
- `harden`: System härten nach CIS-Benchmarks
- `tools`: Sicherheitstools installieren und konfigurieren (fail2ban, PortSentry, psad)

### Auf bestimmte Hosts beschränken

```bash
# Nur auf Webservern ausführen
ansible-playbook -i inventory.ini site.yml --limit webservers

# Nur auf Docker-Hosts ausführen
ansible-playbook -i inventory.ini site.yml --limit dockerhosts
```

## Anpassung

Die zentralen Konfigurationen befinden sich in `group_vars/all.yml`. Hier können Sie folgende Einstellungen anpassen:

- Allgemeine Einstellungen (Sicherheitsupdates, IPv6, Neustarts)
- NTP-Server und Zeitzone
- Benutzerkonten und SSH-Schlüssel
- SSH-Konfiguration (Port, Protokoll, etc.)
- Webserver-Typ und -Konfiguration (Nginx/Apache, SSL)
- Docker-Benutzer und Repository-Einstellungen
- Sicherheitsscanning-Parameter
- Härtungsoptionen und CIS-Benchmarks
- Sicherheitstools-Konfiguration (fail2ban, PortSentry, psad)

## Unterstützte Betriebssysteme

- Debian/Ubuntu
- RedHat/CentOS

## Sicherheit

Das Playbook führt mehrere Sicherheitsmaßnahmen durch:
- Deaktivierung nicht benötigter Dienste
- Sichere SSH-Konfiguration (Port-Änderung, Key-Auth)
- Dateisystem-Härtung und Berechtigungen
- Netzwerkhärtung via sysctl
- SELinux/AppArmor-Konfiguration
- Sichere Passwortrichtlinien
- Installation und Konfiguration von Sicherheitstools:
  - fail2ban: Schutz vor Brute-Force-Angriffen
  - PortSentry: Erkennung und Blockierung von Port-Scans
  - psad: Angriffserkennung basierend auf Firewall-Logs

## Pre-flight Checks

Bevor die Rollen ausgeführt werden, führt das Playbook automatisch Vorbereitungsprüfungen durch:
- Prüfung der Ansible-Version
- Prüfung der Python-Version
- Überprüfung der SSH-Verbindung und Sudo-Rechte
- Überprüfung des Betriebssystems
- Warnung bei geringem Festplattenspeicher
- Warnung bei geringem RAM

## Berichte

Während der Ausführung werden verschiedene Berichte erzeugt:
- Audit-Bericht: Systemfacts und -konfiguration
- Netzwerk-Scan-Bericht: Offene Ports und Netzwerkschnittstellen
- Sicherheitsbericht: Veraltete Pakete, kritische Benutzer und Dateien
- Sicherheitstools-Bericht: Status und blockierte IPs

## Erweiterung

Um eine neue Rolle hinzuzufügen:

1. Erstellen Sie ein neues Verzeichnis unter `roles/`
2. Erstellen Sie die Verzeichnisstruktur (tasks, handlers, defaults, templates, etc.)
3. Fügen Sie die Rolle in `site.yml` hinzu

## Fehlerbehebung

- Prüfen Sie die Verbindung mit `ansible -i inventory.ini all -m ping`
- Verwenden Sie `--verbose` für detaillierte Ausgaben
- Führen Sie einen Dry-Run mit `--check` durch, um Änderungen zu prüfen
- Überprüfen Sie die Berichte im Verzeichnis `/tmp/ansible_reports/` 