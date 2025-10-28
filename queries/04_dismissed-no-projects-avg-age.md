### :exclamation: Задача 4
```txt
Получите среднее значение полных лет сотрудников, которые уволены и не задействованы на проектах.
В результат вывести одно среднее значение.
Если получаете null, то в результат нужно вывести 0.
```
⚠️ Эта задача — хороший пример того, как важно точно понимать бизнес-требования.
### :paperclip: SQL-запрос

Вариант 1
```sql
with cte1 as (--все уникальные id
		select distinct(unnest (array_append(employees_id, project_manager_id))) as employee_id
		from project),
	cte2 as (-- задействованы в проекте
		select distinct(unnest (array_append(employees_id, project_manager_id))) as involved
		from project
		where status = 'В работе'),
	cte3 as (-- не задействованы в проекте
		select employee_id from cte1
		except
		select involved from cte2),
	cte4 as (-- кто уволен
		select employee_id
		from employee
		where dismissal_date is not null),
	cte5 as (--уволенные+не задействованные в проектах
		select employee_id from cte3
		union
		select employee_id from cte4),
	cte6 as (-- присоединяем прием-увольнение, расчитываем средний возраст
		select avg(extract (year from age(current_date, p.birthdate)))::integer as avg_age
		from cte5
		left join employee e on cte5.employee_id = e.employee_id
		left join person p on e.person_id = p.person_id)
select
    case 
        when avg_age is null then 0
        else avg_age::integer
    end as average_age
from cte6 
```
Вариант 2

Более лаконичный 💫
```sql
with cte1 as ( -- не задействованные в активных проектах
    select e.employee_id
    from employee e
    where not exists (
        select 1 
        from project p 
        where p.status = 'В работе'
        and (e.employee_id = any(p.employees_id) or e.employee_id = p.project_manager_id)
    )
),
cte2 as (-- все уволенные
        select employee_id
    from employee
    where dismissal_date is not null
),
cte3 as ( -- объединяем
    select employee_id from cte1
    union
    select employee_id from cte2
)
select --расчитываем средний возраст
    coalesce(avg(extract(year from age(current_date, p.birthdate)))::integer, 0) as average_age
from cte3 
join employee e on cte3.employee_id = e.employee_id
join person p on e.person_id = p.person_id;
```

### :heavy_check_mark: Результат выполнения

|average_age|
|-----|
|40|
