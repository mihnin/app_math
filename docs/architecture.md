# Архитектурный Документ: «Магическая Карта Знаний»

### 1. Введение

Этот документ описывает полную fullstack-архитектуру для проекта «Магическая Карта Знаний», построенную на базе **Яндекс.Облака** для соответствия законодательству РФ о хранении персональных данных (ФЗ-152).

* **Стартовый шаблон:** Рекомендуется использовать принципы и структуру **T3 Stack** (Next.js, TypeScript, tRPC, Tailwind) с адаптацией развертывания под инфраструктуру Яндекс.Облака.
* **Журнал изменений:** Внесены правки по результатам PO-ревью (добавлен инструмент аналитики, инструкции по локальной настройке).

### 2. Высокоуровневая архитектура

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

### 3. Технологический стек

| Категория | Технология | Цель |
| :--- | :--- | :--- |
| Язык | TypeScript | Основной язык |
| Мобильный фреймворк | React Native | Кросс-платформенная разработка |
| API | tRPC на Next.js | Типобезопасный API |
| Хостинг API | Yandex Cloud Functions | Serverless-бэкенд в РФ |
| База данных | PostgreSQL | Надежная реляционная БД |
| Провайдер БД | Yandex Managed Service | Управляемая БД в РФ |
| Аутентификация | Yandex ID | Вход для пользователей |
| Биллинг | RuStore Billing SDK | Монетизация в RuStore |
| Аналитика | Yandex AppMetrica | Сбор метрик MVP |
| Тестирование | Jest, React Testing Library | Юнит- и интеграционное тестирование |
| CI/CD | GitHub Actions | Автоматическая сборка и развертывание |

### 4. Модели данных (Пример)
```typescript
// packages/shared/src/types.ts
export interface PlayerProfile {
  id: string; // ID из Yandex ID
  // ...другие поля профиля
}
export interface PlayerProgress {
  userId: string;
  completed_locations: number[];
  subscription_status: 'active' | 'inactive';
  subscription_expires_at?: Date;
}
5. Структура проекта (Monorepo)
/
├── apps/
│   ├── mobile/         # React Native приложение
│   └── web/            # Next.js приложение (для API)
├── packages/
│   ├── shared/         # Общий код, типы (TypeScript)
│   └── ui/             # Общие React-компоненты
└── package.json
6. Локальная настройка
Для запуска проекта локально разработчику необходимо:

Клонировать репозиторий.

Установить зависимости командой npm install.

Создать файл .env в корне проекта и в apps/web для хранения ключей доступа к Яндекс.Облаку и другим сервисам.

Запустить среду разработки командой npm run dev.

7. Развертывание и CI/CD
API: Настроен CI/CD пайплайн в GitHub Actions для автоматического развертывания serverless-функций в Yandex.Cloud.

Мобильное приложение: Настроен CI/CD пайплайн в GitHub Actions для сборки .apk (Android).