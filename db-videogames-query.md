### Query
Dopo aver importato il dump del db contenente i dati ([[Videogiochi DB]]), eseguire le seguenti query:
#### SELECT
1. Selezionare tutte le software house americane (3)

select *
from software_houses
where country="United States"

2. Selezionare tutti i giocatori della cittÃ  di 'Rogahnland' (2)

select *
from players
where city='Rogahnland'

3. Selezionare tutti i giocatori il cui nome finisce per "a" (220)

select *
from players
where name like '%a'

4. Selezionare tutte le recensioni scritte dal giocatore con ID = 800 (11)

select *
from reviews
where player_id like '800'

5. Contare quanti tornei ci sono stati nell'anno 2015 (9)

select *
from tournaments
where year='2015'

6. Selezionare tutti i premi che contengono nella descrizione la parola 'facere' (2)

select *
from awards
where description like '%facere%'

7. Selezionare tutti i videogame che hanno la categoria 2 (FPS) o 6 (RPG), mostrandoli una sola volta (del videogioco vogliamo solo l'ID) (287)

select DISTINCT name
from videogames
inner join category_videogame
ON ( videogames.id = category_videogame.videogame_id )
where category_videogame.category_id='2' or category_videogame.category_id='6'

8. Selezionare tutte le recensioni con voto compreso tra 2 e 4 (2947)

select *
from reviews
where rating>=2 && rating<=4

9. Selezionare tutti i dati dei videogiochi rilasciati nell'anno 2020 (46)

select *
from videogames
where YEAR(release_date)='2020'

10. Selezionare gli id dei videogame che hanno ricevuto almeno una recensione da stelle, mostrandoli una sola volta (443)

select distinct videogames.id
from videogames
join reviews
on videogames.id=reviews.videogame_id
where rating=5

##### **BONUS**
11. Selezionare il numero e la media delle recensioni per il videogioco con ID = 412 (review number = 12, avg_rating = 3.16 circa)

select count(*), avg(reviews.rating)
from videogames
join reviews
on videogames.id=reviews.videogame_id
where videogames.id=412

12. Selezionare il numero di videogame che la software house con ID = 1 ha rilasciato nel 2018 (13)

select count(*)
from software_houses
join videogames
on (videogames.software_house_id=software_houses.id)
where videogames.software_house_id=1 && year(videogames.release_date)=2018

---

#### GROUP BY
1. Contare quante software house ci sono per ogni paese (3)

select country, count(country)
from software_houses
group by country

2. Contare quante recensioni ha ricevuto ogni videogioco (del videogioco vogliamo solo l'ID) (500)

select videogames.id, videogames.name, count(videogames.name) as "reviews_number"
from videogames
join reviews
on reviews.videogame_id=videogames.id
group by videogames.id

3. Contare quanti videogiochi hanno ciascuna classificazione PEGI (della classificazione PEGI vogliamo solo l'ID) (13)

select pegi_labels.id,pegi_labels.name, count(videogames.id) as "pegi_videogames_number"
from videogames
	join pegi_label_videogame
		on pegi_label_videogame.videogame_id=videogames.id
	join pegi_labels
		on pegi_label_videogame.pegi_label_id=pegi_labels.id
group by pegi_labels.id

4. Mostrare il numero di videogiochi rilasciati ogni anno (11)

select year(release_date)as 'Year', count(id)
from videogames
group by year(release_date)

5. Contare quanti videogiochi sono disponbiili per ciascun device (del device vogliamo solo l'ID) (7)

select devices.id, count(videogames.id)
from devices
join device_videogame
	on device_videogame.device_id=devices.id
join videogames
	on device_videogame.videogame_id=videogames.id
group by devices.id

6. Ordinare i videogame in base alla media delle recensioni (del videogioco vogliamo solo l'ID) (500)

select  videogames.id, avg(reviews.rating)
from videogames
join reviews
	on reviews.videogame_id=videogames.id
group by videogames.id
order by avg(reviews.rating) desc

---

#### JOIN
1. Selezionare i dati di tutti giocatori che hanno scritto almeno una recensione, mostrandoli una sola volta (996)

select  distinct players.id
from players
join reviews
	on reviews.player_id=players.id

2. Sezionare tutti i videogame dei tornei tenuti nel 2016, mostrandoli una sola volta (226)

select  distinct videogames.id
from videogames
join tournament_videogame
	on tournament_videogame.videogame_id=videogames.id
join tournaments
	on tournament_videogame.tournament_id=tournaments.id
where tournaments.year=2016

3. Mostrare le categorie di ogni videogioco (1718)

select videogames.name, categories.name 
from videogames
join category_videogame
	on category_videogame.videogame_id=videogames.id
join categories
	on category_videogame.category_id=categories.id
group by videogames.id, categories.name

4. Selezionare i dati di tutte le software house che hanno rilasciato almeno un gioco dopo il 2020, mostrandoli una sola volta (6)

select distinct software_houses.*
from software_houses
join videogames
	on videogames.software_house_id=software_houses.id
where year(videogames.release_date)=2020

5. Selezionare i premi ricevuti da ogni software house per i videogiochi che ha prodotto (55)

select distinct software_houses.id, videogames.name, awards.name
from software_houses
join videogames
	on videogames.software_house_id=software_houses.id
join award_videogame
	on award_videogame.videogame_id=videogames.id
join awards
	on award_videogame.award_id=awards.id

6. Selezionare categorie e classificazioni PEGI dei videogiochi che hanno ricevuto recensioni da 4 e 5 stelle, mostrandole una sola volta (3363)

--------------------------------------------------------------------------
select *
from pegi_labels pl 
join pegi_label_videogame plv ON plv.pegi_label_id =pl.id 
JOIN videogames v on plv.videogame_id = v.id 
JOIN reviews r on r.videogame_id =v.id 
JOIN category_videogame cv on cv.videogame_id =v.id 
JOIN categories c on c.id =cv.category_id 
WHERE r.rating >=4 && r.rating <=5

7. Selezionare quali giochi erano presenti nei tornei nei quali hanno partecipato i giocatori il cui nome inizia per 'S' (474)

select DISTINCT v.id  
FROM videogames v 
join tournament_videogame tv on tv.videogame_id =v.id 
JOIN tournaments t on t.id =tv.tournament_id 
JOIN player_tournament pt on pt.tournament_id =t.id 
JOIN players p on p.id = pt.player_id 
WHERE p.name LIKE 's%'

8. Selezionare le cittÃ  in cui Ã¨ stato giocato il gioco dell'anno del 2018 (36)

select p.city 
FROM players p
join reviews r on r.player_id =p.id 
join videogames v on v.id =r.videogame_id 
join award_videogame av on av.videogame_id =v.id 
JOIN awards a on a.id =av.award_id 
WHERE a.name = "gioco dell'anno" && av.year = '2018'


9. Selezionare i giocatori che hanno giocato al gioco piÃ¹ atteso del 2018 in un torneo del 2019 (3306)

select p.name 
FROM players p 
join player_tournament pt on pt.player_id =p.id 
join tournaments t on t.id =pt.tournament_id 
join tournament_videogame tv on tv.tournament_id =t.id 
JOIN videogames v on v.id =tv.videogame_id 
join award_videogame av on av.videogame_id = v.id 
JOIN awards a on a.id =av.award_id 
WHERE a.name ="gioco più atteso" && av.year =2018 && t.`year` =2019



##### **BONUS**
10. Selezionare i dati della prima software house che ha rilasciato un gioco, assieme ai dati del gioco stesso (software house id : 5)

select *
from software_houses sh 
join videogames v on v.software_house_id = sh.id  
ORDER BY v.release_date
LIMIT 1

11. Selezionare i dati del videogame (id, name, release_date, totale recensioni) con piÃ¹ recensioni (videogame id : potrebbe uscire 449 o 398, sono entrambi a 20)
12. Selezionare la software house che ha vinto piÃ¹ premi tra il 2015 e il 2016 (software house id : potrebbe uscire 3 o 1, sono entrambi a 3)
13. Selezionare le categorie dei videogame i quali hanno una media recensioni inferiore a 1.5 (10)
