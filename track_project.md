
Track Project 
Author: Avery Mariol
Professor Lehman 
Course CS415 - Database systems
Project Overview- 
  This project designs a relational database for a Track and field coaching staff to manage athletes, competitive events and performance results. The database provides a structured way to track athlete profiles, record performances, organize teams and analyze meet results.​

Users View - 
  From a user's perspective, coaches are able to look up an athlete, view their season best times or distance, compare performance across events, and generate statistical 
  reports to assist withtraining and competition decisions. ​
  
Database ER Model
<img width="2229" height="2264" alt="Track_Project_ERdiagram" src="https://github.com/user-attachments/assets/3fb85bf8-9baa-4180-b8da-ca775d1c6cf2" />
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

Design explanation - The database includes five core tables with normalized relations, Teams, Coaches, Athletes, Events and results. Each table stores a category of information to avoid redundancy and have consistent data. Athlete and event information are separated from
performance data which prevents repeated storage of athlete names or event properties in the results table.​

table description -
  Athletes, stores all registered athletes including attributes like name, team and gender. Core entityy that is used to link performances and teams.

  
Create tables- 
The following SQL creates each table in the track project

```
sql
---------------
--coach table
---------------
CREATE TABLE coach (
    coach_id INT NOT NULL AUTO_INCREMENT,
    coach_name VARCHAR(100) NOT NULL,
    team_id INT NOT NULL,
    PRIMARY KEY (coach_id),
    CONSTRAINT fk_coach_team
        FOREIGN KEY (team_id) REFERENCES team(team_id)
);

---------------
--team table
---------------
CREATE TABLE team (
    team_id INT NOT NULL AUTO_INCREMENT,
    team_name VARCHAR(100) NOT NULL,
    conference VARCHAR(100),
    PRIMARY KEY (team_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

---------------
--athlete table
---------------
CREATE TABLE athlete (
    athlete_id INT NOT NULL AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    gender ENUM('M','F') NOT NULL,
    year ENUM('FR','SO','JR','SR') NOT NULL,
    team_id INT NOT NULL,
    height_cm INT,
    weight_kg INT,
    PRIMARY KEY (athlete_id),
    KEY team_id (team_id),
    CONSTRAINT athlete_ibfk_1
        FOREIGN KEY (team_id) REFERENCES team(team_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

---------------
--event table
---------------
CREATE TABLE event (
    event_id INT NOT NULL AUTO_INCREMENT,
    event_name VARCHAR(100) NOT NULL,
    event_type ENUM('Track','Field') NOT NULL,
    PRIMARY KEY (event_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

---------------
--meet table
---------------
CREATE TABLE meet (
    meet_id INT NOT NULL AUTO_INCREMENT,
    meet_name VARCHAR(150),
    meet_date DATE,
    PRIMARY KEY (meet_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

---------------
--result table
---------------
CREATE TABLE result (
    result_id INT NOT NULL AUTO_INCREMENT,
    athlete_id INT NOT NULL,
    event_id INT NOT NULL,
    meet_id INT NOT NULL,
    performance VARCHAR(20) NOT NULL,
    place INT,
    PRIMARY KEY (result_id),
    KEY athlete_id (athlete_id),
    KEY event_id (event_id),
    KEY meet_id (meet_id),
    CONSTRAINT result_ibfk_1
        FOREIGN KEY (athlete_id) REFERENCES athlete(athlete_id),
    CONSTRAINT result_ibfk_2
        FOREIGN KEY (event_id) REFERENCES event(event_id),
    CONSTRAINT result_ibfk_3
        FOREIGN KEY (meet_id) REFERENCES meet(meet_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

Insert Data-
The following SQL inserts sample data for each table in the track project

-----
--Athlete sample data (15 rows)
-----
```sql
INSERT INTO athlete (athlete_id, first_name, last_name, gender, year, team_id, height_cm, weight_lbs) VALUES
(1, 'Jordan', 'Smith', 'M', 'JR', 1, 180, 165),
(2, 'Avery', 'Coleman', 'F', 'SO', 1, 165, 130),
(10, 'Michael', 'Reed', 'M', 'SR', 1, 183, 175),
(11, 'Samantha', 'Lopez', 'F', 'FR', 2, 160, 118),
(12, 'Daniel', 'Nguyen', 'M', 'SO', 3, 175, 150),
(13, 'Emily', 'Turner', 'F', 'JR', 2, 168, 135),
(14, 'Brandon', 'Hall', 'M', 'FR', 3, 178, 160),
(15, 'Chloe', 'Adams', 'F', 'SR', 1, 162, 120),
(16, 'Isaac', 'Mitchell', 'M', 'SO', 2, 182, 170),
(17, 'Lauren', 'Barker', 'F', 'JR', 1, 170, 140),
(18, 'Ethan', 'Wright', 'M', 'FR', 3, 176, 155),
(19, 'Natalie', 'Perez', 'F', 'SO', 2, 164, 128),
(20, 'Zachary', 'Cole', 'M', 'JR', 1, 185, 180),
(21, 'Sofia', 'Rivera', 'F', 'SR', 3, 159, 115),
(22, 'Connor', 'James', 'M', 'SO', 2, 181, 168);
```
-----
--Team sample data (8 rows)
-----
```sql
INSERT INTO team (team_id, team_name, conference) VALUES
(1, 'Falcons Track & Field', 'MAC'),
(2, 'Falcons', 'North Division'),
(3, 'Tigers', 'South Division'),
(4, 'Eagles', 'East Division'),
(5, 'Falcons Track Club', 'North Division'),
(6, 'Riverside Athletics', 'East Division'),
(7, 'Summit Striders', 'West Division'),
(8, 'Valley Speedsters', 'South Division');
```
-----
--Event sample data(10 rows)
-----
```sql
INSERT INTO event (event_id, event_name, event_type) VALUES
(1, '100m Dash', 'Track'),
(2, 'Long Jump', 'Field'),
(3, '100m Dash', 'Track'),
(4, '200m Dash', 'Track'),
(5, '400m Dash', 'Track'),
(6, '800m Run', 'Track'),
(7, 'Long Jump', 'Field'),
(8, 'High Jump', 'Field'),
(9, 'Shot Put', 'Field'),
(10, 'Javelin Throw', 'Field');
```
-----
--Result sample data(15 rows)
-----
```sql
INSERT INTO result (result_id, athlete_id, event_id, meet_id, performance, place) VALUES
(1, 1, 1, 1, '11.42', 1),
(2, 2, 1, 1, '12.30', 2),
(3, 10, 2, 1, '23.90', 1),
(4, 11, 4, 1, '2:42.10', 3),
(5, 12, 3, 1, '54.55', 2),
(6, 13, 7, 1, '15-02', 1),
(7, 14, 6, 1, '38-06', 2),
(8, 15, 1, 2, '12.10', 3),
(9, 16, 4, 2, '2:20.50', 2),
(10, 17, 5, 2, '5:35.20', 3),
(11, 18, 3, 2, '56.10', 4),
(12, 19, 2, 3, '25.80', 2),
(13, 20, 1, 3, '11.10', 1),
(14, 21, 7, 3, '16-00', 2),
(15, 22, 4, 3, '2:18.80', 1);
```
-----
Coaches sample data(8 rows)
-----
```sql
INSERT INTO coach (coach_name, team_id) VALUES
('Coach Anderson', 1),
('Coach Bennett', 2),
('Coach Carter', 3),
('Coach Daniels', 4),
('Coach Ellis', 5),
('Coach Foster', 6),
('Coach Grant', 7),
('Coach Hayes', 8);
```


SQL queries- 
```sql
SELECT athlete_id, first_name, last_name, gender, year, team_id
FROM athlete
ORDER BY year, last_name;
```
Sample Output
```
+------------+------------+-----------+--------+------+---------+
| athlete_id | first_name | last_name | gender | year | team_id |
+------------+------------+-----------+--------+------+---------+
|         14 | Brandon    | Hall      | M      | FR   |       3 |
|         11 | Samantha   | Lopez     | F      | FR   |       2 |
|         18 | Ethan      | Wright    | M      | FR   |       3 |
|          2 | Avery      | Coleman   | F      | SO   |       1 |
|         22 | Connor     | James     | M      | SO   |       2 |
|         16 | Isaac      | Mitchell  | M      | SO   |       2 |
|         12 | Daniel     | Nguyen    | M      | SO   |       3 |
|         19 | Natalie    | Perez     | F      | SO   |       2 |
|         17 | Lauren     | Barker    | F      | JR   |       1 |
|         20 | Zachary    | Cole      | M      | JR   |       1 |
|          1 | Jordan     | Smith     | M      | JR   |       1 |
|         13 | Emily      | Turner    | F      | JR   |       2 |
|         15 | Chloe      | Adams     | F      | SR   |       1 |
|         10 | Michael    | Reed      | M      | SR   |       1 |
|         21 | Sofia      | Rivera    | F      | SR   |       3 |
+------------+------------+-----------+--------+------+---------+
15 rows in set (0.015 sec)
```
```sql
SELECT athlete_id, CONCAT(first_name, ' ', last_name)AS
full_name FROM athlete;​
```
Sample Output
```
+------------+----------------+
| athlete_id | full_name      |
+------------+----------------+
|          1 | Jordan Smith   |
|          2 | Avery Coleman  |
|         10 | Michael Reed   |
|         11 | Samantha Lopez |
|         12 | Daniel Nguyen  |
|         13 | Emily Turner   |
|         14 | Brandon Hall   |
|         15 | Chloe Adams    |
|         16 | Isaac Mitchell |
|         17 | Lauren Barker  |
|         18 | Ethan Wright   |
|         19 | Natalie Perez  |
|         20 | Zachary Cole   |
|         21 | Sofia Rivera   |
|         22 | Connor James   |
+------------+----------------+
15 rows in set (0.006 sec)
```
```sql
SELECT
    first_name,
    last_name,
    LEFT(first_name, 1) AS first_initial
