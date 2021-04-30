# Bachelorarbeit: Performanceanalyse einer Meltdown-Mitigation (KPTI)
## Inhaltsverzeichnis README
1. Beschreibung
2. Installation und Verwendung
3. Datei- und Ordnerstruktur


### 1. Beschreibung
Diese Bachelorarbeit beschäftigt sich mit der Performanceanalyse der Meltdown Mitigation
Kernel Page Table Isolation auf einem Linux-Betriebssystem. Die Performanceanalyse wurde
auf verschiedenen Testgeräten mit Intel-Prozessoren und einem Ubuntu 20.04 Betriebssystem
durchgeführt. Dazu wurde in der Programmiersprache C ein Analysetool geschrieben. Genauere
Details zur Konzeption des Analysetools können dem beiliegenden Dokument zur Bachelorarbeit
entnommen werden.

Die Verwendung des Analysetools wird in Abschnitt 1.2 erläutert. In Abschnitt 1.3 wird die
zugrunde liegende Ordnerstruktur aufgelistet und auf den Speicherort der mitgelieferten Daten
der Evaluationsergebnisse eingegangen.

Die vorliegende Arbeit wurde für die Verwendung auf oben genannten Systemen optimiert, sodass
für eigene reibungsfreie Analysen ein möglichst ähnliches Setup verwendet werden sollte.

### 2. Installation und Verwendung

Das Skript `install.sh` installiert alle benötigten Programme (make, gnuplot, clang und cpuid),
legt den noch benötigten Ordner für Objektdateien an und gibt Ausführungsrechte für zwei weitere
Dateien, welche die Veränderung von Bootparametern vornehmen. Zudem wird durch Aufruf des
beigefügten Makefiles der Programmcode kompiliert. Nach der Ausführung dieses Shell-Skripts
sind somit alle nötigen Voraussetzungen für das Starten des Analyseprogramms erfüllt. Falls die
Datei `install.sh` nicht bereits die nötigen Berechtigungen zur Ausführung enthält, können diese
mit `$ chmod +x install.sh` vergeben werden. Das Shell-Skript wird durch folgenden Aufruf
ausgeführt: `$ ./install.sh`

Folgende Make Befehle sind verfügbar:

* **make** &rarr; Gesamtes Projekt kompilieren
* **make analyse** &rarr; Analyseprogramm zur Messung ausführen
* **make eval** &rarr; Evaluationsprogramm zur Aufbereitung der Messergebnisse ausführen
* **make kpti** &rarr; KPTI einschalten und neustarten
* **make nopti** &rarr; KPTI ausschalten und neustarten
* **make clean** &rarr; Ausführbare- und Objektdateien löschen
* **make clean-res** &rarr; Alle Messergebnisse des aktuellen Geräts löschen

Es folgt das empfohlene Vorgehen zur Verwendung des Analyseprogramms:
1. Zuerst wird das Analyseprogramm ausgeführt, um Messungen zur Performance von KPTI zu
erhalten. Es wird empfohlen dieses fünf mal hintereinander auszuführen. <br />
`$ make analyse`

2. Voraussichtlich ist auf dem verwendetem Gerät zunächst KPTI aktiviert. Um Messungen ohne
aktivierte Kernel Page Table Isolation durchzuführen,muss der Bootparameter “nopti” gesetzt
werden. Anweisungen zum weiteren Vorgehen, werden jedoch auch vom Analyseprogramm
selbst gegeben. Das Aktualisieren der Boot-Einstellungen kann automatisiert durch den
folgenden Aufruf durchgeführt werden:<br />
`$ make nopti`

3. Da beim Kompilieren relevante Informationen ausgelesen werden, muss nach einem Neustart
das Analyseprogramm vollständig neu kompiliert werden. Dazu sollten zunächst alle
bestehenden Ausführbaren- und Objektdateien gelöscht werden.<br />
`$ make clean`<br />
`$ make`

4. Nun werden die Messungen mit deaktivierter Kernel Page Table Isolation durchgeführt. Es
wird empfohlen wieder fünf Durchläufe zu starten. In jedem Fall sollte mit und ohne KPTI
die gleiche Anzahl an Durchläufen stattfinden.<br />
`$ make analyse`

5. Es liegen nun alle benötigten Messergebnisse vor. Durch Aufruf des Evaluationsprogramms
werden die Messwerte anschaulich in Tabellen und Graphen dargestellt. Zudem findet durch
das Analyseprogramm eine Berechnung von Varianzen und Standardabweichungen der
jeweiligen Messungen statt. Die Performanceanalyse ist damit abgeschlossen.<br />
`$ make eval`



