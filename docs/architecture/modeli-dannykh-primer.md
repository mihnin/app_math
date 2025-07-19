# Модели данных (Пример)
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
```