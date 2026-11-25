# Барахолка

Учебный проект по ООАП. Диаграммы хранятся как код в `docs/diagrams`.

## Архитектура

### Диаграмма классов

```mermaid
classDiagram
  class Polzovatel {
    <<abstract>>
    +str name
    +str phone
    +login()
  }
  class Prodavec {
    +publishAd(title, price) Objyavlenie
  }
  class Pokupatel {
    +buy(ad) Sdelka
  }
  class Moderator {
    +ban(user)
  }
  class Objyavlenie {
    +str title
    +int price
    +str status
  }
  class Sdelka {
    +int price
    +datetime date
  }
  class Otzyv {
    +int rating
    +str text
  }
  class Zhaloba {
    +str reason
    +str status
  }
  class Photo {
    +str url
  }
  Polzovatel <|-- Prodavec
  Polzovatel <|-- Pokupatel
  Polzovatel <|-- Moderator
  Prodavec "1" --> "0..*" Objyavlenie : публикует
  Pokupatel "1" --> "0..*" Sdelka : совершает
  Objyavlenie "1" --> "0..1" Sdelka : продано в
  Pokupatel "1" --> "0..*" Otzyv : пишет
  Otzyv "0..*" --> "1" Prodavec : о продавце
  Pokupatel "1" --> "0..*" Zhaloba : подаёт
  Zhaloba "0..*" --> "1" Objyavlenie : на объявление
  Moderator "1" --> "0..*" Zhaloba : рассматривает
  Objyavlenie "1" *-- "0..10" Photo : содержит
```

### Диаграмма последовательности «Купить»

```mermaid
sequenceDiagram
  actor B as Покупатель
  participant App as Приложение
  participant Deals as Сервис сделок
  participant DB as База данных
  participant Notify as Уведомления
  B->>App: нажать «Купить»
  App->>Deals: создать сделку(adId, buyerId)
  Deals->>DB: проверить, продано ли объявление
  DB-->>Deals: статус объявления
  alt объявление свободно
    Deals-->>App: сделка создана
    Deals->>Notify: сообщить продавцу
    App-->>B: покупка оформлена
  else уже продано
    Deals-->>App: ошибка 409
    App-->>B: «Товар уже продан»
  end
```

### Жизненный цикл объявления

```mermaid
stateDiagram-v2
  state "На модерации" as Moderation
  [*] --> Draft
  Draft --> Moderation : отправить на модерацию
  Moderation --> Draft : отклонено
  Moderation --> Published : одобрено
  Published --> Sold : покупка
  Published --> Archived : снять с продажи
  Published --> Blocked : модератор блокирует
  Blocked --> Published : разблокировать
  Sold --> [*]
  Archived --> [*]
```

### Диаграмма прецедентов (PlantUML)

![Прецеденты](docs/diagrams/usecase.png)

Исходник: [usecase.puml](docs/diagrams/usecase.puml). PNG получается так: вставить текст на plantuml.com/plantuml и скачать картинку.
