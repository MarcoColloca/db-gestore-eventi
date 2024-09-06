# KATA - Query DB: Gestore Eventi


---

1. Selezionare tutti gli eventi gratis, cioè con prezzo nullo (19): <br>

        SELECT * 
        FROM `events`
        WHERE `events`.`price` IS NULL;

---    
2. Selezionare tutte le location in ordine alfabetico (82): <br>

        SELECT * 
        FROM `locations` 
        ORDER BY `name` ASC;
        

---
3. Selezionare tutti gli eventi che costano meno di 20 euro e durano meno di 3 ore (38): <br>

        SELECT * 
        FROM `events`
        WHERE `price` < 20
        AND `duration` < '03:00:00';       
        
---
4. Selezionare tutti gli eventi di dicembre 2023 (25): <br>

        SELECT * 
        FROM `events`
        WHERE `start` >= DATE("2023-12-01") 
        AND `start` < DATE("2024-01-01");

        --------------------------------------

        SELECT * 
        FROM `events`
        WHERE YEAR(`start`) = 2023
        AND MONTH(`start`) = 12;

         
        
---
5. Selezionare tutti gli eventi con una durata maggiore alle 2 ore (823): <br>
 <strong>Il risultato è però 946. </strong>

        SELECT * 
        FROM `events`
        WHERE `duration` > '02:00:00';       
        
    <strong> Query che dà risultato 823. </strong>  

        SELECT * 
        FROM `events`
        WHERE `duration` >= '03:00:00'; 
        
---
6. Selezionare tutti gli eventi, mostrando nome, data di inizio, ora di inizio, ora di fine e durata totale (1040): <br>                         

        SELECT 
        `events`.`name`, 
        DATE(`events`.`start`) AS 'starting_date',  
        TIME(`events`.`start`) AS 'starting_time',
        `events`.`duration` AS 'total_duration',
        TIME(ADDTIME(`events`.`start`, `events`.`duration`)) AS 'ending_time'
        FROM `events`;


---
7. Selezionare tutti gli eventi aggiunti da “Fabiano Lombardo” (id: 1202) (132): <br>

        SELECT *
        FROM `events`
        WHERE `user_id` = 1202;


---
8. Selezionare il numero totale di eventi per ogni fascia di prezzo (81): <br>

        SELECT `events`.`price`, COUNT(`events`.`id`) AS 'events_by_prices' 
        FROM `events` 
        GROUP BY `price`;

---
9. Selezionare tutti gli utenti admin ed editor (9): <br>

        SELECT `users`.* , `roles`.`name` AS 'role'
        FROM `users` 
        INNER JOIN `roles`
            ON `users`.`role_id` = `roles`.`id`
        WHERE `roles`.`name` = 'Admin' OR `roles`.`name` = 'Editor';

---
10. Selezionare tutti i concerti (eventi con il tag “concerti”) (72): <br>

        SELECT `events`.*, `tags`.`name`
        FROM `events`
        INNER JOIN `event_tag`
            ON `events`.`id` = `event_tag`.`event_id`
        INNER JOIN `tags`
            ON `event_tag`.`tag_id` = `tags`.`id`
        WHERE `tags`.`name` = 'Concerti';


---
11. Selezionare tutti i tag e il prezzo medio degli eventi a loro collegati (11): <br>

        SELECT CONCAT(ROUND(AVG(`events`.price),2), ' ' , '€') AS 'average_price', `tags`.`name`
        FROM `events`
        INNER JOIN `event_tag`
        ON `events`.`id` = `event_tag`.`event_id`
        INNER JOIN `tags`
        ON `event_tag`.`tag_id` = `tags`.`id`
        GROUP BY `tags`.`name`;


---
12. Selezionare tutte le location e mostrare quanti eventi si sono tenute in ognuna di esse (82): <br>

        SELECT `locations`.*, COUNT(`events`.`location_id`) AS 'number_of_events'
        FROM `locations`
        INNER JOIN `events`
            ON `locations`.`id` = `events`.`location_id`    
        GROUP BY `events`.`location_id`;

---
13. Selezionare tutti i partecipanti per l’evento “Concerto Classico Serale” (slug: concerto-classico-serale, id: 34) (30): <br>

        SELECT `users`.*, `events`.`name` AS 'event_name'
        FROM `users`
        INNER JOIN `bookings`
            ON `bookings`.`user_id` = `users`.`id`
        INNER JOIN `events`
            ON `bookings`.`event_id` = `events`.`id`
        WHERE `bookings`.`event_id` = 34;

---
14. Selezionare tutti i partecipanti all’evento “Festival Jazz Estivo” (slug: festival-jazz-estivo, id: 2) specificando nome e cognome (13): <br>

        SELECT `users`.`first_name` AS 'name', `users`.`last_name` AS 'surname', `events`.`name` AS 'event_name'
        FROM `users`
        INNER JOIN `bookings`
            ON `bookings`.`user_id` = `users`.`id`
        INNER JOIN `events`
            ON `bookings`.`event_id` = `events`.`id`
        WHERE `bookings`.`event_id` = 2;


---
15. Selezionare tutti gli eventi sold out (dove il totale delle prenotazioni è uguale ai biglietti totali per l’evento) (18): <br>

        SELECT `events`.*, COUNT(`bookings`.`event_id`) AS 'total_bookings'
        FROM `bookings`
        INNER JOIN `events`
            ON `bookings`.`event_id` = `events`.`id`
        GROUP BY `bookings`.`event_id`
        HAVING COUNT(`bookings`.`event_id`) = `events`.`total_tickets`;    

---
16. Selezionare tutte le location in ordine per chi ha ospitato più eventi (82): <br>

        SELECT `locations`.*, COUNT(`events`.`location_id`) AS 'number_of_events'
        FROM `locations`
        INNER JOIN `events`
            ON `locations`.`id` = `events`.`location_id`    
        GROUP BY `events`.`location_id`  
        ORDER BY `number_of_events` DESC;

---
17. Selezionare tutti gli utenti che si sono prenotati a più di 70 eventi (74): <br>

        SELECT `users`.*, COUNT(`bookings`.`user_id`) AS 'booked_events'
                FROM `users`
                INNER JOIN `bookings`
                    ON `users`.`id` = `bookings`.`user_id`    
                GROUP BY `bookings`.`user_id`  
        HAVING `number_of_events` > 70;
---
18. Selezionare tutti gli eventi, mostrando il nome dell’evento, il nome della location, il numero di prenotazioni e il totale di biglietti ancora disponibili per l’evento (1040): <br>

        SELECT `events`.`name` AS 'event_name', `locations`.`name` AS 'location_name', COUNT(`bookings`.`event_id`) AS 'number_of_bookings', `events`.`total_tickets` - COUNT(`bookings`.`event_id`) AS 'remaining_tickets' 
        FROM `locations`
        INNER JOIN `events`
            ON `locations`.`id`= `events`.`location_id`
        INNER JOIN `bookings`
            ON `events`.`id` = `bookings`.`event_id`
        GROUP BY `bookings`.`event_id`;
---