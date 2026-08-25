
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

📌 Zrzut ekranu z konfiguracji środowiska:

<img width="694" height="198" alt="Maszyny_Wirtualne" src="https://github.com/user-attachments/assets/462c2525-20a5-4ea4-8b97-d6d5143b0572" />

2. Wdrożenie narzędzia Sysmon i skonfigurowanie agentów Wazuh na wszystkich stacjach końcowych w celu centralizacji logów.

📌 Zrzut ekranu z poprawnie podłączonymi agentami w panelu Wazuh:

<img width="1920" height="588" alt="panel_agentow" src="https://github.com/user-attachments/assets/d560e7d0-1656-45b6-96cb-0b1233c86960" />

3. Utworzenie dedykowanego Dashboardu w Wazuh do monitorowania kluczowych incydentów.

📌 Zrzut ekranu Dashboardu:

 <img width="1920" height="906" alt="dashboard" src="https://github.com/user-attachments/assets/718eed89-8e83-46ae-ae03-6fa3d491c239" />

4. Symulacja Manipulacji Kontem (MITRE T1098).
   - Ręczna aktywacja domyślnie wyłączonego konta "Guest" na stacji Windows.
   - Opracowanie własnej reguły (Custom Rule) podnoszącej powiadomienie o krytycznym alercie z Event ID 60106 z Sysmona.

📌 Zrzuty ekranu wyzwolonego alertu:

<img width="857" height="81" alt="alert_gosc" src="https://github.com/user-attachments/assets/303c073e-5c36-4ab5-92dd-3ad78225b12d" />

<img width="1476" height="462" alt="alert_2" src="https://github.com/user-attachments/assets/49e18de8-20a3-4709-838d-8727852a8da0" />

5. Symulacja ataku SSH Brute-Force (MITRE T1110) i wdrożenie Active Response.
   - Przeprowadzenie ataku siłowego na port SSH na maszynie Linux (Ubuntu).
   - Konfiguracja modułu Active Response blokującego IP w zaporze na 2 minuty po wystąpieniu 3 błędnych logowań.

📌 Zrzuty ekranu blokady IP:

<img width="631" height="52" alt="blokada" src="https://github.com/user-attachments/assets/fc167b16-72f7-4921-84f6-95f2e5302d30" />

<img width="1240" height="479" alt="blokada_2" src="https://github.com/user-attachments/assets/d2d855af-552e-4b4c-8240-7088aa066fd5" />

---

## 🚨 Raport z Dochodzenia (Investigation Report):

> 💡 **Uwaga dotycząca detekcji:** Zamiast polegać wyłącznie na domyślnych alertach, stworzyłem własne reguły detekcji (Custom Rules) w systemie Wazuh. Obejmują one  monitorowanie aktywacji konta gościa oraz korelację zdarzeń wykrywającą ataki SSH Brute-Force (rejestracja 3 nieudanych prób logowania z tego samego adresu IP w oknie 120 sekund). Pełny kod tych reguł wraz z komentarzami znajduje się w dedykowanym pliku **`custom_rules.xml`** w tym repozytorium.

### Dochodzenie 1: Manipulacja Kontem (Windows 10)
System Wazuh zaalarmował o podejrzanej zmianie w konfiguracji systemu operacyjnego. Zidentyfikowano, że konto `Guest`, które standardowo jest wyłączone ze względów bezpieczeństwa, zostało aktywowane. 
<img width="857" height="81" alt="alert_gosc" src="https://github.com/user-attachments/assets/303c073e-5c36-4ab5-92dd-3ad78225b12d" />

####  Analiza zdarzenia (Triage 5W1H):
- **WHO (Kto):** Nieznany sprawca posiadający dostęp z podwyższonymi uprawnieniami (wykonano podczas testów lokalnych).
- **WHAT (Co):** Modyfikacja atrybutów systemowych prowadząca do włączenia lokalnego konta użytkownika `Guest`.
- **WHEN (Kiedy):**  Alert został odebrany 2026-08-20 15:22:56 
- **WHERE (Gdzie):** Maszyna końcowa oznaczona jako Windows 10 Endpoint.
- **WHY (Dlaczego):** Uruchomienie ukrytych/nieużywanych kont to typowa technika atakujących w celu zdobycia trwałego dostępu (Persistence) do systemu bez wzbudzania podejrzeń.
- **HOW (Jak):** Przez użycie wbudowanych narzędzi administracyjnych systemu operacyjnego Windows.
### Dochodzenie 2: Atak SSH Brute-Force & Active Response (Ubuntu Linux)
W systemie Wazuh odnotowano wyzwolenie niestandardowej reguły detekcji (Custom Rule ID: `100101`), wskazującej na masowe, nieudane próby logowania do usługi SSH na maszynie Ubuntu. Incydent został poddany natychmiastowej weryfikacji w celu potwierdzenia tożsamości atakującego oraz sprawdzenia skuteczności mechanizmu automatycznej obrony.

> 💡 **Weryfikacja reguły i czarnej listy:** Podczas analizy potwierdzono wyzwolenie autorskiej reguły korelacyjnej po 3 błędnych próbach logowania w oknie 120 sekund. Następnie zweryfikowano działanie modułu **Active Response** — potwierdzono dodanie zidentyfikowanego adresu IP sprawcy na czarną listę (blacklista w zaporze sieciowej `iptables`), co skutecznie odcięło dalszy ruch atakującego w czasie rzeczywistym.

####  Analiza zdarzenia (Triage 5W1H):
- **WHO (Kto):** Zidentyfikowany adres IP atakującego `192.168.177.136` generujący powtarzalne żądania uwierzytelnienia na konto użytkownika.
- **WHAT (Co):** Wielokrotne, nieudane próby logowania przez protokół SSH (Event SID: `5760` zmapowany do reguły `100101`), zakończone automatyczną blokadą hosta.
- **WHEN (Kiedy):** Alert o wielokrotnej nieudanej próbie zalogowania został odebrany 2026-08-21 16:22:53 natomiast blokada i alert idący za blokadą został odebrany 2026-08-21 16:23:25
- **WHERE (Gdzie):** Maszyna docelowa `Linux Endpoint` (Ubuntu 24.04 LTS)
- **WHY (Dlaczego):** Próba nieautoryzowanego przełamania poświadczeń w celu zdobycia dostępu.
- **HOW (Jak):** Wysyłanie sekwencji zapytań z jednego adresu źródłowego.

📌 Zrzuty ekranu z analizy alertu i weryfikacji czarnej listy:

<img width="687" height="36" alt="alert_ssh" src="https://github.com/user-attachments/assets/cc467b74-b67a-4b2d-a4d3-2956f77f3551" />
<img width="631" height="52" alt="blokada" src="https://github.com/user-attachments/assets/fc167b16-72f7-4921-84f6-95f2e5302d30" />
<img width="479" height="141" alt="blokada_firewall" src="https://github.com/user-attachments/assets/e678f6b2-27ca-4707-b1e0-6039e2101d94" />
<img width="1050" height="496" alt="alert_4" src="https://github.com/user-attachments/assets/c85c51f8-ffe3-4ac0-9fd2-5bf602a4e841" />
<img width="1225" height="473" alt="alert_3" src="https://github.com/user-attachments/assets/279e9d1a-0e65-459a-b05a-bc526c460ec3" />
