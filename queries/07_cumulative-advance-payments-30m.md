### :exclamation: Задача 7
```txt
Получите накопительный итог планируемых авансовых платежей на каждый месяц в отдельности.
Выведите в результат те даты планируемых платежей, которые идут после преодоления накопительной суммой значения в 30 000 000.
В результат должна попасть дата 2022-06-23.
```
### :paperclip: SQL-запрос
```sql
 with cte as (
  select plan_payment_date, sum(amount) over (
   partition by extract(month from plan_payment_date)
  order by plan_payment_date) as накопительный_итог
  from project_payment
  where payment_type = 'Авансовый')
select distinct on (extract(month from plan_payment_date))
    plan_payment_date as "Date",
    накопительный_итог as "Accumulation"
from cte
where накопительный_итог >= 30000000
order by extract(month from plan_payment_date), plan_payment_date;
```
### :heavy_check_mark: Результат выполнения

|Date      |Accumulation|
|-----|-----|
|2024-01-17| 34659237.90|
|2024-02-25| 36140718.30|
|2023-03-06| 31007387.40|
|2023-04-20| 38733309.00|
|2023-05-25| 32648127.60|
|2022-06-23| 34237017.30|
|2022-07-17| 30425907.00|
|2023-08-07| 37411088.40|
|2022-09-30| 34129608.00|
|2022-11-20| 37462938.90|
