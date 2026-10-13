# BackupApp

Profesjonalna aplikacja desktopowa (.NET 8 WPF) dla systemu Windows, przeznaczona do automatycznego i bezpiecznego tworzenia kopii zapasowych na zewnętrznych nośnikach USB oraz udziałach sieciowych (SMB/NAS).

Projekt kładzie szczególny nacisk na integralność kryptograficzną danych, izolację zasobów sieciowych przed złośliwym oprogramowaniem (ransomware) oraz automatyzację scenariuszy.

---

## Główne możliwości aplikacji

### 1. Elastyczne profile i reguły selekcji
* **Profile zadań (Presety):** Dowolna liczba profili z niezależnymi listami źródeł, regułami i ścieżkami docelowymi.
* **Tryb selekcji plików:**
  * **Wszystkie pliki:** Pełna rekurencyjna kopia wskazanych katalogów i plików.
  * **Tylko najnowszy podfolder:** Inteligentna selekcja najświeższego katalogu w danym zasobie (np. ostatni eksport bazy danych lub projekt z dzisiejszą datą).
* **Centralna synchronizacja:** Możliwość współdzielenia profili w sieci lokalnej (plik `config.json` na centralnym serwerze SMB/NAS).

### 2. Kryptograficzna weryfikacja i raportowanie
* **Weryfikacja SHA-256:** Każdy skopiowany plik jest weryfikowany poprzez obliczenie i porównanie sumy kontrolnej SHA-256 ze źródłem.
* **Plik sum kontrolnych:** Generowanie standardowego pliku `checksums.sha256` w folderze docelowym (zgodnego z narzędziem `sha256sum`).
* **Raport wykonawczy:** Zapis pliku `backup_summary.txt` zawierającego pełną statystykę: liczbę przetworzonych plików, objętość danych, prędkość transferu oraz status integralności.

### 3. Łańcuch zadań i automatyzacja scenariuszy
* **Wyzwalacze uruchomienia:**
  * **Zdarzeniowy:** Automatyczny start procedury w ułamku sekundy po fizycznym wpięciu dedykowanego pendrive'a/dysku USB do komputera.
  * **Cykliczny:** Uruchomienie o wyznaczonej godzinie (np. codziennie o 18:00).
* **Wzbudzanie nośnika USB (PnP Rescan):** Ponowne zamontowanie dysku, który pozostaje stale wpięty w porcie USB, lecz został programowo odmontowany po poprzedniej kopii.
* **Bezpieczne odłączenie sprzętowe:** Opróżnienie buforów zapisu systemu Windows i fizyczne odmontowanie/wysunięcie dysku USB (`FSCTL_DISMOUNT_VOLUME`, `IOCTL_STORAGE_EJECT_MEDIA`).

### 4. Izolacja zasobów sieciowych (Ochrona przed Ransomware)
* **Dynamiczne sesje SMB:** Aplikacja zestawia połączenie z udziałem sieciowym UNC (`\\serwer\udzial`) tuż przed rozpoczęciem transferu przy użyciu Windows Networking API (`WNetAddConnection2`).
* **Odłączanie po zakończeniu:** Natychmiast po ukończeniu kopii sesja SMB jest niszczona (`WNetCancelConnection2`). Udział nie pozostaje stale zmapowany w systemie operacyjnym, uniemożliwiając wirusom szyfrującym (ransomware) zainfekowanie zasobów na serwerze NAS.
* **Bezpieczeństwo poświadczeń:** Integracja z Menedżerem poświadczeń Windows oraz szyfrowanie haseł lokalnych mechanizmem Windows Data Protection API (DPAPI).

### 5. Powiadomienia e-mail (SMTP TLS)
* Bezpośrednia wysyłka szyfrowanego raportu e-mail (sukces lub szczegółowy log błędów) po zakończeniu procedury kopii.

### 6. Wbudowany moduł auto-update
* Bezpośrednia integracja z repozytorium GitHub Releases (`JasperBTA/BackAPP`).
* Sprawdzanie dostępności nowej wersji przy starcie w tle lub na żądanie.
* Pobieranie instalatora i cicha aktualizacja aplikacji jednym kliknięciem.

---

## Wymagania systemowe

* **System operacyjny:** Windows 10 (wersja 1809 lub nowsza) / Windows 11 (architektura x64).
* **Środowisko uruchomieniowe:** Brak dodatkowych wymagań dla wersji instalacyjnej (pakiet Self-Contained z wbudowanym .NET 8 Runtime).

---

## Budowanie ze źródeł

### Wymagania do kompilacji:
* .NET 8.0 SDK
* Inno Setup 6 (do wygenerowania instalatora `BackupApp_Setup.exe`)

### Kompilacja aplikacji:
```powershell
dotnet build BackupApp.csproj -c Release
```

### Generowanie pełnego instalatora i paczki Portable:
```powershell
powershell -ExecutionPolicy Bypass -File .\build_installer.ps1
```
Wygenerowane pakiety znajdą się w katalogu `installer_output/`:
* `BackupApp_Setup.exe` (Instalator Windows)
* `BackupApp_Portable.zip` (Wersja przenośna)

---

## Automatyzacja CI/CD (GitHub Actions)

Projekt zawiera skonfigurowane procesy GitHub Actions w katalogu `.github/workflows/`:
* **`ci.yml`:** Automatyczna weryfikacja kompilacji przy każdym commicie i Pull Requeście na gałąź `main`.
* **`release.yml`:** Automatyczne budowanie instalatora oraz publikacja nowego wydania w GitHub Releases po utworzeniu tagu wersji (np. `v1.0.0`) lub po ręcznym wywołaniu z zakładki *Actions*.

---

## Bezpieczeństwo i licencja

* Projekt nie zbiera danych telemetrycznych.
* Wszelkie poświadczenia są zabezpieczone kluczami kryptograficznymi bieżącego użytkownika Windows (DPAPI).

