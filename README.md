# CMake- und CTest-Schummelzettel

Mein Schummelzettel für CMake und CTest für CMake ab Version 3.28.

## Übersicht

```bash
# Verfügbare Workflows auflisten
cmake --workflow --list-presets

# Konfigurieren + Erstellen + Testen (Release)
cmake --workflow --preset release

# Konfigurieren + Erstellen + Testen (Debug)
cmake --workflow --preset debug

# Konfigurieren + Erstellen + Testen (Custom)
cmake --workflow --preset custom

# Nur erstellen
cmake --build --preset release

# Nur testen
ctest --preset release

# Bestimmtes Target erstellen
cmake --build --preset release --target TARGET1

# Detaillierte Build-Ausgabe
cmake --build --preset release --verbose

# Clean-Target ausführen
cmake --build --preset release --target clean

# Kompilierungsdatenbank anzeigen (Pfad an das Build-Verzeichnis anpassen)
cat build/compile_commands.json
```

## 1. Grundlegende CMake-Befehle

| Befehl | Beschreibung |
| --- | --- |
| `cmake --version` | CMake-Version anzeigen |
| `cmake --help` | Allgemeine Hilfe anzeigen |
| `cmake --list-presets` | Configure-Presets auflisten |
| `cmake --workflow --list-presets` | Workflow-Presets auflisten |
| `cmake --build --list-presets` | Build-Presets auflisten |
| `ctest --list-presets` | Test-Presets auflisten |
| `cmake --help-command add_library` | Hilfe zu einem bestimmten CMake-Befehl anzeigen |
| `cmake --help-module FindOpenMP` | Hilfe zu einem CMake-Modul anzeigen |
| `cmake --help-variable CMAKE_BUILD_TYPE` | Hilfe zu einer CMake-Variable anzeigen |
| `cmake --help-property POSITION_INDEPENDENT_CODE` | Hilfe zu einer CMake-Eigenschaft anzeigen |

## 2. Projektkonfiguration

### Ohne Presets

```bash
# Projekt konfigurieren
cmake -S . -B build

# Release konfigurieren
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release

# Debug konfigurieren
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug

# Generator auswählen
cmake -S . -B build -G Ninja

# Compiler festlegen
cmake -S . -B build -DCMAKE_CXX_COMPILER=g++

# Benutzerdefinierte Option setzen
cmake -S . -B build -DOPTION1=ON

# Mehrere Optionen setzen
cmake -S . -B build \
    -DCMAKE_BUILD_TYPE=Release \
    -DOPTION1=ON \
    -DOPTION2=OFF
```

### Mit Presets

```bash
# Release konfigurieren
cmake --preset release

# Debug konfigurieren
cmake --preset debug

# Benutzerdefiniertes Preset konfigurieren
cmake --preset custom
```

### Single-Config- und Multi-Config-Generatoren

`CMAKE_BUILD_TYPE` legt die Konfiguration für Single-Config-Generatoren fest. Bei Multi-Config-Generatoren wird `--config` beim Erstellen und `-C` (oder `--build-config`) beim Ausführen der Tests verwendet.

```bash
# Single-Config-Generatoren (Ninja, Unix Makefiles)
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build

# Zu Release wechseln
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build

# Multi-Config-Generatoren (Ninja Multi-Config)
cmake -S . -B build -G "Ninja Multi-Config"

# Debug erstellen
cmake --build build --config Debug

# Release erstellen (Multi-Config-Generatoren)
cmake --build build --config Release

# Debug-Tests ausführen
ctest --test-dir build -C Debug

# Release-Tests ausführen
ctest --test-dir build -C Release
```

### Vier verschiedene Preset-Typen

Presets unterschiedlicher Typen werden separat definiert. Ein Workflow-Preset verweist auf Configure-, Build- und Test-Presets. Dass diese denselben Namen haben (zB `release`), ist lediglich eine Namenskonvention und keine automatische Verknüpfung.

| Preset-Typ | Befehl |
| --- | --- |
| Configure-Preset | `cmake --preset release` |
| Build-Preset | `cmake --build --preset release` |
| Test-Preset | `ctest --preset release` |
| Workflow-Preset | `cmake --workflow --preset release` |

## 3. Projekterstellung

### Ohne Presets

