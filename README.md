# Cisco CLI Cheatsheet
Erklärt die Grundlagen und Abläufe der Konfiguration über die Cisco CLI. Manche Befehle sind nur für Router bzw Switches.

Bei Befehlen stehen Wörter in `[]` für optionale Argumente und `<>` für Pflichtargumente. `<IP-Adresse>` steht also für eine IP Adresse die eingetragen werden soll.

## Basics

### Modi
Am Anfang einer Zeile wird immer der Hosname und der aktuelle Modus angezeigt. Es gibt folgende Modi:
- User Mode
 
        Hostname>

- Privileged Mode

        Hostname#

    - Globaler Konfigurationsmodus

            Hostname (config)#

        - Interface Konfigurationsmodus

                Hostname (config-if)#

        - Routing Engine Konfigurationsmodus

                Hostname (config-router)#

        - Line Konfigurationsmodus

                Hostname (config-line)#

- Wechseln von User-Mode in Exec-Mode:

        Hostname> enable
        Hostname#

- Wechseln in den Konfigurationsmodus:

        Hostname# configure terminal

- Wechseln in den nächstniedrigeren Modus (z.B. `config-if` in `config` oder Privileged in User):

        Hostname# exit
        Hostname>

- Von allen Konfigurationsmodi in den Privilegierten Modus:

        Hostname (config-if)# end
        Hostname#

### Anzeige
Um sich verschiedene Informationen anzeigen lassen zu können werden die `show` Befehle genutzt:

- Anzeigen der genauen Hardwareeigenschaften wie Anzahl der Pakete, Fehlerrate, usw...

        Hostname# show interfaces

- Anzeigen von Interface IP Adressen und Status. `brief` lässt einige Informationen weg und formatiert die Ausgabe als übersichtliche Tabelle

        Hostname# show ip interface [brief]

- Anzeigen der Version und Eigenschaften des Systems

        Hostname> show version

### Konfiguration
#### Erstellen
Fast alle Konfigurationen sind so aufgebaut, dass im ersten Wort festgelegt wird was konfiguriert wird und anschließend der Parameter eingegeben wird.

Beispiel für die Konfiguration des Hostnames:

        Hostname> enable
        Hostname# configure terminal
        Hostname (config)# hostname asdfasdf
        asdfasdf (config)#

#### Löschen
Konfiguration werden gelöscht indem vor dem Konfigurations Schlüsselwort ein `no` gesetzt wird

Löschen des Hostnames:

        asdfasdf (config)# no hostname
        Switch (config) #

Entfernen der IP-Adresse auf einem Interface:

        Router (config-if)# no ip address
        Router (config-if)#

## IP Adresse zuweisen
Die IP Adressen Zuweisung ist primär für Router gedacht, geht aber für z.B. Management Gründe auch bei Switches, ist aber da Switches ein Layer-2 Gerät sind dort ein bisschen anders.

### Router

        Hostname> enable
        Hostname# configure terminal
        Hostname (config)# interface Gigabit0/1
        Hostname (config-if)# ip address <IP-Adresse> <Subnetz-Maske>
        Hostname (config-if)# no shutdown
        Hostname (config-if)# end

### Switch

        Hostname> enable
        Hostname# configure terminal
        Hostname (config)# interface vlan 1
        Hostname (config-if)# ip address <IP-Adresse> <Subnetz-Maske>
        Hostname (config-if)# no shutdown
        Hostname (config-if)# end

Falls die IP Adresse über DHCP bezogen werden soll kann statt IP Adresse und Subnetzmaske auch `ip address dhcp` genutzt werden.

## VLANS

VLANS können als IDs die Zahlen von 1-4094 vergeben werden. VLAN 0 und 4095 sind reserviert und 1 ist das Standard VLAN. Es sollte davon abgesehen werden die VLANs 1002-1005 zu verwenden, da diese für Legacy Kram wie Token Ring genutzt werden.

### VLAN erstellen

        Switch (config)# vlan <id>
        [Switch (config)# name <VLAN-Name>]

### VLAN Port zuweisen

        Switch (config-if)# switchport mode access
        Switch (config-if)# switchport access vlan <VLAN-ID>

### Port als Trunk konfigurieren

        Switch (config-if)# switchport mode trunk

### VLANs zu Trunk hinzufügen/entfernen

        Switch (config-if)# switchport trunk allowed vlan [add/remove] <VLAN-IDs>

### Router-on-a-stick konfigurieren
Folgende Konfiguration muss für jedes VLAN, das auf dem Interface anliegen soll vorgenommen werden:

        Router (config)# interface <interface>.<VLAN-ID>
        Router (config-subif)# encapsulation dot1q <VLAN-ID>
        Router (config-subif)# ip address <IP-Adresse> <Subnetz-Maske>
