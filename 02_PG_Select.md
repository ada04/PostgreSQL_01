## Урок 2 ##
### Структура опреатора SELECT ###


Выбор столбцов из одной таблицы

''' select airport_code  from airports;

``` select airport_code, airport_name, city  from airports;

``` select * from airports;

-- Исключение дублирования данных
select aircraft_code from flights f;
select distinct aircraft_code from flights f;

select passenger_name  from tickets t ;
select distinct passenger_name  from tickets t ;

-- Условия выбора
select * from bookings b ;

select * from bookings b 
where total_amount > 1200000;

select flight_no, departure_airport, scheduled_departure 
from flights f 
where scheduled_departure = '2017-08-24 09:30';

-- Условия выбора 2
select flight_no, departure_airport, aircraft_code  
from flights f 
where departure_airport = 'SVO' and aircraft_code = '773';

-- + OR

-- Выражение LIKE
select * from airports a 
where airport_code like 'M%';

select * from airports a 
where airport_code like '_M%';

select flight_no, departure_airport, aircraft_code  
from flights f 
where flight_no like 'P____6'; --6 char

-- Выражение BETWEEN
select * from bookings b 
where total_amount between 35200 and 35300;

select flight_no, departure_airport, scheduled_departure 
from flights f 
where scheduled_departure BETWEEN '2017-08-24' and '2017-08-25';

select city  from airports a where city between 'А' and 'В';

-- Выражение IN
select airport_code, city  
from airports a 
where airport_code in ('SVO', 'DME', 'VKO');

select flight_no, departure_airport, scheduled_departure 
from flights f 
where scheduled_departure IN ('2017-08-24 09:30',  '2017-08-24 10:30');

select airport_code, city  
from airports a 
where airport_code not in ('SVO', 'DME', 'VKO');

-- IN 2
select flight_no, departure_airport, scheduled_departure 
from flights f 
where departure_airport in ('SVO', 'DME', 'VKO')
  and flight_no like '%2';

select flight_no, departure_airport, scheduled_departure, actual_departure  
from flights f 
where actual_departure  in ('2017-02-10 12:30', '2017-02-10 13:30', '2017-02-10 14:30', NULL);

select flight_no, departure_airport, scheduled_departure, actual_departure  
from flights f 
where actual_departure  not in ('2017-02-10 12:30', '2017-02-10 13:30', '2017-02-10 14:30', NULL);

select book_ref from bookings b 
where total_amount > 1200000;

select * from tickets t 
where book_ref in 
 (select book_ref from bookings b 
  where total_amount > 1200000);
  
-- выражение IS NULL
select flight_no, departure_airport, status, actual_departure  
from flights f 
where actual_departure is null;

select flight_no, departure_airport, scheduled_departure, actual_departure  
from flights f 
where actual_departure  in ('2017-02-10 12:30', '2017-02-10 13:30', '2017-02-10 14:30')
  or actual_departure is NULL;

select flight_no, departure_airport, scheduled_departure, actual_departure  
from flights f 
where (actual_departure  in ('2017-02-10 12:30', '2017-02-10 13:30', '2017-02-10 14:30')
  or actual_departure is null)
  and scheduled_departure < '2017-08-16';
  
-- вычисляемые столбцы
select flight_no, departure_airport, arrival_airport, actual_departure - scheduled_departure as delay   
from flights f 
where actual_departure is not null
 and actual_departure - scheduled_departure > '04:30:00';

-- Конкатенация столбцов
-- Вывести информацию о самолётах:
-- Воздушное судно <модель> с кодом <код> и дальностью полёта <расстояние> км.
select 'Воздушное судно '||model||' с кодом '||aircraft_code ||' и дальностью полёта '||range||' км.' as txt
from aircrafts a ;

select CONCAT('Воздушное судно ',model,' с кодом ',aircraft_code,' и дальностью полёта ',range,' км.') as txt
from aircrafts a ;

--Вывести названия аэропортов, которые содержат название города
select airport_name, city  
from airports a 
where airport_name like '%'||city||'%';
 
-- Выражение CASE с параметром

-- Первому зарегистрировашемуся на рейс 43915 
-- выдать приз "Бесплатный ланч", второму "Фирменный блокнот",
-- третьему "Фирменную ручку", а остальным открытку
select ticket_no, boarding_no, 
  case boarding_no 
    when 1 then 'Бесплатный ланч'
    when 2 then 'Фирменный блокнот'
    when 3 then 'Фирменная ручка'
    else 'Открытка'
   end as gift
from boarding_passes bp 
where flight_id =43915;

-- Купившим билет на рейс 36094 
-- в класс Business начислить 20% кешбек, Economy не начислять
select ticket_no, fare_conditions,  
  case fare_conditions  
    when 'Business' then amount * 0.2
   end as Bonus
from ticket_flights tf  
where flight_id =36094;


-- Выражение CASE с условием

-- Первым 3 зарегистрировашемся на рейс 43915 
-- выдать приз "Бесплатный ланч", 4-5 "Фирменный блокнот",
-- а остальным открытку
select ticket_no, boarding_no, 
  case  
    when boarding_no <= 3 then 'Бесплатный ланч'
    when boarding_no <= 5 then 'Фирменный блокнот'
    else 'Открытка'
   end as gift
from boarding_passes bp 
where flight_id =43915;


-- Купившим билет на рейс 36094 в класс Business начислить 
-- 20% кешбек,если номер заканчивается на 1
-- 15% кешбек,если номер заканчивается на 2
-- 10% кешбек,если номер заканчивается на 3
-- 5% кешбек,остальным
-- купившим в эконом, 20% тем, у кого номер билета на 1
select ticket_no, fare_conditions,  
  case fare_conditions  
    when 'Business' then 
       case 
	      when ticket_no like '%1' then amount * 0.2
	      when ticket_no like '%2' then amount * 0.15
	      when ticket_no like '%3' then amount * 0.1
	      else amount * 0.05
       end
    when 'Economy' then 
       case 
	      when ticket_no like '%1' then amount * 0.2
	   end   
   end as Bonus
from ticket_flights tf  
where flight_id =36094;


-- Сотрировка
select * from airports;
select * from airports order by 3;
select * from airports order by city desc;
select * from airports order by timezone desc, city asc;

-- LIMIT, OFFSET
select ticket_no, boarding_no
from boarding_passes bp 
where flight_id =43915
--order by 2
limit 5;

select ticket_no, boarding_no
from boarding_passes bp 
where flight_id =43915
--order by 2
limit 2
offset 3;


------- ПРАКТИКА ---------
-- Вывести для ВС Airbus A320, A321 
-- для мест 1A, 1F надпись "Ваше место возле окна"
-- для мест 1B, 1E надпись "Ваше место посередине"
-- для мест 1С, 1D надпись "Ваше место возле прохода"

-- найдём нужный борт
select * from aircrafts a ; 
-- теперь нам нужен любой рейс, который обслуживает этот тип самолёта
select flight_id  from flights f where aircraft_code = '321'
limit 1; -- in ('320','321')

select * from boarding_passes bp 
where seat_no like '1_'
 and flight_id = 3940; --нет ни одной записи
 
select * from boarding_passes bp 
where seat_no like '1_'
 and flight_id in (select flight_id  from flights f where aircraft_code = '321' limit 100) ; 

select *, 
  case 
  	when seat_no in ('1A','1F') then 'Ваше место возле окна'
  	when seat_no in ('1B','1E') then 'Ваше место посередине'
  	when seat_no in ('1C','1D') then 'Ваше место возле прохода'
  end as win	
from boarding_passes bp 
where seat_no like '1_'
 and flight_id in (select flight_id  from flights f where aircraft_code = '321' limit 100) ; 


 select * from flights f;

select * from seats;

select * from tickets t ;

select * from bookings b ;

select * from aircrafts a ;

