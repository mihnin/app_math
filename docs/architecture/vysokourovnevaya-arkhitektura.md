# Высокоуровневая архитектура

* **Техническое резюме:**
    Проект представляет собой кросс-платформенное мобильное приложение на React Native, которое взаимодействует с бэкендом через типобезопасный API на tRPC. Бэкенд реализован в виде serverless-функций (Yandex Cloud Functions) и работает с базой данных PostgreSQL (Yandex Managed Service). Аутентификация и биллинг осуществляются через внешние российские сервисы (Yandex ID, RuStore Billing).

* **Платформа и Инфраструктура:**
    * **Провайдер:** Яндекс.Облако (серверы в РФ).
    * **Ключевые сервисы:** Yandex Cloud Functions, Yandex Managed Service for PostgreSQL, Yandex API Gateway, Yandex ID.
* **Репозиторий:** Monorepo, управляемый через `npm workspaces`.

* **Диаграмма Архитектуры:**
    ```mermaid
    graph TD
        subgraph "Мобильное приложение (React Native)"
            A[Основной код приложения] --> AB[Адаптер биллинга];
            AB --> RS[RuStore Billing SDK];
        end
        subgraph "Внешние сервисы"
            RS_API[Сервер биллинга RuStore]
            YandexID[Сервис Yandex ID для входа]
        end
        subgraph "Yandex.Cloud (Серверы в РФ)"
            B[API на Yandex Cloud Functions]
            D[БД: Yandex Managed Service for PostgreSQL]
        end
        A -- "Вход" --> YandexID
        RS -- "Покупка" --> RS_API
        RS_API -- "Вебхук об оплате" --> B
        B -- "Обновить статус в БД" --> D
        A -- "Проверить статус подписки" --> B
    ```