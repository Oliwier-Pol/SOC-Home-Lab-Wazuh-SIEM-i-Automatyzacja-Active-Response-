
# 🛡️ SOC Home Lab: Wazuh SIEM & Automatyzacja (Active Response)

## 🎯 Cel Projektu :
Zbudowanie i konfiguracja środowiska SOC (Security Operations Center) od podstaw w celu zaprezentowania działania systemu SIEM, telemetrii stacji końcowych, tworzenia reguł detekcji oraz automatyzacji obrony z wykorzystaniem Active Response.

## 📊 Opis Projektu :
Głównym założeniem projektu było wdrożenie środowiska testowego z systemem Wazuh SIEM. Środowisko posłużyło do symulacji ataków sieciowych (na usługi) oraz wektorów kompromitacji stacji końcowej, a następnie do wykreowania odpowiednich mechanizmów alarmowania i natychmiastowej mitygacji.

## 🏗️ Architektura Środowiska :

<img width="335" height="331" alt="schemat" src="https://github.com/user-attachments/assets/f7ff26fb-8076-48f9-bebb-4517d06af25a" />

## 🧰 Wykorzystane Narzędzia :
- Wazuh SIEM (Server, Indexer, Dashboard)
- Sysmon (System Monitor dla Windows i Linux)
- Ubuntu 24.04 LTS (Serwer i stacja robocza)
- Windows 10 Pro (Host i stacja robocza)
- Vmware Workstation Pro (Obsługa wirtualnych maszyn)

## 🛠️ Zaprezentowane Umiejętności :
- Integracja i wdrożenie systemu Wazuh oraz agentów na stacjach końcowych (Windows i Linux).
- Instalacja i konfiguracja narzędzia Sysmon w celu wzbogacenia logów systemowych.
- Projektowanie i testowanie własnych reguł w Wazuh.
- Stworzenie spersonalizowanych dashboardów do wizualizacji zdarzeń bezpieczeństwa i alarmów.
- Symulacja ataku typu SSH Brute-Force oraz manipulacji kontami systemowymi w Windows.
- Wdrożenie automatycznej odpowiedzi na incydent (SOAR) za pomocą mechanizmu Wazuh Active Response.
- Tworzenie raportu dochodzeniowego przy użyciu metodologii 5W1H.

## 📁 Kluczowe Wyniki :
- W pełni funkcjonalne wdrożenie SIEM.
- Niestandardowy Dashboard SOC w systemie Wazuh.
- Działające reguły detekcyjne.
- Zautomatyzowany mechanizm blokady zagrożeń w czasie rzeczywistym.
- Raport z analizy incydentów.

## 🔍 Wykonane Kroki :
1. Utworzenie środowiska maszyn wirtualnych:
   - Host (Windows 10 Pro) – zarządzanie i wyświetlanie dashboardu.
   - Serwer Wazuh (Ubuntu 24.04 LTS).
   - Stacja końcowa Linux (Ubuntu 24.04 LTS) – z agentem Wazuh i Sysmon.
   - Stacja końcowa Windows (Windows 10 Pro) – z agentem Wazuh i Sysmon.

📌 Zrzuty ekranu z konfiguracji środowiska:
<img width="390" height="230" alt="Zrzut z maszyn wirtualnych" src="" />

2. Wdrożenie narzędzia Sysmon i skonfigurowanie agentów Wazuh na wszystkich stacjach końcowych w celu centralizacji logów.

📌 Zrzut ekranu z poprawnie podłączonymi agentami w panelu Wazuh:
<img width="785" height="450" alt="Agenty Wazuh" src="" />

3. Utworzenie dedykowanego Dashboardu w Wazuh do monitorowania kluczowych incydentów.

📌 Zrzuty ekranu Dashboardu:
<img width="390" height="230" alt="Custom Dashboard 1" src="" />

4. Symulacja Manipulacji Kontem (MITRE T1098).
   - Ręczna aktywacja domyślnie wyłączonego konta "Guest" na stacji Windows.
   - Opracowanie własnej reguły (Custom Rule) podnoszącej powiadomienie o krytycznym alercie z Event ID 60106 z Sysmona.

📌 Zrzuty ekranu wyzwolonego alertu:
<img width="785" height="230" alt="Alert Windows Guest" src="" />

5. Symulacja ataku SSH Brute-Force (MITRE T1110) i wdrożenie Active Response.
   - Przeprowadzenie ataku siłowego na port SSH na maszynie Linux (Ubuntu).
   - Konfiguracja modułu Active Response blokującego IP w zaporze na 2 minuty po wystąpieniu 3 błędnych logowań.

📌 Zrzuty ekranu blokady IP:
<img width="785" height="230" alt="Active Response Trigger" src="" />

---

## 🚨 Raport z Dochodzenia (Investigation Report):

### Dochodzenie 1: Manipulacja Kontem (Windows 10)
System Wazuh zaalarmował o podejrzanej zmianie w konfiguracji systemu operacyjnego. Zidentyfikowano, że konto `Guest`, które standardowo jest wyłączone ze względów bezpieczeństwa, zostało aktywowane. 

> 💡 **Uwaga dotycząca detekcji:** Zamiast polegać na domyślnych alertach, napisałem niestandardową regułę detekcji która monitoruje konto gościa. Pełny kod tej reguły (jak i pozostałych, użytych w projekcie) znajduje się w dedykowanym pliku **`custom_rules.xml`** dostępnym w tym repozytorium.

####  Analiza zdarzenia (Triage 5W1H):
- **WHO (Kto):** Nieznany sprawca posiadający dostęp z podwyższonymi uprawnieniami (wykonano podczas testów lokalnych).
- **WHAT (Co):** Modyfikacja atrybutów systemowych prowadząca do włączenia lokalnego konta użytkownika `Guest`.
- **WHEN (Kiedy):** 
- **WHERE (Gdzie):** Maszyna końcowa oznaczona jako Windows 10 Endpoint.
- **WHY (Dlaczego):** Uruchomienie ukrytych/nieużywanych kont to typowa technika atakujących w celu zdobycia trwałego dostępu (Persistence) do systemu bez wzbudzania podejrzeń.
- **HOW (Jak):** Przez użycie wbudowanych narzędzi administracyjnych systemu operacyjnego Windows (np. z wiersza poleceń / Zarządzania komputerem).
