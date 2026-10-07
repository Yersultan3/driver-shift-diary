# Дневник смен водителя

Мобильное и веб-приложение для учёта поездок и подсчёта дохода за рабочую смену.

**Веб:** [driver-shift-diary.vercel.app](https://driver-shift-diary.vercel.app)  
**API:** [driver-shift-diary.up.railway.app/docs](https://driver-shift-diary.up.railway.app/docs)

---

## Стек

| Слой | Технология |
|------|------------|
| Мобильное / Веб | Flutter (Dart) |
| Бэкенд | Python, FastAPI, Uvicorn |
| Хранилище | JSON-файл на сервере |
| Деплой фронта | Vercel |
| Деплой бэкенда | Railway |

---

## Возможности

- Просмотр поездок за любой день — навигация кнопками `<` / `>`
- Дневная сводка: выручка, комиссия, чистый доход, разбивка наличные / карта
- Добавление поездки: время, сумма, тип оплаты, комиссия (15% по умолчанию)
- Защита от дублей по ID поездки
- Русская локаль для дат и форматирования чисел

---

## Архитектура

```
├── lib/
│   ├── main.dart
│   ├── models/trip.dart               # Trip, DaySummary
│   ├── services/api_service.dart      # HTTP-клиент → Railway
│   └── screens/
│       ├── home_screen.dart           # Главный экран
│       └── add_trip_screen.dart       # Форма добавления
├── server/
│   ├── main.py                        # FastAPI: /trips, /summary
│   ├── requirements.txt
│   └── tests/test_api.py
└── build/web/                         # Собранный Flutter web → Vercel
```

---

## API

| Метод | Путь | Описание |
|-------|------|----------|
| `GET` | `/trips?date=YYYY-MM-DD` | Список поездок за день |
| `GET` | `/summary?date=YYYY-MM-DD` | Сводка за день |
| `POST` | `/trips` | Добавить поездку |

Валидация: сумма > 0, конец позже начала, `payment` — `cash` или `card`, дубли по `id` → 409.

---

## Запуск локально

### Бэкенд
```bash
cd server
pip install -r requirements.txt
python -m uvicorn main:app --reload --port 8000
```

### Flutter (Android — реальное устройство)
```bash
adb reverse tcp:8000 tcp:8000
flutter run
```

### Тесты
```bash
cd server && pytest -v
flutter test
```

---

## Деплой

Бэкенд задеплоен на **Railway** — все платформы (Android, Web) подключаются к нему напрямую, локальный сервер не требуется.

Веб-версия собрана командой `flutter build web --release` и задеплоена на **Vercel**.
