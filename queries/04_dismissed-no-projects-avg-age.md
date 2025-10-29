### :exclamation: Задача 4
```txt
Получите среднее значение полных лет сотрудников, которые уволены и не задействованы на проектах.
В результат вывести одно среднее значение.
Если получаете null, то в результат нужно вывести 0.
```
⚠️ Эта задача — наглядный пример того, как важно глубоко понимать бизнес-требования. Со стороны она выглядит простой, но чем больше вникаешь, тем яснее становится истинная суть запроса.

Решение потребовало очень вдумчивого подхода. Прежде чем ментор одобрил окончательный вариант, я предоставила ему около десяти разных решений. Каждый раз, когда моё решение отклонялось, ответ был один и тот же: «Ложный запрос», и больше никаких комментариев. Я ломала голову и даже сердилась. Но не сдавалась и продолжала перечитывать задачу вновь и вновь, пока не получила инсайт, а с ним и верное решение.

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
	cte5 as (-- уволенные + не задействованные в проектах
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
