### :exclamation: Задача 4
```txt
Получите среднее значение полных лет сотрудников, которые уволены и не задействованы на проектах.
В результат вывести одно среднее значение.
Если получаете null, то в результат нужно вывести 0.
```
### :paperclip: SQL-запрос
```sql
with cte1 as (--все уникальные id
		select distinct(unnest (array_append(employees_id, project_manager_id))) as employee_id
		from project),
	cte2 as (-- задействованы в проекте
		select distinct(unnest (array_append(employees_id, project_manager_id))) as involved
		from project
		where status = 'В работе'),
	cte3 as (-- незадействованы в проекте
		select employee_id from cte1
		except
		select involved from cte2),
	cte4 as (-- кто уволен
		select employee_id
		from employee
		where dismissal_date is not null),
	cte5 as (--уволенные+незадействованные в проектах
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
### :heavy_check_mark: Результат выполнения

|average_age|
|-----|
|40|
