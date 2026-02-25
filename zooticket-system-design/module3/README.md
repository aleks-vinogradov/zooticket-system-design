# Модуль 3: Моделирование процессов ZooTicket

## Задание

Построить диаграммы:
1. **Последовательности (Sequence Diagram)** для сценария «Сканирование QR на входе»
2. **Деятельности (Activity Diagram)** для сценария возврата билета

---

## 1. Диаграмма последовательности: Сканирование QR на входе

### Описание сценария

Посетитель подходит к турникету, сканирует QR-код с билета. Система проверяет валидность билета и открывает турникет при успешной проверке.

### Код диаграммы (PlantUML)

Сохраните этот код в файл `module3/sequence-diagram.puml`:

```plantuml
@startuml
title Сканирование QR-кода на входе

actor "Посетитель" as Visitor
participant "Турникет" as Turnstile
participant "ZooTicket Backend" as Backend
database "База Данных" as DB

Visitor -> Turnstile : Подносит билет с QR-кодом
activate Turnstile

Turnstile -> Turnstile : Сканирует QR-код
Turnstile -> Backend : Отправляет код на проверку (API)
activate Backend

Backend -> DB : Запрос: Найти билет по коду
activate DB
DB --> Backend : Данные билета (статус, дата)
deactivate DB

alt Билет валиден (активен, дата совпадает)
    Backend -> DB : Обновить статус билета на "Использован"
    activate DB
    DB --> Backend : Подтверждение обновления
    deactivate DB
    Backend --> Turnstile : Ответ: "Успех. Проход разрешен"
    Turnstile -> Turnstile : Открывает турникет, зеленый сигнал
    Turnstile --> Visitor : Сигнал "Проходите"
else Билет невалиден
    Backend --> Turnstile : Ответ: "Ошибка. Проход запрещен"
    Turnstile -> Turnstile : Красный сигнал, звуковой сигнал
    Turnstile --> Visitor : Сигнал "Билет недействителен"
end

deactivate Backend
deactivate Turnstile
@enduml