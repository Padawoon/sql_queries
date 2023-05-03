# sql_queries

#### NESTED (SUBQUERIES):
###### IN:
```sql
SELECT *
FROM flight f
WHERE f.flight_id IN (SELECT p.flight_flight_id
                      FROM passenger p
                      WHERE p.first_name LIKE 'A%')
```
###### ANOTHER "IN" EXAMPLE:
```sql
SELECT f.flight_number
FROM flight f
WHERE f.departure_airport_airport_id IN (SELECT a.airport_id
                                         FROM airport a
                                         WHERE a.country LIKE 'U%'
                                         OR    a.country LIKE 'I%'
                                         OR    a.country LIKE 'C%')
ORDER BY f.flight_id
```

###### EXISTS:
```sql
SELECT *
FROM flight f
WHERE EXISTS (SELECT *
              FROM passenger p
              WHERE p.flight_flight_id = f.flight_id
              AND   p.first_name LIKE 'A%')
```
###### ANOTHER EXISTS EXAMPLE:
```sql
SELECT f.flight_number
FROM flight f
WHERE EXISTS (SELECT *
              FROM airport a
              WHERE a.country LIKE 'U%'
              OR    a.country LIKE 'I%'
              OR    a.country LIKE 'C%'
              AND   f.departure_airport_airport_id = a.airport_id)
ORDER BY f.flight_id
```
###### REGEXP:
```sql
SELECT f.flight_number
FROM flight f
WHERE EXISTS (SELECT *
              FROM airport a
              WHERE a.country REGEXP '^[UIC]'
              AND   f.departure_airport_airport_id = a.airport_id)
ORDER BY f.flight_id
```
###### DOUBLE NESTED SUBQUERY WITH "ALL":
```sql
SELECT *
FROM flight f2
WHERE f2.flight_charge > ALL (SELECT flight_charge
                              FROM flight f
                              WHERE f.aircraft_aircraft_id IN (SELECT a.aircraft_id FROM aircraft a WHERE a.model LIKE 'Boe%'))
```
#### JOINS:
```sql
SELECT *
FROM passenger p
  JOIN flight f ON p.flight_flight_id = f.flight_id
WHERE p.first_name LIKE 'A%'
```

```sql
SELECT f.flight_id AS ID,
       f.flight_number AS 'Flight Number',
       a.airport_name AS 'From',
       a2.airport_name AS 'To',
       CONCAT(f.departure_date,' ',f.departure_time) AS Departure,
       CONCAT(f.arrival_date,' ',f.arrival_time) AS Arrival,
       f.flight_charge AS 'Flight Charge',
       ac.model AS Aircraft,
       CONCAT(ac.number_of_seats -COUNT(p.passenger_id),'/',ac.number_of_seats) AS 'Available Seats'
FROM flight f
  JOIN airport a ON f.departure_airport_airport_id = a.airport_id
  JOIN airport a2 ON f.destination_airport_airport_id = a2.airport_id
  JOIN aircraft ac ON f.aircraft_aircraft_id = ac.aircraft_id
  LEFT JOIN passenger p ON f.flight_id = p.flight_flight_id
GROUP BY f.flight_id
```

```sql
SELECT a.airport_name AS "Departure Airport",
       CONCAT(f.departure_date,' ',f.departure_time) AS Departure,
       a2.airport_name AS "Arrival Airport",
       CONCAT(f.arrival_date,' ',f.arrival_time) AS Arrival
FROM flight f
  JOIN airport a ON f.departure_airport_airport_id = a.airport_id
  JOIN airport a2 ON f.destination_airport_airport_id = a2.airport_id
```
