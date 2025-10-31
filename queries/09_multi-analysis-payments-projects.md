### :exclamation: Задача 9
```txt
Сделайте сквозную нумерацию фактических платежей по проектам на каждый год в отдельности в порядке даты платежей. Получите платежи, сквозной номер которых кратен 5.
Выведите скользящее среднее размеров платежей с шагом 2 строки назад и 2 строки вперед от текущей.
Получите сумму скользящих средних значений.
Получите сумму проектов на каждый год.
Выведите в результат значение года (годов) и сумму проектов, где сумма проектов меньше,
чем сумма скользящих средних значений.
```
### :paperclip: SQL-запрос
```sql
with cte1 as (
			select amount, fact_transaction_timestamp,
			row_number() over (partition by date_trunc('year', fact_transaction_timestamp) order by fact_transaction_timestamp::date) as numbering, -- сквозная нумерация фактических платежей по проектам на каждый год в отдельности в порядке даты платежей
			sum (amount) over (partition by date_trunc('year', fact_transaction_timestamp)) as sum_projects_year--стоимост проектов на каждый год
			from project_payment pp
			where fact_transaction_timestamp is not null
/*amount   |year_ |numbering|sum_projects_year|
-----------+------+---------+-----------------+
10899992.70|2022.0|        1|     546687728.07|
 6085181.40|2022.0|        2|     546687728.07|
10159252.50|2022.0|        3|     546687728.07|
 3633330.90|2022.0|        4|     546687728.07|
 3380656.33|2022.0|        5|     546687728.07|*/	
			),
cte2 as	(
		select*,
		avg(amount) over (order by fact_transaction_timestamp rows between 2 preceding and 2 following)  as moving_age
		from cte1
		where numbering % 5 = 0 -- платежи, сквозной номер которых кратен 5
		),	
--select *
--from cte2
/*
amount     |fact_transaction_timestamp|numbering|sum_projects_year|moving_age          |
-----------+--------------------------+---------+-----------------+--------------------+
 3380656.33|   2022-04-11 03:01:32.537|        5|     546687728.07|2743756.910000000000|
 4056787.60|   2022-05-04 02:32:57.417|       10|     546687728.07|4227260.707500000000|
  793826.80|   2022-05-25 20:49:24.866|       15|     546687728.07|4006252.686000000000|
 8677772.10|   2022-06-07 16:23:33.224|       20|     546687728.07|4250861.620000000000|
 */
cte3 as(
		select*,
		sum(moving_age) over() as sum_moving_age -- сумма скользящих средних значений
		from cte2
		)
/*
amount     |fact_transaction_timestamp|numbering|sum_projects_year|moving_age          |sum_moving_age        |
-----------+--------------------------+---------+-----------------+--------------------+----------------------+
 3380656.33|   2022-04-11 03:01:32.537|        5|     546687728.07|2743756.910000000000|310755623.303500000000|
 4056787.60|   2022-05-04 02:32:57.417|       10|     546687728.07|4227260.707500000000|310755623.303500000000|
  793826.80|   2022-05-25 20:49:24.866|       15|     546687728.07|4006252.686000000000|310755623.303500000000|
 8677772.10|   2022-06-07 16:23:33.224|       20|     546687728.07|4250861.620000000000|310755623.303500000000|
 3122220.60|   2022-06-14 11:24:22.512|       25|     546687728.07|4240244.300000000000|310755623.303500000000|
 */
select  extract(year from fact_transaction_timestamp), sum_projects_year
from cte3
where sum_projects_year < sum_moving_age 
group by sum_projects_year, extract(year from fact_transaction_timestamp)
/*
sum_projects_year|extract|
-----------------+-------+
     200054090.14|   2024|
 */

--мой вариант без комментов
with cte1 as (
		select amount, fact_transaction_timestamp,
			row_number() over (partition by date_trunc('year', fact_transaction_timestamp) order by fact_transaction_timestamp::date) as numbering,
			sum (amount) over (partition by date_trunc('year', fact_transaction_timestamp)) as sum_projects_year
		from project_payment pp
		where fact_transaction_timestamp is not null
			),
cte2 as	(
		select*,
			avg(amount) over (order by fact_transaction_timestamp rows between 2 preceding and 2 following)  as moving_age
		from cte1
		where numbering % 5 = 0 -- платежи, сквозной номер которых кратен 5
		),
cte3 as(
		select*,
			sum(moving_age) over() as sum_moving_age
		from cte2
		)
select sum_projects_year, extract(year from fact_transaction_timestamp) as year_
from cte3
where sum_projects_year < sum_moving_age 
group by sum_projects_year, extract(year from fact_transaction_timestamp);
```
### :heavy_check_mark: Результат выполнения

|sum_projects_year|year_|
|-----|--_--|
|200054090.14|2024
