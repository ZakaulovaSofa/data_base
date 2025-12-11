# Лабораторная работа №2

Практическое развертывание базы данных и работа с SQL

## Инсталляция базы данных на сервере

---

## FOREIGN KEY для связи таблиц

Внешние ключи (foreign keys) используются для создания связей между таблицами и обеспечения ссылочной целостности — то есть гарантии, что данные остаются согласованными.

---

### book -> library
**Назначение**

Этот внешний ключ устанавливает связь между книгой и библиотекой, в которой она находится. Поле library_id в таблице book должно ссылаться на существующую библиотеку в таблице library.

**Причина существования**

Связь обеспечивает целостность данных: невозможно добавить книгу без указания библиотеки, которой она принадлежит. Также предотвращается ситуация, когда книга остаётся привязанной к несуществующей библиотеке.
Использование ON DELETE CASCADE позволяет автоматически удалять книги, если удалена связанная с ними библиотека.
```sql
ALTER TABLE zakaulova.book
ADD CONSTRAINT fk_book_library
FOREIGN KEY (library_id) REFERENCES zakaulova.library (library_id)
ON DELETE CASCADE;
```
---

### reservation -> customer

**Назначение**

Связывает бронь (reservation) с конкретным клиентом (customer). Каждая бронь должна принадлежать реальному клиенту.

**Причина существования**

Это гарантирует, что в таблице reservation не может появиться запись с клиентом, которого нет в таблице customer.
ON DELETE CASCADE обеспечивает автоматическое удаление всех броней клиента при удалении записи о нём.
```sql
ALTER TABLE zakaulova.reservation
ADD CONSTRAINT fk_reservation_customer
FOREIGN KEY (customer_id) REFERENCES zakaulova.customer (customer_id)
ON DELETE CASCADE;
```

---

### reservation -> book

**Назначение**

Обеспечивает привязку брони к конкретной книге.

**Причина существования**

Не позволяет создать бронь на книгу, которой нет в базе данных.
ON DELETE CASCADE гарантирует, что если книга удаляется, то связанные с ней брони также удаляются и не остаются в таблице как «висячие» записи.

```sql
ALTER TABLE zakaulova.reservation
ADD CONSTRAINT fk_reservation_book
FOREIGN KEY (book_id) REFERENCES zakaulova.book (book_id)
ON DELETE CASCADE;
```

---

### authors_of_books -> book

**Назначение**

Обеспечивает связь книги с авторами в таблице связей «книга ↔ автор». Каждая запись в таблице autors_of_books должна ссылаться на существующую книгу.

**Причина существования**

Предотвращает появление в связующей таблице данных о связи с книгой, которой не существует.
ON DELETE CASCADE автоматически удаляет все связи автора с книгой, если книга удаляется.

```sql
ALTER TABLE zakaulova.autors_of_books
ADD CONSTRAINT fk_authors_of_books_book
FOREIGN KEY (book_id) REFERENCES zakaulova.book (book_id)
ON DELETE CASCADE;
```

---

### authors_of_books -> writer

**Назначение**

Обеспечивает корректную связь каждого автора с книгой через таблицу authors_of_books.

**Причина существования**

Гарантирует, что каждая запись в таблице связей указывает только на реально существующего автора.
ON DELETE CASCADE автоматически удаляет связи с книгами, если автор удаляется.

```sql
ALTER TABLE zakaulova.authors_of_books
ADD CONSTRAINT fk_authors_of_books_writer
FOREIGN KEY (writer_id) REFERENCES zakaulova.writer (writer_id)
ON DELETE CASCADE;
```

---

## CHECK-ограничения

### Корректность дат автора:

**Назначение**

Проверяет корректность дат в данных об авторе.

**Причина существования**

Ограничение гарантирует, что дата рождения автора всегда предшествует дате смерти. Если дата смерти отсутствует (живой автор или дата неизвестна) — значение допускается. Это предотвращает логически неверные записи (например, «умер раньше, чем родился»).

```sql
ALTER TABLE zakaulova.writer
ADD CONSTRAINT chk_writer_dates
CHECK (
    date_of_death IS NULL OR  
    date_of_birth < date_of_death
);
```

---

### Корректность даты рождения клиента:

**Назначение**

Контролирует корректность даты рождения клиента.

**Причина существования**

Гарантирует невозможность ввода даты рождения, которая находится в будущем относительно текущей даты. Это предотвращает ошибки и исключает невозможные значения.

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

**Вывести список книг и их авторов**

Выбирает названия книг и имена авторов, связав таблицу книг zakaulova.book со связующей таблицей zakaulova.authors_of_books и затем с таблицей авторов zakaulova.writer. Для каждой пары «книга — автор» возвращается одна строка.

### Разбор по частям

**FROM zakaulova.book b**
- базовая (левая) таблица запроса. b — алиас (псевдоним) для zakaulova.book, используется дальше в запросе вместо полного имени.

**JOIN zakaulova.authors_of_books aob ON b.book_id = aob.book_id**
- это INNER JOIN (ключевое слово INNER опущено, но семантика такая же).
- соединяет книги с записями связующей таблицы по совпадению book_id.
- результат этого соединения содержит только те книги, у которых есть соответствующая запись в authors_of_books.

**JOIN zakaulova.writer w ON w.writer_id = aob.writer_id**
- второе INNER JOIN: соединяет полученные пары «книга ↔ запись в связующей таблице» с таблицей writer по writer_id.
- в результате остаются только те записи, у которых найден соответствующий автор.

**SELECT b.title AS book_title, w.first_name AS writer_first_name, w.last_name AS writer_last_name**
- выбираются три столбца: название книги и имя+фамилия автора. Через AS явно задаются заголовки (алиасы) выходных колонок.

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
