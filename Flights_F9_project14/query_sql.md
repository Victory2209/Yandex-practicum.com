# Работа с базой 
# Описание проекта 
Менеджер авиаперевозчика готовит исследование. 
Он хочет выявить связь между числом полётов, типом самолётов и календарём музыкальных фестивалей. Нужно помочь ему получить необходимые данные.

# Описание данных 

Таблица airports — информация об аэропортах:  
airport_code — трёхбуквенный код аэропорта  
airport_name — название аэропорта  
city — город  
timezone — временная зона  

Таблица aircrafts — информация о самолётах:  
aircraft_code — код модели самолёта  
model — модель самолёта  
range — дальность полёта  

Таблица tickets — информация о билетах:  
ticket_no — уникальный номер билета  
passenger_id — персональный идентификатор пассажира  
passenger_name — имя и фамилия пассажира 

Таблица flights — информация о рейсах:  
flight_id — уникальный идентификатор рейса  
departure_airport — аэропорт вылета  
departure_time — дата и время вылета  
arrival_airport — аэропорт прилёта  
arrival_time — дата и время прилёта  
aircraft_code — id самолёта  

Таблица ticket_flights — стыковая таблица «рейсы-билеты»:  
ticket_no — номер билета  
flight_id — идентификатор рейса  

Таблица festivals — информация о фестивалях:  
festival_id — уникальный номер фестиваля  
festival_date — дата проведения фестиваля  
festival_city — город проведения фестиваля  
festival_name — название фестиваля  


1. Найдите количество рейсов на каждой модели самолёта с вылетом в сентябре 2018 года. Назовите получившееся поле flights_amount и выведите его. 
Также напечатайте на экране поле model. Столбцы в таблице должны быть выведены в следующем порядке:  
model  
flights_amount  
```
SELECT
    aircrafts.model AS model,
    COUNT(flights.flight_id) AS flights_amount
FROM 
    flights
LEFT JOIN
    aircrafts ON aircrafts.aircraft_code = flights.aircraft_code
WHERE
    CAST(departure_time AS date) < '2018-10-01' AND
    CAST(departure_time AS date) > '2018-08-31'
GROUP BY
    model 
```

2. Посчитайте отправленные в сентябре рейсы самолётов следующих моделей:  
Boeing,  
Airbus,  
другие (“other”).  
Назовите получившуюся переменную flights_amount и выведите её значение на экран.  
```
SELECT
    COUNT(flights.flight_id) AS flights_amount,
   CASE WHEN model LIKE 'Airbus%' THEN 'Airbus'
        WHEN model LIKE 'Boeing%' THEN 'Boeing'
        ELSE 'other'
        END AS type_aircraft
FROM
   flights
INNER JOIN
    aircrafts ON aircrafts.aircraft_code = flights.aircraft_code
WHERE
    CAST(departure_time AS date) BETWEEN '2018-09-01' AND '2018-09-30'
GROUP BY
    type_aircraft
```

3. Посчитайте среднее количество прибывающих рейсов в день для каждого города за август 2018 года. Назовите получившееся поле average_flights, вместе с ним выведите 
столбец city. Выводите столбцы в таком порядке:  
city,  
average_flights.  
```
SELECT
    subq.city AS city,
    AVG(subq.flight_amount) AS average_flights
FROM
(SELECT
     COUNT(flights.flight_id) AS flight_amount,
     airports.city AS city,
     EXTRACT (day from CAST(flights.arrival_time AS date)) AS day
 FROM
     flights
 INNER JOIN airports
     ON airports.airport_code = flights.arrival_airport
 WHERE
     CAST(flights.arrival_time AS date) BETWEEN '2018-08-01' AND '2018-08-31'
 GROUP BY
     city,
     day) AS subq
GROUP BY 
    city;
```

4. Установите фестивали, которые проходили с 23 июля по 30 сентября 2018 года в Москве, и номер недели, в которую они проходили. 
Выведите название фестиваля festival_name и номер недели festival_week.
```
SELECT
    festival_name,
    EXTRACT(WEEK FROM CAST(festival_date AS date)) AS festival_week
FROM
    festivals
WHERE
    festival_city = 'Москва' AND festival_date BETWEEN  '2018-07-23' AND '2018-09-30'
```

5. Для каждой недели с 23 июля по 30 сентября 2018 года посчитайте билеты, которые купили на рейсы в Москву (номер недели week_number и количество билетов 
ticket_amount). Получите таблицу:  
с количеством купленных за неделю билетов;  
отметкой, проходил ли в эту неделю фестиваль;  
название фестиваля festival_name;  
номер недели week_number.  
Выводите столбцы в таком порядке: - week_number - ticket_amount - festival_week - festival_name
```
SELECT
    EXTRACT(week FROM CAST(flights.arrival_time AS date)) AS week_number,
    COUNT(ticket_flights.ticket_no) AS ticket_amount,
    sub.festival_week AS festival_week,
    sub.festival_name AS festival_name
    
FROM tickets
    LEFT JOIN ticket_flights ON ticket_flights.ticket_no  = tickets.ticket_no
    LEFT JOIN flights ON flights.flight_id  = ticket_flights.flight_id
    LEFT JOIN airports ON airports.airport_code = flights.arrival_airport
    LEFT JOIN
        (SELECT
             festival_name,
             EXTRACT(WEEK FROM CAST(festival_date AS date)) AS festival_week
         FROM
             festivals
         WHERE
             festival_city = 'Москва' AND festival_date BETWEEN '2018-07-23' AND '2018-09-30') as sub ON sub.festival_week = EXTRACT(week FROM CAST(flights.arrival_time AS date))
WHERE
    airports.city = 'Москва' AND
    CAST(flights.arrival_time AS date) BETWEEN '2018-07-23' AND '2018-09-30'
GROUP BY
    EXTRACT(week FROM CAST(flights.arrival_time AS date)), 
    festival_week, 
    festival_name
```
