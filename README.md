# BackupApp

Aplikacja do tworzenia kopii zapasowych na zewnętrznych nośnikach USB oraz udziałach sieciowych (SMB/NAS).

## Instalacja

Pobierz najnowszą wersję instalatora (`BackupApp_Setup.exe`) lub paczkę przenośną (`BackupApp_Portable.zip`) z zakładki [Releases](../../releases).

---

## Konfiguracja profilu kopii zapasowej

1. **Wybór nośnika docelowego:**
   * Wybierz podłączony dysk USB z listy rozwijanej w prawej kolumnie.
   * W razie potrzeby użyj przycisku odświeżenia listy napędów.

2. **Definiowanie źródeł:**
   * Dodaj katalogi lub pliki za pomocą przycisków wyboru folderu / pliku.
   * W przypadku zasobów sieciowych podaj ścieżkę UNC (np. `\\serwer\udzial\katalog`).

3. **Reguła selekcji:**
   * **Wszystkie pliki:** Kopiuje całą zawartość wskazanych ścieżek źródłowych.
   * **Tylko najnowszy podfolder:** Kopiuje wyłącznie najświeższy podkatalog znajdujący się w ścieżce źródłowej (np. ostatni dzienny eksport bazy).

4. **Weryfikacja integralności:**
   * Weryfikacja sum SHA-256 – program porównuje sumy kontrolne plików źródłowych ze skopiowanymi oraz generuje pliki `checksums.sha256` i `backup_summary.txt` na nośniku docelowym.

---

## Moduł harmonogramu i automatyzacji

Konfiguracja dostępna pod przyciskiem **Moduł harmonogramu...** w nagłówku aplikacji.

### Wyzwalacze uruchomienia:
* **Aktywacja przez wydarzenie (wpięcie dysku USB):** Proces rozpoczyna się automatycznie po fizycznym podłączeniu nośnika do komputera.
* **Aktywacja cykliczna:** Uruchamia kopię codziennie o wskazanej godzinie (np. 18:00).

### Akcje wstępne i końcowe:
* **Podłączanie udziałów sieciowych:** Nawiązuje sesję SMB przed transferem, korzystając z poświadczeń zapisanych w Menedżerze poświadczeń Windows.
* **Odłączanie udziałów sieciowych:** Zamyka sesję SMB natychmiast po zakończeniu kopiowania.
* **Wzbudź / zamontuj dysk USB (rescan PnP):** Wymusza ponowne wykrycie odmontowanego nośnika stale wpiętego do portu przed uruchomieniem zadania cyklicznego.
* **Bezpiecznie odłącz / wysuń nośnik:** Odmontowuje wolumin USB po zakończeniu kopiowania.

### Powiadomienia e-mail (SMTP):
* Konfiguracja serwera SMTP (np. host, port 587, szyfrowanie SSL/TLS, login i hasło).
* Hasło zapisywane jest w postaci zaszyfrowanej przy użyciu Windows DPAPI.

### Praca w tle:
* Opcje działania w zasobniku systemowym (System Tray) oraz autostartu z systemem Windows.

---

## Plik konfiguracyjny

* **Wersja instalacyjna:** `%LOCALAPPDATA%\BackupApp\config.json`
* **Wersja przenośna (Portable):** `config.json` bezpośrednio w folderze aplikacji.