```bash
# Standard-Target erstellen
cmake --build build

# Standard-Target mit parallelen Jobs erstellen
cmake --build build -j

# Release erstellen (Multi-Config-Generatoren)
cmake --build build --config Release

# Bestimmtes Target erstellen
cmake --build build --target TARGET1

# Mehrere Targets erstellen
cmake --build build --target TARGET1 TARGET2

# Detaillierte Build-Ausgabe
cmake --build build --verbose

# Clean-Target des CMake-Build-Systems ausführen
cmake --build build --target clean

# Verfügbare Build-Targets auflisten
cmake --build build --target help
```

### Mit Build-Presets

```bash
cmake --build --preset release
cmake --build --preset debug
cmake --build --preset custom
```

## 4. Testen mit CTest

### Ohne Presets

```bash
# Alle Tests ausführen
ctest --test-dir build

# Tests mit ausführlicher Ausgabe ausführen
ctest --test-dir build -V

# Ausgabe nur bei fehlgeschlagenen Tests anzeigen
ctest --test-dir build --output-on-failure

# Tests parallel ausführen
ctest --test-dir build -j

# Tests mit einem bestimmten Label ausführen
ctest --test-dir build -L "unit"

# Tests mit einem bestimmten Label ausschließen
ctest --test-dir build -LE "slow"

# Tests ausführen, deren Namen einem regulären Ausdruck entsprechen
ctest --test-dir build -R "unit"

# Tests ausschließen, deren Namen einem regulären Ausdruck entsprechen
ctest --test-dir build -E "slow"

# Nur fehlgeschlagene Tests des vorherigen Durchlaufs erneut ausführen
ctest --test-dir build --rerun-failed

# Registrierte Tests anzeigen
ctest --test-dir build --show-only
```

### Mit Test-Presets

```bash
ctest --preset release
ctest --preset debug
ctest --preset custom
```

## 5. Workflows (Konfigurieren + Erstellen + Testen)

Eine der nützlichsten Funktionen von modernem CMake ist die Möglichkeit, die Schritte Konfiguration, Build und Test mithilfe eines Workflow-Presets nacheinander auszuführen.

Ein Workflow führt normalerweise die folgenden Schritte aus:

1. Konfigurieren
2. Erstellen
3. Testen

```bash
# Vollständigen Release-Workflow ausführen
cmake --workflow --preset release

# Debug-Workflow ausführen
cmake --workflow --preset debug

# Benutzerdefinierten Workflow ausführen
cmake --workflow --preset custom

# Verfügbare Workflows auflisten
cmake --workflow --list-presets
```

## 6. Konfigurations- und Build-Informationen anzeigen

```bash
# CMake-Cache anzeigen
cat build/CMakeCache.txt

# Ausgewählten Build-Typ anzeigen
grep CMAKE_BUILD_TYPE build/CMakeCache.txt

# Verwendeten Compiler (C++) anzeigen
grep CMAKE_CXX_COMPILER:FILEPATH build/CMakeCache.txt

# CUDA-Compiler anzeigen
grep CMAKE_CUDA_COMPILER:FILEPATH build/CMakeCache.txt

# Kompilierungsdatenbank anzeigen (Pfad an das Build-Verzeichnis anpassen)
cat build/compile_commands.json

# Kompilierungsbefehle durchsuchen
grep -n "source.cpp" build/compile_commands.json
```

### `compile_commands.json` generieren

CMake unterstützt die Generierung einer Kompilierungsdatenbank mit Ninja- und Makefile-Generatoren. Die generierte Datei `compile_commands.json` enthält die Compiler-Befehle, die zum Kompilieren verwendet werden. Sie wird häufig auch von `clangd`, `clang-tidy` und anderen Entwicklungstools verwendet.

Die Generierung der Kompilierungsdatenbank in `CMakeLists.txt` aktivieren:

```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```

Alternativ kann die Option während der Konfiguration übergeben werden:

```bash
cmake -S . -B build -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

## 7. Konfigurationsoptionen ändern

CMake-Optionen können geändert werden, ohne das Build-Verzeichnis löschen zu müssen.

```bash
# Zu Debug wechseln
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug

# Zu Release wechseln
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release

# OPTION1 aktivieren
cmake -S . -B build -DOPTION1=ON

# OPTION2 aktivieren
cmake -S . -B build -DOPTION2=ON

# OPTION3 aktivieren
cmake -S . -B build -DOPTION3=ON

# OPTION4 aktivieren
cmake -S . -B build -DOPTION4=ON

# OPTION1 deaktivieren
cmake -S . -B build -DOPTION1=OFF
```

> **Wichtig:** Das Ändern von Konfigurationsoptionen löst nicht zwangsläufig einen vollständigen Neuaufbau aus. CMake und das zugrunde liegende Build-System erstellen nur die betroffenen Targets bei Bedarf neu.

## 8. Builds bereinigen und neu generieren

### Alles entfernen und neu konfigurieren

```bash
# Gesamtes Build-Verzeichnis löschen
rm -rf build

