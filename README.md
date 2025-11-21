<img src="https://media.giphy.com/media/hvRJCLFzcasrR4ia7z/giphy.gif" width="28"> &nbsp; [![Taplink](https://img.shields.io/badge/Давайте_работать_вместе!-Taplink-FF69B4?style=for-the-badge&logo=linktree)](https://lawlena.taplink.ws)

# Data-Analysis-SQL-Pro
🏭 ***Анализ данных компании: от кадров до финансов.***

В этом репозитории представлены решения комплексных аналитических задач для бизнеса, охватывающих кадровую аналитику, финансовые расчеты, операционную эффективность и глубокий анализ данных с использованием SQL.

🎯 ***Ключевые особенности анализа:***
  * работа с иерархическими данными - рекурсивные запросы для анализа организационной структуры;
  * временной анализ - расчет стажа, возраста, накопительных итогов;
  * финансовое моделирование - расчет бонусов, анализ платежей, скользящие средние;
  * комплексная фильтрация - многоуровневые условия отбора данных;
  * оптимизация отчетности - использование материализованных представлений для часто запрашиваемых данных.

🧰 ***Применяемые технологии и методы:***
  * Язык: SQL (ориентирован на PostgreSQL);
  * Ключевые концепции:
    * сложная агрегация данных и фильтрация,
    * рекурсивные запросы для иерархических данных,
    * оконные функции (накопительный итог, сквозная нумерация, скользящее среднее),
    * работа с датами и временем,
    * материализованные представления,
    * подзапросы и CTE (Common Table Expressions).

📚 ***Исходные [данные](./data/stroy.backup) и [описание](./data/description.md) базы данных Stroy***

🗃️ ***Схема базы данных***

```mermaid
erDiagram
    COUNTRY {
        int country_id PK
        varchar country_name
    }

    CITY {
        int city_id PK
        varchar city_name
        int country_id FK
    }

    ADDRESS {
        int address_id PK
        varchar address
        int city_id FK
    }

    PERSON {
        int person_id PK
        varchar first_name
        varchar last_name
        varchar middle_name
        date birthdate
        varchar phone_number
        varchar email
        int address_id FK
    }

    CUSTOMER {
        int customer_id PK
        varchar customer_name
        varchar phone_number
        varchar email
        int address_id FK
    }

    TYPE_OF_WORK {
        int type_of_work_id PK
        varchar type_name
    }

    CUSTOMER_TYPE_OF_WORK {
        int customer_id FK
        int type_of_work_id FK
    }

    UNIT_TYPE {
        int unit_type_id PK
        varchar type_name
    }

    COMPANY_STRUCTURE {
        int unit_id PK
        int parent_id FK
        varchar unit_name
        int unit_type FK
    }

    POSITION {
        int position_id PK
        varchar position_name
        int manager_position_id FK
        int unit_id FK
        int grade
        boolean to_wearst
    }

    GRADE_SALARY {
        int grade PK
        numeric min_salary
        numeric max_salary
    }

    EMPLOYEE {
        int employee_id PK
        int person_id FK
    }

    EMPLOYEE_POSITION {
        int employee_id FK
        int position_id FK
        numeric salary
        numeric rate
        date start_date
    }

    EMPLOYEE_SALARY_HISTORY {
        int employee_salary_history_id PK
        int employee_id FK
        int position_id FK
        numeric salary_old
        numeric salary_new
        date start_date
        date end_date
    }

    PROJECT {
        int project_id PK
        varchar project_name
        int customer_id FK
        varchar project_message_id
        int employees_id FK
        numeric project_cost
        date sign_date
        text description
    }

    PROJECT_PAYMENT {
        int project_payment_id PK
        int project_id FK
        numeric amount
        varchar payment_type
        date plan_payment_date
        timestamp fact_transaction_timestamp
    }

    COUNTRY ||--o{ CITY : has
    CITY ||--o{ ADDRESS : located_in
    ADDRESS ||--o{ PERSON : used_by
    ADDRESS ||--o{ CUSTOMER : used_by
    PERSON ||--|| EMPLOYEE : is
    CUSTOMER ||--o{ PROJECT : orders
    CUSTOMER }o--o{ TYPE_OF_WORK : specializes_in
    COMPANY_STRUCTURE ||--o{ POSITION : contains
    UNIT_TYPE ||--o{ COMPANY_STRUCTURE : classifies
    COMPANY_STRUCTURE ||--o{ COMPANY_STRUCTURE : reports_to
    POSITION ||--o{ POSITION : manages
    GRADE_SALARY ||--o{ POSITION : defines_salary_range
    EMPLOYEE }o--o{ POSITION : holds
    EMPLOYEE_POSITION }|--|| EMPLOYEE_SALARY_HISTORY : tracks_changes
    PROJECT ||--o{ PROJECT_PAYMENT : receives_payments
    EMPLOYEE ||--o{ PROJECT : manages
```

📝 ***SQL запросы с решениями и результатами:***

<div class="table-container">
    <table class="solutions-table">
        <thead>
            <tr>
                <th>Категория</th>
                <th>SQL Концепции</th>
                <th>Аналитические задачи и решения</th>
                <th>Ссылка на решение</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td rowspan="1"><strong>Анализ проектной деятельности</strong></td>
                <td>Агрегация, фильтрация по датам</td>
                <td>Анализ проектной активности: количество заключенных контрактов в 2023 году</td>
                <td><a href="queries/01_projects-2023.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Кадровая аналитика</strong></td>
                <td>Работа с датами, агрегация</td>
                <td>Демографический анализ нового найма: суммарный возраст сотрудников, присоединившихся в 2022</td>
                <td><a href="queries/02_hired-2022-age-sum.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Поиск и фильтрация сотрудников</strong></td>
                <td>Строковые функции, сортировка, ограничение результатов</td>
                <td>Поиск ключевых сотрудников: самый опытный работник с фамилией на М (8 букв)</td>
                <td><a href="queries/03_longest-serving-m-surname.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Статистика по сотрудникам</strong></td>
                <td>Условные выражения, агрегатные функции</td>
                <td>Анализ уволенного персонала: средний возраст не задействованных в проектах сотрудников</td>
                <td><a href="queries/04_dismissed-no-projects-avg-age.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Финансовый анализ</strong></td>
                <td>Суммирование, JOIN таблиц, географическая фильтрация</td>
                <td>Географический анализ платежей: общая сумма поступлений от контрагентов из Жуковский, Россия</td>
                <td><a href="queries/05_zhukovsky-payments-total.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Расчет бонусов и ранжирование</strong></td>
                <td>Агрегация, JOIN, оконные функции</td>
                <td>Мотивация руководителей: определение ТОП-менеджера по бонусам за завершенные проекты</td>
                <td><a href="queries/06_top-manager-bonus-completed.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Накопительные итоги</strong></td>
                <td>Оконные функции, фильтрация по накопительной сумме</td>
                <td>Финансовое планирование: анализ накопительных авансовых платежей с помесячной детализацией</td>
                <td><a href="queries/07_cumulative-advance-payments-30m.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Рекурсивные запросы</strong></td>
                <td>Рекурсия CTE, иерархические данные</td>
                <td>Структурный анализ затрат: фонд оплаты труда подразделения ID=17 с учетом всех дочерних отделов</td>
                <td><a href="queries/08_recursive-salary-department-17.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Комплексный анализ данных</strong></td>
                <td>Оконные функции, скользящее среднее, агрегация</td>
                <td> Комплексный финансовый мониторинг: скользящие средние платежей и сравнительный анализ с бюджетом проектов</td>
                <td><a href="queries/09_multi-analysis-payments-projects.md">📁 просмотреть</a></td>
            </tr>
            <tr>
                <td rowspan="1"><strong>Создание отчетных представлений</strong></td>
                <td>Материализованные представления, JOIN множественных таблиц</td>
                <td>Оптимизация отчетности: создание материализованного представления для комплексного анализа проектов и платежей</td>
                <td><a href="queries/10_materialized-view-report.md">📁 просмотреть</a></td>
            </tr>
        </tbody>
    </table>
</div>

[queries/](./queries/) - все SQL запросы с решениями 

[![Вернуться в главный репозиторий](https://img.shields.io/badge/%E2%86%90-Главный%20репозиторий-blue)](https://github.com/Elen-Lavr)
[![Наверх](https://img.shields.io/badge/%E2%86%91-Наверх-blue)](#Data-Analysis-SQL-Pro)
