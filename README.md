# Задание 5 — Серверная часть (CRUD)

## Ветка: `feature/PROJ-005-server-crud`

---

## Что сделано в этой ветке

Реализована структурированная серверная архитектура на Node.js + Express с разделением на слои: маршруты, контроллеры, сервисы, middleware.

---

## Созданные файлы

```
landing/server/
├── index.js                        — точка входа сервера
├── routes/
│   ├── authRoutes.js               — маршруты авторизации
│   ├── trackRoutes.js              — маршруты треков
│   ├── albumRoutes.js              — маршруты альбомов
│   └── profileRoutes.js            — маршруты профиля
├── controllers/
│   ├── authController.js           — обработка auth запросов
│   ├── trackController.js          — обработка track запросов
│   ├── albumController.js          — обработка album запросов
│   └── profileController.js        — обработка profile запросов
├── services/
│   ├── userService.js              — бизнес-логика пользователей
│   ├── trackService.js             — бизнес-логика треков
│   ├── albumService.js             — бизнес-логика альбомов
│   ├── tokenService.js             — JWT токены
│   └── passwordService.js          — хеширование паролей
└── middleware/
    └── auth.js                     — requireAuth, optionalAuth
```

---

## API маршруты

| Метод | Маршрут | Описание |
|-------|---------|----------|
| POST | /api/auth/register | Регистрация |
| POST | /api/auth/login | Вход |
| POST | /api/auth/logout | Выход |
| GET | /api/me | Текущий пользователь |
| DELETE | /api/users/me | Удалить аккаунт |
| GET | /api/tracks | Треки пользователя |
| POST | /api/upload | Загрузить mp3 |
| POST | /api/tracks/delete | Удалить треки |
| GET | /api/search | Поиск треков |
| GET | /api/albums | Список альбомов |
| POST | /api/albums | Создать альбом |
| GET | /api/albums/:id | Получить альбом |
| PATCH | /api/albums/:id | Обновить альбом |
| DELETE | /api/albums/:id | Удалить альбом |
| POST | /api/albums/:id/tracks | Добавить трек в альбом |
| DELETE | /api/albums/:id/tracks/:trackId | Удалить трек из альбома |
| GET | /api/profile/me | Профиль пользователя |
| GET | /music/:ownerId/:filename | Стриминг аудио |

---

## Запуск сервера

```bash
cd landing/server
npm install
node index.js
```

Сервер запустится на `http://localhost:8787`

---

## Технологии

- **Node.js** — среда выполнения
- **Express** — веб-фреймворк
- **pg** — PostgreSQL клиент
- **bcrypt** — хеширование паролей
- **jsonwebtoken** — JWT токены
- **multer** — загрузка файлов
- **cookie-parser** — работа с cookie
- **cors** — CORS заголовки
- **dotenv** — переменные окружения

---

## Коммиты ветки

- `feat(middleware): add requireAuth and optionalAuth middleware`
- `feat(services): add tokenService with JWT sign and verify`
- `feat(services): add passwordService with bcrypt hash and verify`
- `feat(services): add userService with register, login, getMe, deleteUser`
- `feat(services): add trackService with getUserTracks, createTrack, deleteTracks, search`
- `feat(services): add albumService with full albums CRUD and track relations`
- `feat(controllers): add authController for register, login, logout, me, delete`
- `feat(controllers): add trackController with upload, stream, delete, search`
- `feat(controllers): add albumController with CRUD and track management`
- `feat(controllers): add profileController for private and public profile`
- `feat(routes): add auth routes for register, login, logout, me, delete`
- `feat(routes): add track routes for upload, stream, delete, search`
- `feat(routes): add album routes with full CRUD and track relations`
- `feat(routes): add profile routes for private and public profile`
- `refactor(server): restructure index.js with routes, controllers, services`
=======
# Задание 4 — Структура базы данных

## Ветка: `feature/PROJ-004-database-structure`

---

## Что сделано в этой ветке

Спроектирована и создана структура базы данных PostgreSQL для проекта Melody.

---

## Созданные файлы

```
landing/server/db/
├── schema.sql   — SQL-схема всех таблиц, индексов и ограничений
├── pool.js      — создание пула соединений PostgreSQL
└── init.js      — инициализация БД при запуске сервера
DB_STRUCTURE.md  — документация структуры БД
```

---

## Таблицы базы данных

### `users` — пользователи
| Поле | Тип | Описание |
|------|-----|----------|
| id | UUID | Первичный ключ |
| email | TEXT | Email (уникальный) |
| password_hash | TEXT | Хэш пароля (bcrypt) |
| display_name | TEXT | Имя пользователя (уникальное) |
| bio | TEXT | Описание профиля |
| avatar_url | TEXT | Ссылка на аватар |
| created_at | TIMESTAMPTZ | Дата регистрации |
| updated_at | TIMESTAMPTZ | Дата обновления |

### `tracks` — музыкальные треки
| Поле | Тип | Описание |
|------|-----|----------|
| id | UUID | Первичный ключ |
| user_id | UUID | Владелец трека |
| title | TEXT | Название трека |
| storage_name | TEXT | Имя файла на диске |
| original_name | TEXT | Оригинальное имя файла |
| mime_type | TEXT | MIME-тип (audio/mpeg) |
| file_path | TEXT | Путь к файлу |
| file_size | BIGINT | Размер в байтах |
| duration | INTEGER | Длительность в секундах |

### `albums` — альбомы
| Поле | Тип | Описание |
|------|-----|----------|
| id | UUID | Первичный ключ |
| user_id | UUID | Владелец альбома |
| name | TEXT | Название альбома |
| description | TEXT | Описание |
| cover_url | TEXT | Обложка |

### `album_tracks` — связь альбомов и треков (many-to-many)
| Поле | Тип | Описание |
|------|-----|----------|
| album_id | UUID | Ссылка на альбом |
| track_id | UUID | Ссылка на трек |
| position | INTEGER | Порядок в альбоме |
| added_at | TIMESTAMPTZ | Дата добавления |

---

## Связи между таблицами

```
users (1) ──→ tracks (many)
users (1) ──→ albums (many)
albums (many) ←→ tracks (many)  через album_tracks
```

При удалении пользователя — каскадное удаление треков и альбомов (`ON DELETE CASCADE`).

---

## Как применяется схема

Схема применяется автоматически при запуске сервера:

```javascript
import { createPool, initDb } from './db/init.js';
const pool = createPool(process.env.DATABASE_URL);
await initDb(pool);
```

---

## Коммиты ветки

- `feat(db): add schema.sql with users, tracks, albums, album_tracks tables`
- `feat(db): add pool.js for PostgreSQL connection`
- `feat(db): add init.js to apply schema on server startup`
- `docs(db): add database structure description with tables and relations`

