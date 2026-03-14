# Python review 2 Django web service


## Требования
* Python 3.11+
* Docker (для контейнерного запуска)

Модель обучена на базе `wiki_movie_plots_deduped.csv`, и она ищет похожие фильмы по тексту статьи Wikipedia. Асинхронные задачи обучения/инференса поддерживаются через Celery. Статус задач можно посмотреть по `task_id`.

## Запуск через docker-compose
1. Сборка и запуск контейнеров:
   ```bash
   docker-compose up --build
   ```
2. После старта сервисы доступны по адресам:
- **Frontend (Nginx, точка входа в приложение):** `http://localhost:8000`
- **Flower (мониторинг Celery):** `http://localhost:5555`
- **PostgreSQL (порт наружу):** `localhost:5433`

Nginx проксирует запросы из `:8000` в Django backend (`frontend/nginx.conf`).

3. Остановка и удаление контейнеров:
   ```bash
   docker-compose down -v
   ```

## Контейнеры в `docker-compose.yml`

- `frontend` — Nginx
- `backend` — Django-приложение
- `celery-worker` — обработка фоновых задач
- `flower` — UI мониторинга очередей Celery
- `redis` — брокер и backend результатов Celery
- `db` — PostgreSQL


## Функционал сервиса
1. `http://localhost:8000/train` — обучите модель перед началом работы.
2.  Вернитесь на начальную страницу `http://localhost:8000/` и введите ссылку на фильм из Википедии (на английском языке) в специальном поле.
3. Выберите, какое количество похожих фильмов необходимо вывест (5/10/15/20)
4. Посмотреть статус задачи можно по ссылке: `http://localhost:8000/status/<task_id>/`.


После обучения модель сохраняется в `model.pickle` и `data.npz` и используется для поиска похожих фильмов.
