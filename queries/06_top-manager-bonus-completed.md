### :exclamation: Задача 6
```txt
Пусть руководитель проекта получает премию в 1% от стоимости завершенных проектов.
Если взять завершенные проекты, какой руководитель проекта получит самый большой бонус?
В результат нужно вывести идентификатор руководителя проекта, его ФИО и размер бонуса.
Если таких руководителей несколько, предусмотреть вывод всех.
```
### :paperclip: SQL-запрос
```sql
select  project_manager_id, full_fio, bonus
from (
		select project_manager_id,
			sum(project_cost* 0.01) as bonus,
			dense_rank() over (order by sum(project_cost * 0.01) desc) as rank_bonus
		from project
		where status = 'Завершен'
		group by project_manager_id
				) a
left join employee e on a.project_manager_id = e.employee_id
left join person p on e.person_id = p.person_id
where rank_bonus = 1;
```
### :heavy_check_mark: Результат выполнения

|project_manager_id|full_fio|bonus|
|-----|-----|-----|
|53|Михайлов Михаил Михайлович|904814.22|
