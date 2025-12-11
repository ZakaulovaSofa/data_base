# Лабораторная работа №3

Представления и процедуры

**Цель:** Освоение механизмов абстракции данных и программных модулей


## Создание представления `users_reservations`

```sql
CREATE VIEW zakaulova.users_reservations AS
SELECT c.customer_id,
       c.first_name, 
       c.last_name, 
       b.title, 
       r.start_date
FROM zakaulova.customer c
JOIN zakaulova.reservation r
  ON r.customer_id = c.customer_id
JOIN zakaulova.book b
  ON b.book_id = r.book_id
ORDER BY c.last_name, c.first_name, r.start_date;
```

**Описание кода:**

* Представление `users_reservations` объединяет три таблицы:

  * `customer` – информация о клиентах;
  * `reservation` – информация о бронированиях;
  * `book` – информация о книгах.
* Используется **JOIN** для связывания таблиц по внешним ключам:

  * `customer_id` связывает клиентов с их бронированиями,
  * `book_id` связывает бронирования с конкретными книгами.
* Выбираются только необходимые поля: `customer_id`, `first_name`, `last_name`, `title`, `start_date`.
* Результаты сортируются по фамилии и имени клиента, а также по дате бронирования.

**Соответствие задачам:**

* Создано представление для удобного доступа к данным о бронированиях клиентов.
* Представление агрегирует сложный запрос, делая его простым для повторного использования.

---

## Создание хранимой функции `get_user_reservations`

```sql
CREATE OR REPLACE FUNCTION zakaulova.get_user_reservations(p_customer_id INT)
RETURNS TABLE (
    first_name VARCHAR,
    last_name VARCHAR,
    title VARCHAR,
    start_date DATE
) AS $$
BEGIN
    RETURN QUERY
    SELECT ur.first_name, ur.last_name, ur.title, ur.start_date
    FROM zakaulova.users_reservations ur
    WHERE ur.customer_id = p_customer_id
    ORDER BY ur.start_date;
END;
$$ LANGUAGE plpgsql;
```

**Описание кода:**

* Функция принимает параметр `p_customer_id` – идентификатор клиента.
* Возвращает таблицу с полями: `first_name`, `last_name`, `title` и `start_date`.
* Использует **представление `users_reservations`** для получения всех бронирований конкретного клиента.
* Сортирует результат по дате начала бронирования.

**Соответствие задачам:**

* Реализована хранимая процедура (функция) с параметром.
* Функция абстрагирует сложный SQL-запрос, возвращая готовый набор данных для конкретного клиента.

---

## Итог лабораторной работы

1. Созданное представление `users_reservations` позволяет легко получать информацию о бронированиях клиентов, объединяя данные из нескольких таблиц.
2. Хранимая функция `get_user_reservations` демонстрирует использование параметров и повторное использование представления для выборки данных.
3. Лабораторная работа показала, как с помощью представлений и процедур можно скрыть сложные запросы и сделать интерфейс данных более удобным для пользователей.

---
