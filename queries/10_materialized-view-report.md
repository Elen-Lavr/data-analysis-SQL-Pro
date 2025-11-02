### :exclamation: Задача 10
```txt
Создайте материализованное представление, которое будет хранить отчет следующей структуры:
  * идентификатор проекта,
  * название проекта,
  * дата последней фактической оплаты по проекту,
  * размер последней фактической оплаты,
  * ФИО руководителей проектов,
  * названия контрагентов в виде строки названия типов работ контрагентов.
```
⚠️ Всегда следует помнить, что каждый шаг выполнения запроса оценивается временем ЦП, использованием памяти и стоимостью.

Преимущество обычного представления состоит в том, что оно не занимает много места. Обновлять их можно вручную или установить обновление по расписанию, или же посредством триггеров, тогда данные будут актуальны. Однако  обычное представление  уступает в скорости и производительности. 

Материализованные представления существенно снижают стоимость для разработчиков. Результаты, полученные в материализованном представлении, сохраняются в памяти. При этом материализованные представления могут использоваться только на чтение.

Поэтому вопрос стоит всегда: стоят ли несколько секунд дополнительного времени для сохранения копии данных?
### :paperclip: SQL-запрос
```sql
create materialized view project_report as
with last_payments as (	
	select project_id,
		fact_transaction_timestamp, 
	    amount
	from  project_payment
	where fact_transaction_timestamp is not null and payment_type = 'Финальный'
	order by project_id, fact_transaction_timestamp desc
	),
customer_works as (	
	select c.customer_id,
           string_agg(distinct tw.type_of_work_name, ', ' order by tw.type_of_work_name) as work_types
    from customer c
    left join customer_type_of_work ctw on c.customer_id = ctw.customer_id
    left join type_of_work tw on ctw.type_of_work_id = tw.type_of_work_id
    group by c.customer_id
    )              		
select p.project_id, p.project_name, lp.fact_transaction_timestamp as last_payment_date, lp.amount as last_payment_amount, pe.full_fio as project_manager, c.customer_name, cw.work_types
from last_payments lp
left join project p  on lp.project_id = p.project_id
left join customer c on p.customer_id = c.customer_id
left join customer_works cw on c.customer_id = cw.customer_id
left join employee e on p.project_manager_id = e.employee_id
left join person pe on e.person_id = pe.person_id
order by project_id 
```
### :heavy_check_mark: Результат выполнения
```sql
/*
project_id|project_name            |last_payment_date      |last_payment_amount|project_manager                 |customer_name                    |work_types                                                              |
----------+------------------------+-----------------------+-------------------+--------------------------------+---------------------------------+------------------------------------------------------------------------+
         1|Вершина Прогресса       |2022-09-11 19:27:09.444|         3809874.20|Новиков Николай Николаевич      |In Institute                     |Девелопмент                                                             |
         2|СтройЭксперт            |2024-01-06 21:56:25.461|         7266661.80|Горшков Григорий Григорьевич    |Risus Quisque PC                 |Генеральное проектирование, Управление, Управление проектами            |
         4|Небосвод                |2022-10-20 04:04:16.465|         4797527.80|Кузнецов Кузьма Кузьмич         |Nisi Aenean Ltd                  |Девелопмент, Коммерческие интерьеры                                     |
         5|Золотой Угол            |2022-12-02 21:00:51.611|         2328393.80|Сидоров Сидор Сидорович         |Aliquet Lobortis LLC             |Генеральное проектирование, Девелопмент, Управление проектами           |
         6|АрхиСтрой               |2023-05-19 15:23:52.020|         6032094.80|Николаев Николай Николаевич     |Sed Turpis Nec Consulting        |Девелопмент, Коммерческие интерьеры                                     |
         8|ГрандСтрой              |2023-10-17 17:01:25.749|         7513575.20|Иванов Иван Иванович            |Habitant Morbi Tristique Corp.   |Градостроительные проекты, Девелопмент, Проектирование                  |
         9|Стальные Высоты         |2023-06-29 18:46:28.239|         7019748.40|Николаев Николай Николаевич     |Eget Tincidunt Industries        |Девелопмент, Проектирование, Управление                                 |
        10|МонолитМастер           |2024-03-09 11:18:43.431|         6772835.00|Михайлов Михаил Михайлович      |Congue Elit Sed Ltd              |Генеральное проектирование, Управление, Управление проектами            |
...
*/
