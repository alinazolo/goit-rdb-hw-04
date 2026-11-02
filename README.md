# Olist E-commerce — PostgreSQL Homework 4

Навчальний проєкт з аналізу даних бразильського маркетплейсу **Olist** за допомогою PostgreSQL, SQL та Jupyter Notebook.

## 1. Джерело датасету

**Brazilian E-Commerce Public Dataset by Olist** — Kaggle:
https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

Датасет містить інформацію про замовлення, покупців, товари, продавців і відгуки.

## 2. Як отримати файли

CSV-файли можна отримати двома способами:

1. Завантажити архів із [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), розпакувати його та вказати шлях до файлів у notebook.
2. Завантажити дані безпосередньо в notebook за допомогою `kagglehub`:

```python
import kagglehub

path = kagglehub.dataset_download("olistbr/brazilian-ecommerce")
print(path)
```

Також можна використати CSV-файли з архіву навчального курсу.

## 3. Використані таблиці

### Таблиці Olist

| Таблиця | Призначення |
| --- | --- |
| `olist_customers` | Покупці та їхня географія; містить `customer_id` і `customer_unique_id` |
| `olist_orders` | Замовлення, статуси та дати покупки/доставки |
| `olist_order_items` | Товарні позиції замовлень, ціни, вартість доставки та продавці |
| `olist_products` | Характеристики й категорії товарів |
| `olist_sellers` | Продавці та їхня географія |
| `olist_order_reviews` | Оцінки й відгуки до замовлень |

### Додаткові таблиці

| Таблиця | Призначення |
| --- | --- |
| `seller_alerts` | Попередження щодо продавців |
| `seller_score` | Агреговані оцінки та сегменти продавців |
| `olist_customer_dim` | Довідник унікальних покупців |
| `customer_segments` | Сегментація покупців за поведінковими метриками |

## 4. Як запустити проєкт

1. Відкрити папку `goit-rdb-hw-04` у **VS Code**.
2. Створити й активувати віртуальне середовище Python (за потреби):

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. Встановити залежності:

   ```bash
   python -m pip install pgserver psycopg2-binary sqlalchemy pandas kagglehub jupyter ipykernel ipython-sql
   ```

4. Відкрити файл `homework.ipynb` і вибрати Python-ядро з поточного віртуального середовища.
5. Послідовно виконати комірки notebook, починаючи з підготовки PostgreSQL:

   ```python
   import pgserver
   from sqlalchemy import create_engine, text

   pg = pgserver.get_server('/tmp/hw4_pg', cleanup_mode='stop')
   engine = create_engine(pg.get_uri(), future=True)

   with engine.connect() as conn:
       print(conn.execute(text('SELECT version()')).scalar_one())
   ```

6. Завантажити CSV-файли, створити таблиці й виконати SQL-запити у порядку, наведеному в notebook.

> **Примітка:** `pgserver` запускає локальний екземпляр PostgreSQL. Для запуску notebook потрібні встановлений Python і відповідні залежності; окремий системний PostgreSQL Server не обов'язковий. Шлях `/tmp/hw4_pg` використовується для локальних даних PostgreSQL у цьому прикладі.

## 5. Зміст роботи

- Створення типізованої схеми та перевірка обмежень (`PRIMARY KEY`, `FOREIGN KEY`, `CHECK`, `UNIQUE`).
- DML-операції: `INSERT ... RETURNING`, `ON CONFLICT`, `UPDATE`, `DELETE`.
- Формування довідника покупців і поведінкових сегментів.
- Аналіз даних за допомогою `INNER JOIN`, `LEFT JOIN`, `FULL OUTER JOIN`, `SELF JOIN` та anti-join.
- Бізнес-агрегації з `GROUP BY`, `HAVING` і `STRING_AGG`.
- Порівняння наборів через `EXCEPT`, ранжування через `ROW_NUMBER()` та підсумкова рефлексія.

## 6. Важливі зауваження щодо даних

- `customer_unique_id` використовується для customer-level аналізу, оскільки `customer_id` може відрізнятися між замовленнями одного покупця.
- Для обчислення рейтингу продавців необхідно враховувати гранулярність `seller_id × order_id`, щоб не множити оцінки через кілька товарних позицій.
- Частина замовлень може не мати відгуків, а частина товарів — заповненої категорії; це потрібно враховувати в аналітичних запитах.
