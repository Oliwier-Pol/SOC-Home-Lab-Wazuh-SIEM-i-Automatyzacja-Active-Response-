# 🛡️ SOC Home Lab: Wazuh SIEM i Automatyzacja (Active Response)

## 📖 Opis Projektu
To repozytorium dokumentuje architekturę, konfigurację oraz praktyczne scenariusze reagowania na incydenty (Incident Response) zrealizowane w ramach własnego, domowego laboratorium SOC (Security Operations Center). Głównym celem projektu było wdrożenie systemu SIEM, konfiguracja telemetrii na stacjach końcowych, tworzenie własnych reguł detekcji oraz implementacja automatycznej mitygacji zagrożeń (moduł Active Response).

> **Podziękowania:** Projekt został zainspirowany i zbudowany w oparciu o koncepcje z serii *"Build a SOC Lab with Wazuh"* autorstwa kanału MyDFIR.

## 🏗️ Architektura Laboratorium
Środowisko składa się z maszyny hosta, która zarządza trzema dedykowanymi maszynami wirtualnymi (VM), symulując w ten sposób firmową sieć:

*   **Maszyna Hosta (Windows 10 Pro):** Pełni rolę hipernadzorcy (Hypervisor) oraz głównej stacji roboczej służącej do zarządzania środowiskiem i dostępu do panelu Wazuh.
*   **Serwer Wazuh (Ubuntu 24.04 LTS):** Główny węzeł systemu SIEM. Utrzymuje komponenty takie jak Wazuh Indexer, Server oraz Dashboard.
*   **Endpoint Linux (Ubuntu 24.04 LTS):** Maszyna skonfigurowana z agentem Wazuh oraz narzędziem Sysmon dla systemu Linux. Stanowi główny cel ataków sieciowych (SSH).
*   **Endpoint Windows (Windows 10 Pro):** Maszyna skonfigurowana z agentem Wazuh oraz narzędziem Sysmon dla systemu Windows. Stanowi główny cel ataków bezpośrednio na system operacyjny (Manipulacja kontami).

## 🛠️ Wykorzystane Narzędzia i Technologie
*   **SIEM:** Wazuh
*   **Detekcja na stacjach końcowych (EDR / Telemetria):** Sysmon (Windows & Linux), Agenty Wazuh
*   **Systemy Operacyjne:** Ubuntu 24.04 LTS, Windows 10 Pro
*   **Kluczowe zagadnienia:** Akwizycja logów (Log Ingestion), Tworzenie własnych reguł (Custom Rule Creation), Aktywna Obrona (Active Response), Personalizacja Dashboardów, Raportowanie Incydentów (5W1H).

---

## 🎯 Scenariusze Ataków i Automatyczna Detekcja

### Scenariusz 1: Manipulacja Kontem (MITRE ATT&CK T1098)
*   **Działanie:** Domyślnie wyłączone konto `Gość` (`Guest`) na systemie Windows 10 zostało ręcznie aktywowane.
*   **Detekcja:** Sysmon zarejestrował zmiany w systemie, a log z tym zdarzeniem został natychmiast przesłany do systemu Wazuh.
*   **Modyfikacja:** W systemie Wazuh stworzyłem **własną regułę (Custom Rule)**. Dzięki temu, zamiast zaginąć w gąszczu standardowych logów operacyjnych, jakakolwiek zmiana statusu konta "Gość" natychmiast generuje alert o wysokim priorytecie (High Severity) na dedykowanym dashboardzie, powiadamiając analityka SOC o potencjalnej próbie utrzymania dostępu (Persistence) przez atakującego.

### Scenariusz 2: Atak SSH Brute-Force i Active Response (MITRE ATT&CK T1110)
*   **Działanie:** Przeprowadzono symulację ataku słownikowego (Brute-Force) na port SSH maszyny Linux (Ubuntu) ze stacji Windows.
*   **Detekcja:** Agent Wazuh wykrył liczne nieudane próby uwierzytelnienia (na podstawie logów demona `sshd`).
*   **Aktywna Obrona (Wazuh Firewall):** Skonfigurowałem moduł **Active Response**, aby system automatycznie i bez udziału człowieka neutralizował zagrożenie. Po wykryciu **3 nieudanych prób logowania w oknie czasowym 2 minut**, adres IP atakującego jest automatycznie dodawany do czarnej listy i blokowany na poziomie lokalnej zapory sieciowej serwera.
*   **Rezultat:** Atak został całkowicie powstrzymany w czasie rzeczywistym, a dla analityka wygenerowano zautomatyzowany alert z podsumowaniem zdarzenia.

---

## 📊 Dashboardy i Raportowanie
Na potrzeby projektowych scenariuszy zaprojektowano **własny Dashboard (Custom Dashboard)** w systemie Wazuh. Pozwala on na łatwą wizualizację kluczowej telemetrii: śledzenie nieudanych logowań, kategoryzację zdarzeń z Sysmona oraz monitorowanie akcji podjętych przez moduł Active Response.

Do repozytorium dołączony jest również oficjalny **Raport z Incydentu (Incident Response Report)**, w którym szczegółowo, z technicznego punktu widzenia, opisałem przebieg ataków przy użyciu metodologii 5W1H (Kto, Co, Kiedy, Gdzie, Dlaczego, Jak) oraz przedstawiłem rekomendacje naprawcze.

## 🚀 Kluczowe Umiejętności i Wnioski
1.  **Głęboka widoczność procesów (Endpoint Visibility):** Pomyślne wdrożenie narzędzia Sysmon (zarówno na Windowsie, jak i Linuxie) w celu drastycznego zwiększenia jakości i użyteczności logów (Log Fidelity).
2.  **Zaawansowane reguły detekcji:** Praktyczne doświadczenie w pisaniu, testowaniu i wdrażaniu własnych reguł oraz dekoderów w systemie Wazuh.
3.  **Automatyzacja i SOAR:** Zrozumienie i zaimplementowanie mechanizmów aktywnej obrony (Active Response), co stanowi fundament nowoczesnych operacji typu Security Orchestration, Automation, and Response.