# Von Grund auf neu konfigurieren
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release

# Erstellen
cmake --build build -j
```

### `--fresh` verwenden

Alternativ kann folgender Befehl verwendet werden:

```bash
# CMake-generierte Konfiguration entfernen und neu generieren
cmake --fresh -S . -B build
```

> **Wichtig:** `--fresh` entfernt den vorhandenen CMake-Cache und die von CMake generierten Dateien aus dem Build-Verzeichnis, bevor eine neue Konfiguration durchgeführt wird. Bereits kompilierte Objektdateien, Bibliotheken, ausführbare Dateien oder andere Build-Artefakte werden dadurch nicht zwangsläufig gelöscht.

## 9. Befehle für `CMakeLists.txt`

| Befehl | Zweck |
| --- | --- |
| `cmake_minimum_required()` | Minimale erforderliche CMake-Version festlegen |
| `project()` | Projekt definieren |
| `option()` | Boolesche Konfigurationsoption definieren |
| `set()` | Variable definieren |
| `find_package()` | Externe Abhängigkeiten finden |
| `find_library()` | Externe Bibliotheken finden |
| `find_path()` | Verzeichnisse mit Header-Dateien finden |
| `add_executable()` | Ausführbare Datei erstellen |
| `add_library()` | Bibliothek erstellen |
| `target_sources()` | Quelldateien zu einem Target hinzufügen |
| `target_include_directories()` | Include-Verzeichnisse konfigurieren |
| `target_link_libraries()` | Bibliotheken verknüpfen |
| `target_compile_options()` | Compiler-Optionen konfigurieren |
| `target_compile_definitions()` | Präprozessormakros definieren |
| `target_compile_features()` | Verwendete Sprachfeatures festlegen |
| `set_target_properties()` | Target-Eigenschaften konfigurieren |
| `add_subdirectory()` | Ein weiteres CMake-Projektverzeichnis einbinden |
| `include()` | Ein CMake-Modul oder Skript einbinden |
| `configure_file()` | Dateien konfigurieren oder kopieren |
| `install()` | Installationsregeln definieren |
| `include(CTest)` | Testinfrastruktur aktivieren |
| `add_test()` | Einen Test registrieren |

## 10. Generator Expressions

Generator Expressions können Compiler-Optionen abhängig von der Build-Konfiguration, der Programmiersprache, der Compiler-ID und anderen Target-Eigenschaften bedingt anwenden. Die Compiler-Flags selbst sind jedoch nicht zwangsläufig plattformübergreifend kompatibel.

### Beispiele

```cmake
# Konfigurationsabhängige Option (compilerabhängig)
$<$<CONFIG:Release>:-O3>

# Sprachabhängige Option
$<$<COMPILE_LANGUAGE:CXX>:-Wall>

# CUDA-spezifische Option
$<$<COMPILE_LANGUAGE:CUDA>:--extended-lambda>

# GNU-Compiler-spezifische Option
$<$<CXX_COMPILER_ID:GNU>:-march=native>

# Nur für Release und C++
$<$<AND:$<CONFIG:Release>,$<COMPILE_LANGUAGE:CXX>>:-O3>
```

Generator Expressions werden abhängig vom jeweiligen Ausdruck während der Generierung des Build-Systems oder während dessen Ausführung ausgewertet.

## 11. Empfohlene Ordnerstruktur der Preset-Dateien

```text
project/
├── CMakeLists.txt
├── CMakePresets.json
├── CMakeUserPresets.json
├── src/
├── include/
├── tests/
└── build/
    ├── debug/
    │   ├── CMakeCache.txt
    │   └── compile_commands.json
    ├── release/
    │   ├── CMakeCache.txt
    │   └── compile_commands.json
    └── custom/
```

| Datei / Verzeichnis | Git |
| --- | --- |
| `CMakeLists.txt` | Einchecken |
| `CMakePresets.json` | Einchecken |
| `CMakeUserPresets.json` | Nicht einchecken |
| `build/` | Nicht einchecken |

---

## Lizenz

Dieses Projekt ist unter der Apache License 2.0 lizenziert. Siehe [LICENSE](LICENSE) für Details.

Weitere Informationen zur Lizenz finden Sie hier: https://www.apache.org/licenses/LICENSE-2.0

---