### 3. Datei- und Ordnerstruktur
**/code/**: Enthält Programmcode und alle benötigten Skripte und Dateien.

Analyseprogramm:<br />
* `./analyse/analyse.c`: Programmcode zur Messung der Performance.
* `./analyse/analyse.h`: Header-Datei zum Programmcode zur Messung der Performance.
* `./analyse/analyse-fork.c`: Programmcode zur Messung der Performance des Systemcalls
fork. Wird vom Analyseprogramm aufgerufen.
* `./analyse/testfile.txt`: Textdatei, die beim Aufruf der Systemcalls open, read und close
zur Messung der Performance verwendet wird.
<br />

Evaluationsprogramm:
* `./eval/eval.c`: Programmcode zur Aufbereitung der Messungen.
* `./eval/eval.h`: Header-Datei zum Programmcode zur Aufbereitung der Messungen.
* `./eval/statistics.h`: Header-Datei, welche Programmcode zur Berechnung von Varianz
und Standardabweichung enthält. Wird vom Evaluationsprogramm verwendet.
<br />

Veränderung von Bootparametern:
* `./grub_settings/grub_kpti`: Textdatei, welche die Standard Grub-Konfiguration (mit KPTI)
zum Booten enthält. Wird vom Shell-Skript s_grub_kpti.sh verwendet.
* `./grub_settings/grub_nopti`: Textdatei, welche die Standard Grub-Konfiguration mit
deaktivierter Kernel Page Table Isolation zum Booten enthält. Wird vom Shell-Skript
s_grub_nopti.sh verwendet.
* `./grub_settings/s_grub_kpti.sh`: Shell-Skript, welches die Standard-Boot-Konfiguration
mit KPTI herstellt und das Gerät Neustartet.
* `./grub_settings/s_grub_nopti.sh`: Shell-Skript, welches die Standard-Boot-Konfiguration
ohne KPTI herstellt und das Gerät Neustartet.
<br />

Auslesen von System Spezifikationen:
* `./system_specs/system_specs.c`: Wird vom Analyseprogramm aufgerufen. Enthält Programmcode,
welcher beim Kompilieren gesetzte Makros zu System Informationen ausliest
und für das Schreiben in eine Datei aufbereitet.
* `./system_specs/system_specs.h`: Header-Datei zu system_specs.c

**/results/\<CPU-Modell>/**: Enthält die Ergebnisse der Messungen. Alle Daten werden automatisiert
in einen Ordner, der nach dem CPU-Modell benannt ist, abgelegt.
<br />

System Spezifikationen:
* `./system_specs.txt`: Enthält die ausgelesenen System Spezifikationen des Geräts
Messergebnisse zur Performance von Systemcalls:
* `./measurements-syscalls/measurements_KPTI.csv`: Enthält die Messwerte des letzten Analysedurchlaufs
unter KPTI.
* `./measurements-syscalls/measurements_NOPTI.csv`: Enthält die Messwerte des letzten Analysedurchlaufs
ohne KPTI.
* `./measurements-syscalls/last_five_avg_kpti.csv`: Enthält die Durchschnittsmesswerte
der letzten maximal fünf Analysedurchläufe unter KPTI.
* `./measurements-syscalls/last_five_avg_kpti.csv`: Enthält die Durchschnittsmesswerte
der letzten maximal fünf Analysedurchläufe ohne KPTI.
<br />

Messergebnisse zur TLB Belegung:
* `./measurements-tlb/tlb_measurement_kpti<1-5>.csv`: Enthält die Messwerte des jeweiligen
Analysedurchlaufs unter KPTI.
* `./measurements-tlb/tlb_measurement_nopti<1-5>.csv`: Enthält die Messwerte des jeweiligen
Analysedurchlaufs ohne KPTI.
<br />

Evaluationsergebnisse:
* `./eval/*`: Enthält Graphen und Tabellen zu den Messergebnissen, sowie eine Zusammenfassung
aller einzelnen TLB Messungen jeweils mit und ohne kpti.
* `./eval/statistics/*`: Enthält Auflistungen zu Varianz und Standardabweichungen der TLB
Messungen, den Messwerten zur Performance von Systemcalls des letzten Analysedurchlaufs
und zu den Durchschnittswerten der Systemcall Messungen der letzten maximal fünf
Durchläufe alle jeweils mit und ohne KPTI.
