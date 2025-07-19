# Структура проекта (Monorepo)
```
/
├── apps/
│   ├── mobile/         # React Native приложение
│   └── web/            # Next.js приложение (для API)
├── packages/
│   ├── shared/         # Общий код, типы (TypeScript)
│   └── ui/             # Общие React-компоненты
└── package.json
```