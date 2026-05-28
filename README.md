        # postgresql — Что такое PostgreSQL

        Homework-шаблон для урока **l1_what_is_postgres** (Что такое PostgreSQL) на платформе Vibe Learn.

        ## Что делать

        Напиши Go-программу, которая поднимает PG через testcontainers, создаёт таблицу
`events(id bigserial, created_at timestamptz, payload jsonb)`, инсёртит 10k синтетических
событий и измеряет латентность двух запросов: SELECT по PK и SELECT по payload->>'user_id'
с GIN-индексом и без. Тесты проверят: правильную миграцию, создание индекса, корректность
p95-измерения, graceful shutdown.

## Контекст (из transfer-задачи урока)

Тебе пришёл pet-project друга, который вырос: интернет-магазин «крафтовых ножей». Стек:
Node.js + MongoDB (просто потому что «было модно»). Сейчас:

- **товары**: name, price, attributes (длина, сталь, страна), фотографии;
- **заказы**: user_id, items, total, status, created_at — нужны отчёты по периодам;
- **отзывы**: текст, рейтинг, нужен полнотекстовый поиск «острый» / «не точил»;
- **карта магазинов**: офлайн-точки с координатами, поиск «ближайшая в радиусе 5км»;
- **inventory**: остатки на складах, **нельзя продать больше, чем есть**.

## Recap из урока

- PostgreSQL — это **реляционная база + расширяемая платформа**: jsonb, array, postgis, pgvector — всё под одним сервером и одним SQL.
- Чтения **никогда не блокируют записи** благодаря MVCC: каждая транзакция видит свой snapshot. Цена — dead tuples, которые чистит VACUUM.
- Эмпирика: **CRUD сложнее, чем `id + name`? Бери PG.** Не нужна отдельная база под jsonb, гео, full-text, time-series — всё лечится расширениями.
- SQLite — embedded (один файл, один writer); MySQL — простой сетевой OLTP; **PostgreSQL — там, где нужна корректность + богатые типы**.
- PG почти никогда не теряет данные молча: ANSI SQL strict mode, FK реально проверяются, constraint-ы блокируют грязные данные на уровне БД, а не в коде.

        ## Как работать

        1. Платформа Vibe Learn создаёт копию этого репо в твоём GitHub-аккаунте по клику «Начать домашку» на странице урока (через GitHub `/generate`, codecrafters-pattern).
        2. Склонируй копию локально, реализуй TODO в `main.go`, прогони тесты, запушь.
        3. CI (`.github/workflows/ci.yml`) запускает `go vet` + `go test ./...` на каждый push. Платформа слушает результат через webhook от GitHub Actions и обновляет статус домашки на странице урока.

        ## Локальное окружение

        - Go 1.22+
        - Docker + docker-compose — `docker compose up -d` поднимает single-node PostgreSQL 16 на `localhost:5432` с healthcheck. DSN: `postgres://postgres:postgres@localhost:5432/postgres`. Переопределяется через env `DATABASE_URL`.

        ## Запуск

        ```bash
        # Поднять локальный PostgreSQL
        docker compose up -d

        # Прогнать тесты (интеграционный включается через PG_INTEGRATION=1)
        go test ./...
        PG_INTEGRATION=1 go test ./...

        # Запустить main (печатает marker; замени stub на реализацию)
        go run .
        ```

        ## Заметка автора

        Это baseline-шаблон, сгенерированный платформой. Бизнес-сущность задачи (что конкретно реализовать в `main.go`, какие тесты сделать строгими) расширяется по ходу итераций — параллельно с углублением теории урока.
