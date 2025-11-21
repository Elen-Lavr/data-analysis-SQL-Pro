### :exclamation: Задача
```txt
Используя рекурсию посчитайте сумму фактических окладов сотрудников из
структурного подразделения с id равным 17 и всех дочерних подразделений.
В результат вывести одно значение суммы.
```
### :paperclip: SQL-запрос
```sql
-- 8. Используя рекурсию посчитайте сумму фактических окладов сотрудников из структурного подразделения с id равным 17 и всех дочерних подразделений.
-- В результат вывести одно значение суммы.

with recursive r as (
-- стартовая часть
    select *, 0 as level
    from company_structure cs
    where unit_id = 17
    /*стартовая точка
unit_id|parent_id|unit_name|unit_type|
-------+---------+---------+---------+
     17|        4|Дизайна  |        2|  */  
    union 
-- рекурсивная часть
    select cs.*, level+1 as level
    from r
    join company_structure cs on cs.parent_id = r.unit_id -- идем по иерархии вниз
    )
--select*
--from r
/*unit_id|parent_id|unit_name|unit_type|level|
-------+---------+---------+---------+-----+
     17|        4|Дизайна  |        2|    0|
     83|       17|Группа №1|        4|    1|
     84|       17|Группа №2|        4|    1|*/
select sum (salary) as сумма_фактических_окладов
from r
join position po on r.unit_id = po.unit_id
join employee_position ep on po.position_id  = ep.position_id
join employee e on ep.employee_id  = e.employee_id;
```
### :heavy_check_mark: Результат выполнения

|сумма_фактических_окладов|
|-----|
|4050000|
