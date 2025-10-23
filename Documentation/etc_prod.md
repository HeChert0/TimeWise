# 📱 Отчет об архитектуре приложения TimeWise

## 1. Общая архитектура

### Архитектурный паттерн: **MVVM с элементами MVI**

**MVVM (Model-View-ViewModel)**
- **Model** - слой данных (Room БД, Task, TaskDao)
- **View** - UI слой (Compose экраны)
- **ViewModel** - связующее звено (BaseViewModel<Action, State>)

**Элементы MVI (Model-View-Intent)**
- BaseViewModel использует генерики для Action (намерения) и State (состояние)
- Однонаправленный поток данных
- Предсказуемое управление состоянием

## 2. Применяемые паттерны проектирования

### **Repository Pattern**
- `TaskDataService` - абстракция над источником данных
- Изолирует бизнес-логику от деталей хранения данных
- Единая точка доступа к данным

### **Observer Pattern**
- Использование `Flow` для реактивного программирования
- Автоматическое обновление UI при изменении данных
- Подписка на изменения в базе данных

### **Singleton Pattern**
- `AppDatabase` - единственный экземпляр БД
- `Companion objects` для хранения констант
- SharedPreferences для глобальных настроек

### **Factory Pattern**
- Room генерирует реализации DAO
- Создание экземпляров ViewModel

### **Strategy Pattern**
- `PriorityConverter` - стратегия конвертации enum в БД
- `ListStringConverter` - стратегия для списков

## 3. Технологический стек

### **UI Framework**
- **Jetpack Compose** - декларативный UI
  - Material Design компоненты
  - Кастомные темы (LightColorScheme/DarkColorScheme)
  - Type-safe навигация

### **Навигация**
- **Navigation Compose**
  - RouteWith1Arg/RouteWith2Arg для типобезопасных маршрутов
  - NavHostController для управления стеком
  - Deep linking поддержка

### **База данных**
- **Room** (абстракция над SQLite)
  - Compile-time проверка SQL запросов
  - Автоматическая генерация кода
  - Поддержка миграций

### **Асинхронность**
- **Kotlin Coroutines**
  - Flow для потоков данных
  - Suspend функции для IO операций
  - Lifecycle-aware корутины

### **Dependency Injection**
- Ручное внедрение зависимостей через конструкторы
- Возможно использование Hilt/Dagger (не явно видно)

## 4. Хранение данных

### **Локальная база данных (Room)**
```
SQLite БД на устройстве
├── Таблица Tasks
│   ├── id (PRIMARY KEY)
│   ├── title
│   ├── description
│   ├── priority (конвертируется через PriorityConverter)
│   ├── status
│   ├── creationDate
│   ├── updateDate
│   ├── notificationTime
│   └── attachments (List<String> через ListStringConverter)
```

### **SharedPreferences**
- `StatusPreferences` - хранение настроек приложения
- Ключ-значение хранилище
- Для простых настроек и флагов

### **Файловая система**
- Вложения (attachments) - пути к файлам
- Хранятся в internal storage приложения

## 5. Слои приложения

### **Presentation Layer (UI)**
- Compose экраны
- ViewModel для управления состоянием
- Навигация между экранами

### **Domain Layer (Бизнес-логика)**
- Use cases (неявно в TaskDataService)
- Модели данных (Task, Priority)
- Бизнес-правила

### **Data Layer (Данные)**
- Room Database
- DAO интерфейсы
- Конвертеры типов
- SharedPreferences

## 6. Ключевые особенности

### **Реактивность**
- Flow для автоматического обновления UI
- Наблюдение за изменениями в БД
- State management через ViewModel

### **Type Safety**
- Kotlin null-safety
- Sealed classes для состояний
- Generic параметры в BaseViewModel

### **Модульность**
- Четкое разделение ответственности
- Слабая связанность компонентов
- Высокая тестируемость

### **Производительность**
- Локальное хранение данных (offline-first)
- Ленивая загрузка через Flow
- Оптимизация запросов Room

## 7. Преимущества архитектуры

✅ **Масштабируемость** - легко добавлять новые функции  
✅ **Тестируемость** - каждый слой можно тестировать отдельно  
✅ **Поддерживаемость** - четкая структура кода  
✅ **Offline-first** - работает без интернета  
✅ **Современный стек** - актуальные технологии Android  

## 8. Потенциальные улучшения

- Добавление DI фреймворка (Hilt)
- Внедрение Use Cases для бизнес-логики
- Добавление кеширования
- Синхронизация с облаком
- Более детальная обработка ошибок

## Заключение

Приложение TimeWise использует современную архитектуру Android-разработки с акцентом на чистоту кода, реактивность и удобство поддержки. Выбранные технологии и паттерны являются industry standard для Android приложений в 2023-2024 годах.