FROM athlete;
```
Sample Output
```
+------------+-----------+---------------+
| first_name | last_name | first_initial |
+------------+-----------+---------------+
| Jordan     | Smith     | J             |
| Avery      | Coleman   | A             |
| Michael    | Reed      | M             |
| Samantha   | Lopez     | S             |
| Daniel     | Nguyen    | D             |
| Emily      | Turner    | E             |
| Brandon    | Hall      | B             |
| Chloe      | Adams     | C             |
| Isaac      | Mitchell  | I             |
| Lauren     | Barker    | L             |
| Ethan      | Wright    | E             |
| Natalie    | Perez     | N             |
| Zachary    | Cole      | Z             |
| Sofia      | Rivera    | S             |
| Connor     | James     | C             |
+------------+-----------+---------------+
15 rows in set (0.011 sec)
```
```sql
 SELECT team_id, COUNT(*) AS athlete_count
    -> FROM athlete
    -> GROUP BY team_id
    -> HAVING COUNT(*) > 5;
```
Sample Output
```
+---------+---------------+
| team_id | athlete_count |
+---------+---------------+
|       1 |             6 |
+---------+---------------+
1 row in set (0.002 sec)
```
```sql
SELECT 
    a.first_name, 
    e.event_name, 
    r.performance
FROM result r
JOIN athlete a ON r.athlete_id = a.athlete_id
JOIN event e ON r.event_id = e.event_id
JOIN team t ON a.team_id = t.team_id;
```
Sample Output
```
+------------+------------+-------------+
| first_name | event_name | performance |
+------------+------------+-------------+
| Jordan     | 100m Dash  | 11.42       |
| Avery      | 100m Dash  | 12.30       |
| Chloe      | 100m Dash  | 12.10       |
| Zachary    | 100m Dash  | 11.10       |
| Michael    | Long Jump  | 23.90       |
| Natalie    | Long Jump  | 25.80       |
| Daniel     | 100m Dash  | 54.55       |
| Ethan      | 100m Dash  | 56.10       |
| Samantha   | 200m Dash  | 2:42.10     |
| Isaac      | 200m Dash  | 2:20.50     |
| Connor     | 200m Dash  | 2:18.80     |
| Lauren     | 400m Dash  | 5:35.20     |
| Brandon    | 800m Run   | 38-06       |
| Emily      | Long Jump  | 15-02       |
| Sofia      | Long Jump  | 16-00       |
+------------+------------+-------------+
15 rows in set (0.014 sec)
```
```sql
SELECT t.team_name, a.first_name AS athlete FROM team t LEFT
JOIN athlete a ON t.team_id = a.team_id;​
```
Sample Output
```
+-----------------------+----------+
| team_name             | athlete  |
+-----------------------+----------+
| Falcons Track & Field | Jordan   |
| Falcons Track & Field | Avery    |
| Falcons Track & Field | Michael  |
| Falcons Track & Field | Chloe    |
| Falcons Track & Field | Lauren   |
| Falcons Track & Field | Zachary  |
| Falcons               | Samantha |
| Falcons               | Emily    |
| Falcons               | Isaac    |
| Falcons               | Natalie  |
| Falcons               | Connor   |
| Tigers                | Daniel   |
| Tigers                | Brandon  |
| Tigers                | Ethan    |
| Tigers                | Sofia    |
| Eagles                | NULL     |
| Falcons Track Club    | NULL     |
| Riverside Athletics   | NULL     |
| Summit Striders       | NULL     |
| Valley Speedsters     | NULL     |
+-----------------------+----------+
20 rows in set (0.015 sec)
```
```sql
UPDATE athlete
SET year = 'SR'
WHERE athlete_id = 10;
```
```sql
DELETE FROM result WHERE result_id = 200;​
```
```sql
CREATE VIEW upperclassman AS SELECT athlete_id, first_name,
last_name, year FROM athlete WHERE year IN ('JR', 'SR');​
```
Sample Output
```
+------------+------------+-----------+------+
| athlete_id | first_name | last_name | year |
+------------+------------+-----------+------+
|          1 | Jordan     | Smith     | JR   |
|         10 | Michael    | Reed      | SR   |
|         13 | Emily      | Turner    | JR   |
|         15 | Chloe      | Adams     | SR   |
|         17 | Lauren     | Barker    | JR   |
|         20 | Zachary    | Cole      | JR   |
|         21 | Sofia      | Rivera    | SR   |
+------------+------------+-----------+------+
7 rows in set (0.025 sec)
```
```sql
START TRANSACTION;​
```
```sql
UPDATE athlete SET year = 'SR' WHERE athlete_id = 10;​
```
```sql
ROLLBACK;​
```

Reports - <img width="682" height="642" alt="Screenshot 2025-12-09 104350" src="https://github.com/user-attachments/assets/a08ecc96-65a4-465c-aaeb-0e5cd859e22d" />

This chart report was created using power BI which imported directly from the MariaDB tables used in my project. Power BI automatically
recognized the categories and allowed me to build this chart comparing the number of male and female athletes for each academic
year. The chart aggregates athlete records using a “Count of athlete_id” grouped by both year and gender. This report is valuable
for coaches because it provide a visual breakdown of roster composition for class years as well as gender distribution on a team to
help athletic directors maintain balanced teams and future planning.





 
