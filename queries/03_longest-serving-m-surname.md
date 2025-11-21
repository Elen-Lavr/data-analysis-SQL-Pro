### :exclamation: Задача
```txt
Получите сотрудников, у которого фамилия начинается на М, всего в фамилии 8 букв и который работает дольше других.
Если таких сотрудников несколько, выведите одного случайного.
В результат выведите два столбца, в первом должны быть имя и фамилия через пробел, во втором дата найма.
```
На мой взгляд, задачи работы с текстом позволяют проявить творческий подход! Они многовариантны и предоставляют простор для импровизации. Работа с текстом в SQL — это искусство находить баланс между читаемостью, производительностью и элегантностью. Я написала восемь вариантов решения, и это лишь часть из возможных 💗

### :paperclip: SQL-запрос
Вариант 1
```sql
select concat_ws ( ' ', last_name, first_name) as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where p.last_name::text like 'М%' and length(last_name) = 8
order by ("Дата найма") asc, random()
limit 1;
```
Вариант 2

Оптимальная производительность💫
```sql
select concat_ws ( ' ', last_name, first_name) as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where p.last_name like 'М_______'
order by e.hire_date asc, random()
limit 1;
```
Вариант 3
```sql
select p.first_name || ' ' || p.last_name as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where p.last_name like 'М_______'
order by e.hire_date asc, random()
limit 1;
```
Вариант 4
```sql
select concat_ws(' ', first_name, last_name) as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where substring(last_name from 1 for 1) = 'М' 
  and length(last_name) = 8
order by e.hire_date asc, random()
limit 1;
```
Вариант 5
```sql
select concat_ws(' ', first_name, last_name) as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where substring(last_name from 1 for 8) = last_name 
  and length(last_name) = 8
  and last_name like 'М%'
order by e.hire_date asc, random()
limit 1;
```
Вариант 6
```sql
select concat_ws(' ', first_name, last_name) as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where substring(last_name from 1 for 1) = 'М' 
  and substring(last_name from 2 for 7) ~ '^[A-Za-zА-Яа-я]{7}$'
order by e.hire_date asc, random()
limit 1;
```
Вариант 7
```sql
select concat_ws(' ', first_name, last_name) as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where last_name ~ '^М.{7}$'
order by e.hire_date asc, random()
limit 1;
```
Вариант 8
```sql
select concat_ws(' ', first_name, last_name) as "Имя Фамилия", e.hire_date as "Дата найма"
from person p
join employee e on p.person_id = e.person_id
where left(last_name, 1) = 'М' and length(last_name) = 8
order by e.hire_date asc, random()
limit 1;
```
### :heavy_check_mark: Результат выполнения

|Имя Фамилия    |Дата найма|
|---------------|----------|
|Михайлов Михаил|2015-09-11|
