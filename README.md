# Winda – system sterowania jednokabinową windą z autoryzacją pięter  

## Dane studenta  
- Maciej Brzeżawski
- mbrzezawski@student.agh.edu.pl 
---

## Opis modelowanego systemu  

Projekt przedstawia **symulacyjny model windy z jedną kabiną**, pracującej w budynku o ustawialnej liczbie pięter (N ≥ 2). Kluczową cechą jest **system autoryzacji użytkowników** – każda osoba ma przypisany identyfikator (np. karta RFID lub PIN), który określa, do których pięter wolno jej dojechać.  

### Wymagania funkcjonalne  
1. **Wezwania zewnętrzne** – panel przy drzwiach na każdym piętrze (przycisk „↑” / „↓”).  
2. **Wezwania wewnętrzne** – panel w kabinie z przyciskami pięter; przyciski nieuprawnione są nieaktywne.  
3. **Proces autoryzacji** – kabina rusza dopiero po przyłożeniu karty/PIN‑u; ważność sesji kończy się po zamknięciu drzwi na wybranym piętrze.  
4. **Priorytety**:  
   - **Alarm** (natychmiastowe zatrzymanie i otwarcie drzwi),  
   - **Tryb serwisowy** – pełen dostęp dla konserwatora, blokada zwykłych wezwań.  
5. **Rejestr zdarzeń** – wszystkie akcje zapisywane w dzienniku (czas, użytkownik, zdarzenie).  

### Wymagania niefunkcjonalne  
- **Bezpieczeństwo** – wzajemne wykluczanie dostępu do krytycznych zasobów (kolejka zleceń, drzwi).  
- **Niezawodność** – wykrywanie i obsługa przeciążenia kabiny, timeout na zamykanie drzwi.  
- **Rozszerzalność** – możliwość dodania kolejnych kabin lub nowego rodzaju czytnika bez zmian w logice sterownika.  

### Architektura w Ada  

| Moduł (pakiet) | Rola w systemie | Kluczowe mechanizmy Ada |
| -------------- | --------------- | ----------------------- |
| `Elevator`     | Główny sterownik kabiny (ruch, drzwi) | zadania (`task`), opóźnienia `delay until` |
| `Auth`         | Autoryzacja i sesje użytkowników      | obiekt chroniony (`protected`) z procedurą `Authorize` |
| `Call_Queue`   | Kolejka wezwań                        | obiekt chroniony + warunek `requeue` |
| `IO_Panel`     | Obsługa przycisków i lampek           | zadania I/O, zdarzenia `select … or` |
| `Logger`       | Zapis zdarzeń do pliku                | zadanie w trybie pasywnym + bufor FIFO |
| `Config`       | Stałe konfiguracyjne (liczba pięter)  | pakiet czysto deklaratywny |

---
