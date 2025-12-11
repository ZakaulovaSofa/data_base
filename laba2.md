# Лабораторная работа №2

Практическое развертывание базы данных и работа с SQL

## Инсталляция базы данных на сервере

## FOREIGN KEY для связи таблиц

```sql
ALTER TABLE zakaulova.book
ADD CONSTRAINT fk_book_library
FOREIGN KEY (library_id) REFERENCES zakaulova.library (library_id)
ON DELETE CASCADE;

ALTER TABLE zakaulova.reservation
ADD CONSTRAINT fk_reservation_customer
FOREIGN KEY (customer_id) REFERENCES zakaulova.customer (customer_id)
ON DELETE CASCADE;

ALTER TABLE zakaulova.reservation
ADD CONSTRAINT fk_reservation_book
FOREIGN KEY (book_id) REFERENCES zakaulova.book (book_id)
ON DELETE CASCADE;

ALTER TABLE zakaulova.autors_of_books
ADD CONSTRAINT fk_authors_of_books_book
FOREIGN KEY (book_id) REFERENCES zakaulova.book (book_id)
ON DELETE CASCADE;

ALTER TABLE zakaulova.authors_of_books
ADD CONSTRAINT fk_authors_of_books_writer
FOREIGN KEY (writer_id) REFERENCES zakaulova.writer (writer_id)
ON DELETE CASCADE;
```

---

## CHECK-ограничения

### Корректность дат автора:

```sql
ALTER TABLE zakaulova.writer
ADD CONSTRAINT chk_writer_dates
CHECK (
    date_of_death IS NULL OR  
    date_of_birth < date_of_death
);
```

### Корректность даты рождения клиента:

```sql
ALTER TABLE zakaulova.customer
ADD CONSTRAINT chk_customer_birth
CHECK (
    date_of_birth < CURRENT_DATE
);
```

---

# Заполнение таблиц данными

## Таблица `library`

```sql
INSERT INTO zakaulova.library (library_id, name, address)
VALUES (1, 'Центральная', 'Ленина, 44');

INSERT INTO zakaulova.library (library_id, name, address)
VALUES (2, 'Горная', 'Улица Строителей, 5');

INSERT INTO zakaulova.library (library_id, name, address, type)
VALUES (3, 'Детская', 'Улица Комсомольцев, 32', 'Детская');
```

---

## Таблица `writer`

```sql
INSERT INTO zakaulova.writer (writer_id, first_name, last_name, country)
VALUES 
(1, 'Агата', 'Кристи', 'Англия'),
(2, 'Александр', 'Пушкин', 'Россия'),
(3, 'Ю', 'Несбе', 'Норвегия'),
(4, 'Харуки', 'Мураками', 'Япония'),
(5, 'Евгений', 'Водолазкин', 'Россия'),
(6, 'Ричард', 'Осман', 'Ангия');
```

---

## Таблица `book`

```sql
INSERT INTO zakaulova.book (book_id, library_id, title, genre)
VALUES 
(1, 1, 'Убийство в Восточном экспрессе', 'детектив'),
(2, 2, 'Немой свидетель', 'детектив'),
(3, 2, 'Убийства по алфавиту', 'детектив'),
(4, 3, 'Скака о рыбаке и рыбке', 'сказка'),
(5, 1, 'Снеговик', 'детектив'),
(6, 1, 'Норвежский лес', 'роман'),
(7, 2, 'Охота на овец', 'роман'),
(8, 1, 'Авиатор', 'роман'),
(9, 2, 'Клуб убийств по четвергам', 'детектив');
```

---

## Таблица `authors_of_books`

```sql
INSERT INTO zakaulova.authors_of_books (book_id, writer_id)
VALUES 
(1, 1),
(2, 1),
(3, 1),
(4, 2),
(5, 3),
(6, 4),
(7, 4),
(8, 5),
(9, 6);
```

---

## Таблица `reservation`

```sql
INSERT INTO zakaulova.reservation (reservation_id, customer_id, book_id, start_date, end_date)
VALUES 
(1, 1, 4, '2025-09-01', '2025-10-01'),
(2, 2, 9, '2025-09-05', '2025-10-05'),
(3, 2, 5, '2025-09-05', '2025-10-05'),
(4, 3, 2, '2025-08-20', '2025-09-20');
```

---

# Содержательный SELECT-запрос 

### **Вывести список книг и их авторов**

```sql
SELECT 
    b.title AS book_title,
    w.first_name AS writer_first_name,       
    w.last_name AS writer_last_name
FROM zakaulova.book b
JOIN zakaulova.authors_of_books aob 
    ON b.book_id = aob.book_id
JOIN zakaulova.writer w 
    ON w.writer_id = aob.writer_id;
```

### ✔️ Описание результата

Запрос соединяет **три таблицы**:

* `book`
* `authors_of_books`
* `writer`

И выводит для каждой книги её название и фамилию/имя автора.

---

# Итог лабораторной работы

В ходе выполнения лабораторной работы №2 были выполнены следующие действия:
* Созданы все таблицы на pgadmin
* Установлены все необходимые внешние ключи.
* Добавлены проверочные ограничения CHECK.
* Таблицы заполнялись корректными данными.
* Выполнен содержательный SELECT-запрос с использованием нескольких JOIN.

---
