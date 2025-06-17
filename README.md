# Winda ― system sterowania jednokabinową windą z autoryzacją pięter  
*(projekt AADL / OSATE)*
### Autor: Maciej Brzeżawski

---

## 1. Opis ogólny

Projekt przedstawia **symulacyjny model jednokabinowej windy** pracującej w budynku
o konfigurowalnej liczbie pięter (w przykładzie – parter + I piętro).  
Kluczową cechą jest **autoryzacja użytkowników** – kabina rusza dopiero po pozytywnej weryfikacji identyfikatora RFID/PIN, a uprawnienia wygasają po zamknięciu drzwi na docelowym piętrze.

Najważniejsze wymagania funkcjonalne i sposób ich realizacji:

| Wymaganie | Implementacja w modelu |
|-----------|-----------------------|
| Wezwania zewnętrzne (↑/↓) | urządzenia `floor_p0`, `floor_p1`, porty `up_call`, `down_call` |
| Wezwania z kabiny | `cabin_panel.floor_request` → wątek `Call_Thread` |
| Autoryzacja | `rfid_reader.user_id` → `Auth_Thread`; sesja kończona przez `door_sensor.door_closed` |
| Alarm (stop + open) | `Safety_Thread` z priorytetem 15 |
| Tryb serwisowy | tryb systemu **Service** |
| Rejestr zdarzeń | `Logger_Thread` (period 100 ms) |

---

## 2. Inwentaryzacja komponentów

| # | Typ | Nazwa w modelu | Krótka rola |
|---|-----|----------------|-------------|
| 1 | **processor** | `cpu` | wykonuje proces sterownika |
| 2 | **bus** | `shaft_bus` | wspólna magistrala CAN-like |
| 3 | **process** | `ctrl` (`Elevator_Controller`) | koordynator całej logiki |
| 4 | **device** | `cabin_panel` | panel przycisków w kabinie |
| 5 | **device** | `floor_p0` | panel parteru |
| 6 | **device** | `floor_p1` | panel I piętra |
| 7 | **device** | `rfid_reader` | czytnik kart / PIN-pad |
| 8 | **device** | `door_sensor` | czujnik zamknięcia drzwi |
| 9 | **thread** | `Auth_Thread` | weryfikacja użytkownika |
|10 | **thread** | `Call_Thread` | kolejkowanie wezwań |
|11 | **thread** | `Motor_Thread` | sterowanie napędem |
|12 | **thread** | `Logger_Thread` | rejestr zdarzeń |
|13 | **thread** | `Safety_Thread` | obsługa alarmu |

---

## 3. Architektura sprzętowa  

![Diagram hardware](/diagrams/Elevator_Hardware.png)
---

## 4. Architektura programowa  

![Diagram software](/diagrams/Elevator_Software.png)

---

## 5. Implementacja systemu

![Instancja systemu](/diagrams/Elevator_Impl_DIagram.png)

*Widok instancji `Elevator_impl_Instance.aaxl2` – widać dwa panele piętrowe, czujnik drzwi i pełną siatkę połączeń.*

---

## 6. Wyniki weryfikacji

| Analiza OSATE | Wynik |
|---------------|-------|
| **Check Binding Constraints** | ✔ Brak błędów |
| **Check Connection Binding Consistency** | ✔ Brak błędów |

*(wszystkie komponenty i połączenia poprawnie przypisane do CPU / magistrali)*

---

## 7. Wnioski

Model spełnia wszystkie założenia projektu:

* funkcjonalne (wezwania, autoryzacja, tryb serwisowy, alarm, dziennik),  
* niefunkcjonalne (spójne bindingi HW/SW, możliwość rozszerzeń).