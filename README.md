Track Project 
Author: Avery Mariol
Professor Lehman 
Course CS415 - Database systems
Project Summary- 
  This project designs a relational database for a Track and field coaching staff to manage athletes, competitive events and performance results. The database provides a structured way to
  track athlete profiles, record performances, organize teams and analyze meet results.​
data base use case - 
  From a user's perspective, coaches are able to look up an athlete, view their season best times or distance, compare performance across events, and generate statistical 
  reports to assist withtraining and competition decisions. ​
  
ER diagram 
Mermaid Code: direction TB 

    Teams { 
        INT team_id PK ""   
        VARCHAR(100) team_name  ""   
        VARCHAR(255) description  ""   
        VARCHAR(100) home_venue  ""  
    } 

    Coaches { 
        INT team_id PK ""   
        VARCHAR(120) full_name  ""   
        VARCHAR(80) role  ""   
        INT team_id FK ""   
    } 

    Athletes { 
        INT athlete_id PK ""   
        VARCHAR(120) full_name  ""   
        ENUM_MF gender  ""   
        DATE birth_date  ""   
        INT team_id FK ""   
        SMALLINT height_cm  ""   
        SMALLINT weight_kg  ""   
    } 

    Events { 
        INT event_id PK ""   
        VARCHAR(100) event_name  ""   
        ENUM_Track_Field event_type  ""   
        VARCHAR(20) unit  ""   
        VARCHAR(50) discipline  ""   

    } 
    Results { 
        INT event_id PK ""   
        INT athlete_id FK ""   
        INT event_id FK ""   
        DATE meet_date  ""   
        VARCHAR(120) venue  ""   
        DECIMAL62 performance  ""   
        TINYINT place  ""   
        DECIMAL31 wind  ""   
        YEAR season  ""   
    } 
    
    Teams ||--o{Coaches : "has"
    Teams ||--o{Athletes : "has" 

    Athletes ||--o{ Results : "records" 
    Events ||--|{Results : "appears in" 

Design explanation - 
 why these entites were chosen , design decisions and normalization choices 
table description -
  Athletes, stores all registered athletes including attributes like name, team and gender. Core entityy that is used to link performances and teams.
Create tables- 
Insert Data-
SQL queries- 
SELECT athlete_id, first_name, last_name, gender, year, team_id​
    -> FROM athlete​
    -> ORDER BY year, last_name;​

SELECT athlete_id, CONCAT(first_name, ' ', last_name)AS
full_name FROM athlete;​

SELECT​
    ->     first_name,​
    ->     last_name,​
    ->     LEFT(first_name, 1) AS first_initial​
    -> FROM athlete;​

SELECT team_id, COUNT(*) AS athlete_count FROM athlete
GROUP BY team_id HAVING COUNT(*) > 10;​

SELECT a.first_name, e.event_name, r.performance FROM result r​
    -> JOIN athlete a ON r.athlete_id = a.athlete_id​
    -> JOIN event e ON r.event_id = e.event_id​
    -> JOIN team t ON a.team_id = t.team_id;​

SELECT t.team_name, a.first_name AS athlete FROM team t LEFT
JOIN athlete a ON t.team_id = a.team_id;​

UPDATE athlete SET year = 'SR' WHERE athlete_id = 10;​

DELETE FROM result WHERE result_id = 200;​

CREATE VIEW upperclassman AS SELECT athlete_id, first_name,
last_name, year FROM athlete WHERE year IN ('JR', 'SR');​

START TRANSACTION;​

UPDATE athlete SET year = 'SR' WHERE athlete_id = 10;​

ROLLBACK;​
 

 
