Установка Clickhouse по инструкции https://clickhouse.com/docs/ru/getting-started/install

Импорт тестовой БД:

```
wget https://s3.amazonaws.com/menusdata.nypl.org/gzips/2021_08_01_07_01_17_data.tgz
tar xvf 2021_08_01_07_01_17_data.tgz
clickhouse-client --query "CREATE DATABASE IF NOT EXISTS test"
```
Создание таблиц: 

```
CREATE TABLE test.dish
(
    id UInt32,
    name String,
    description String,
    menus_appeared UInt32,
    times_appeared Int32,
    first_appeared UInt16,
    last_appeared UInt16,
    lowest_price Decimal64(3),
    highest_price Decimal64(3)
) ENGINE = MergeTree ORDER BY id;

CREATE TABLE test.menu
(
    id UInt32,
    name String,
    sponsor String,
    event String,
    venue String,
    place String,
    physical_description String,
    occasion String,
    notes String,
    call_number String,
    keywords String,
    language String,
    date String,
    location String,
    location_type String,
    currency String,
    currency_symbol String,
    status String,
    page_count UInt16,
    dish_count UInt16
) ENGINE = MergeTree ORDER BY id;

CREATE TABLE test.menu_page
(
    id UInt32,
    menu_id UInt32,
    page_number UInt16,
    image_id String,
    full_height UInt16,
    full_width UInt16,
    uuid UUID
) ENGINE = MergeTree ORDER BY id;

CREATE TABLE test.menu_item
(
    id UInt32,
    menu_page_id UInt32,
    price Decimal64(3),
    high_price Decimal64(3),
    dish_id UInt32,
    created_at DateTime,
    updated_at DateTime,
    xpos Float64,
    ypos Float64
) ENGINE = MergeTree ORDER BY id;

```

<img width="368" alt="image" src="https://github.com/user-attachments/assets/80d78bf2-e04b-4d44-be74-6b84adab4004">

Импортируем данные 

```
clickhouse-client --format_csv_allow_single_quotes 0 --input_format_null_as_default 0 --query "INSERT INTO test.dish FORMAT CSVWithNames" < Dish.csv
clickhouse-client --format_csv_allow_single_quotes 0 --input_format_null_as_default 0 --query "INSERT INTO test.menu FORMAT CSVWithNames" < Menu.csv
clickhouse-client --format_csv_allow_single_quotes 0 --input_format_null_as_default 0 --query "INSERT INTO test.menu_page FORMAT CSVWithNames" < MenuPage.csv
clickhouse-client --format_csv_allow_single_quotes 0 --input_format_null_as_default 0 --date_time_input_format best_effort --query "INSERT INTO test.menu_item FORMAT CSVWithNames" < MenuItem.csv
```

Скорость запросов:

Общее количество данных
```
07409a560833 :) select count(1) from dish;

SELECT count(1)
FROM dish

Query id: 2ad93157-cece-47d0-8c0b-7664e86280c0

   ┌─count(1)─┐
1. │   428146 │
   └──────────┘

1 row in set. Elapsed: 0.006 sec. 

07409a560833 :) select count(1) from menu;

SELECT count(1)
FROM menu

Query id: f69cb972-36e4-4c68-9ae1-9f3e209597bc

   ┌─count(1)─┐
1. │    17547 │
   └──────────┘

1 row in set. Elapsed: 0.004 sec.
```

```
 select distinct name from menu;
```
<img width="823" alt="image" src="https://github.com/user-attachments/assets/db867d1f-ef4f-4a6d-9317-9c7d34caea88">

Денормализация данных
```
CREATE TABLE menu_item_denorm
ENGINE = MergeTree ORDER BY (dish_name, created_at)
AS SELECT
    price,
    high_price,
    created_at,
    updated_at,
    xpos,
    ypos,
    dish.id AS dish_id,
    dish.name AS dish_name,
    dish.description AS dish_description,
    dish.menus_appeared AS dish_menus_appeared,
    dish.times_appeared AS dish_times_appeared,
    dish.first_appeared AS dish_first_appeared,
    dish.last_appeared AS dish_last_appeared,
    dish.lowest_price AS dish_lowest_price,
    dish.highest_price AS dish_highest_price,
    menu.id AS menu_id,
    menu.name AS menu_name,
    menu.sponsor AS menu_sponsor,
    menu.event AS menu_event,
    menu.venue AS menu_venue,
    menu.place AS menu_place,
    menu.physical_description AS menu_physical_description,
    menu.occasion AS menu_occasion,
    menu.notes AS menu_notes,
    menu.call_number AS menu_call_number,
    menu.keywords AS menu_keywords,
    menu.language AS menu_language,
    menu.date AS menu_date,
    menu.location AS menu_location,
    menu.location_type AS menu_location_type,
    menu.currency AS menu_currency,
    menu.currency_symbol AS menu_currency_symbol,
    menu.status AS menu_status,
    menu.page_count AS menu_page_count,
    menu.dish_count AS menu_dish_count
FROM menu_item
    JOIN dish ON menu_item.dish_id = dish.id
    JOIN menu_page ON menu_item.menu_page_id = menu_page.id
    JOIN menu ON menu_page.menu_id = menu.id;
```
<img width="784" alt="image" src="https://github.com/user-attachments/assets/a2eed1f3-bdca-4100-bc11-5b7efe0698b7">

<img width="402" alt="image" src="https://github.com/user-attachments/assets/9502efe3-2042-4077-a47d-871dcbef77a2">

<img width="878" alt="image" src="https://github.com/user-attachments/assets/f9ea819b-7e54-463f-a1cd-a0daf53f4589">



