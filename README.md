Домашнее задание к занятию 12.5. «Индексы» - `Жевлаков Анатолий`


### Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

```
SELECT SUM(DATA_LENGTH), SUM(INDEX_LENGTH), (SUM(INDEX_LENGTH) / SUM(DATA_LENGTH)) * 100 FROM information_schema.TABLES t 
WHERE INDEX_LENGTH != 0 AND DATA_LENGTH != 0;

```

### Задание 2

Выполните explain analyze следующего запроса:

```
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id

```
перечислите узкие места;
оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

Анализ выдал Table Scan on, что говорит о просмотре полностью всей таблицы, что замедляет выполнение запроса. Так же тут присутствует лишняя таблица film. 
С Join on запрос отрабатывает в разы быстрее

```
SELECT distinct concat(c.last_name, ' ', c.first_name), sum(p.amount)
FROM payment p
JOIN rental r ON p.payment_date = r.rental_date 
JOIN inventory i ON i.inventory_id = r.inventory_id
JOIN customer c ON r.customer_id = c.customer_id 
WHERE date(p.payment_date) = '2005-07-30'
GROUP BY c.customer_id

```


