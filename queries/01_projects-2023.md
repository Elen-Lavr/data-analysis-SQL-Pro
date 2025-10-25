### :exclamation: Задача 1
```txt
Получите количество проектов, подписанных в 2023 году.
В результат вывести одно значение количества.
```
### :paperclip: SQL-запрос
```sql
select count(*) 
from project p 
where sign_date between '2023-01-01' and '2024-01-01';
```
### :heavy_check_mark: Результат выполнения

|count|
|-----|
|54|
