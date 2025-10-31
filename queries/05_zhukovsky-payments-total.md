### :exclamation: Задача 5
```txt
Чему равна сумма полученных платежей от контрагентов из Жуковский, Россия.
В результат вывести одно значение суммы.
```
### :paperclip: SQL-запрос
```sql
select coalesce(sum(pp.amount), 0) as total_amount
from project_payment pp 
join project p on pp.project_id = p.project_id
join customer c on p.customer_id = c.customer_id
join address a on c.address_id = a.address_id
join city c3 on a.city_id = c3.city_id
join country c2 on c3.country_id = c2.country_id
where c3.city_name = 'Жуковский' and c2.country_name = 'Россия'
and pp.fact_transaction_timestamp is not null;
```
### :heavy_check_mark: Результат выполнения

|total_amount|
|-----|
|31784754.79|
