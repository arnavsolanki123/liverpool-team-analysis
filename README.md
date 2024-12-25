# liverpool-team-analysis

# football Team Analysis SQL Queries

This repository contains a set of SQL queries used to analyze the performance and statistics of a specific football team, in this case, Liverpool. Below are the queries categorized by the type of analysis they perform:

## Question 1: Team Performance Metrics
```sql
SELECT t.name,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS win, 
    SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS loss, 
    SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS draw
FROM teamstats t1
JOIN teams t USING(teamid)
WHERE t1.teamid = 87
GROUP BY 1;
```
**Output:**
| Name      | Win | Loss | Draw |
|-----------|-----|------|------|
| Liverpool | 159 | 46   | 61   |

## Question 2: Season-wise Goal Comparison
```sql
SELECT t.season, SUM(t.goals), t.teamid 
FROM teamstats t
JOIN games g USING(gameid)
WHERE t.teamid = 87
GROUP BY 1;
```
**Output:**
| Season | Goals | TeamID |
|--------|-------|--------|
| 2015   | 63    | 87     |
| 2016   | 78    | 87     |
| 2014   | 52    | 87     |
| 2017   | 84    | 87     |
| 2018   | 89    | 87     |
| 2019   | 85    | 87     |
| 2020   | 68    | 87     |

## Question 3: Season Wins and Losses Comparison
```sql
SELECT t1.season,
    SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS win, 
    SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS loss, 
    SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS draw
FROM teamstats t1
WHERE t1.teamid = 87
GROUP BY 1;
```
**Output:**
| Season | Win | Loss | Draw |
|--------|-----|------|------|
| 2015   | 16  | 10   | 12   |
| 2016   | 22  | 6    | 10   |
| 2014   | 18  | 12   | 8    |
| 2017   | 21  | 5    | 12   |
| 2018   | 30  | 1    | 7    |
| 2019   | 32  | 3    | 3    |
| 2020   | 20  | 9    | 9    |

## Question 4: Results as a Home Team
```sql
SELECT 
    season, 
    SUM(CASE WHEN homeTeamID = 87 AND homeGoals > awayGoals THEN 1 ELSE 0 END) AS win,  
    SUM(CASE WHEN homeTeamID = 87 AND homeGoals < awayGoals THEN 1 ELSE 0 END) AS loss,  
    SUM(CASE WHEN homeTeamID = 87 AND homeGoals = awayGoals THEN 1 ELSE 0 END) AS draw
FROM games
WHERE homeTeamID = 87
GROUP BY 1;
```
**Output:**
| Season | Win | Loss | Draw |
|--------|-----|------|------|
| 2015   | 8   | 3    | 0    |
| 2016   | 12  | 2    | 0    |
| 2014   | 10  | 4    | 0    |
| 2017   | 12  | 0    | 0    |
| 2018   | 17  | 0    | 0    |
| 2019   | 18  | 0    | 0    |
| 2020   | 10  | 6    | 0    |

## Question 5: Results as an Away Team
```sql
SELECT 
    season, 
    SUM(CASE WHEN awayTeamID = 87 AND homeGoals > awayGoals THEN 1 ELSE 0 END) AS loss,  
    SUM(CASE WHEN awayTeamID = 87 AND homeGoals < awayGoals THEN 1 ELSE 0 END) AS win,  
    SUM(CASE WHEN awayTeamID = 87 AND homeGoals = awayGoals THEN 1 ELSE 0 END) AS draw
FROM games
WHERE awayTeamID = 87
GROUP BY 1;
```
**Output:**
| Season | Loss | Win | Draw |
|--------|------|-----|------|
| 2015   | 7    | 8   | 4    |
| 2016   | 4    | 10  | 5    |
| 2014   | 8    | 8   | 3    |
| 2017   | 5    | 9   | 5    |
| 2018   | 1    | 13  | 5    |
| 2019   | 3    | 14  | 2    |
| 2020   | 3    | 10  | 6    |

## Question 6: Average Shots on Target (Season-wise)
```sql
SELECT season, AVG(shotsontarget) 
FROM teamstats
WHERE teamid = 87
GROUP BY 1;
```
**Output:**
| Season | Avg Shots on Target |
|--------|---------------------|
| 2015   | 5.3158              |
| 2016   | 6.2105              |
| 2014   | 5.0526              |
| 2017   | 6.1316              |
| 2018   | 5.9474              |
| 2019   | 6.0789              |
| 2020   | 5.6316              |

## Question 7: Average Shots on Target (All Seasons)
```sql
SELECT teamid, AVG(shotsontarget) 
FROM teamstats
WHERE teamid = 87
GROUP BY 1;
```
**Output:**
| TeamID | Avg Shots on Target |
|--------|---------------------|
| 87     | 5.7669              |

## Question 8: Top 5 Players with Shots on Target
```sql
SELECT  p.name,
    COUNT(CASE WHEN s.shotResult = 'SavedShot' OR s.shotResult = 'BlockedShot' OR s.shotResult = 'Goal' THEN 1 END) AS shot_on_target 
FROM shots s
JOIN teamstats t ON t.gameid = s.gameID
JOIN players p ON p.playerid = s.shooterid
WHERE t.teamid = 87
GROUP BY 1 
ORDER BY shot_on_target DESC  
LIMIT 5;
```
**Output:**
| Name            | Shots on Target |
|-----------------|-----------------|
| Mohamed Salah  | 381             |
| Roberto Firmino | 360             |
| Philippe Coutinho | 261          |
| Sadio ManÈ      | 246             |
| Georginio Wijnaldum | 116        |

## Question 9: Top 5 Players with Shots Off Target
```sql
SELECT  p.name,
    COUNT(CASE WHEN s.shotResult = 'MissedShots' OR s.shotResult = 'ShotOnPost' OR s.shotResult = 'OwnGoal' THEN 1 END) AS shot_off_target
FROM shots s
JOIN teamstats t ON t.gameid = s.gameID
JOIN players p ON p.playerid = s.shooterid
WHERE t.teamid = 87
GROUP BY 1 
ORDER BY shot_off_target DESC 
LIMIT 5;
```
**Output:**
| Name            | Shots Off Target |
|-----------------|------------------|
| Mohamed Salah  | 157              |
| Sadio ManÈ      | 145              |
| Roberto Firmino | 143              |
| Philippe Coutinho | 113           |
| Georginio Wijnaldum | 84          |

## Question 10: Discipline Analysis
```sql
SELECT 
    p.name AS PlayerName, 
    SUM(a.yellowCard) AS TotalYellowCards, 
    SUM(a.redCard) AS TotalRedCards
FROM 
    appearances a
JOIN 
    players p ON a.playerID = p.playerID
JOIN 
    games g ON a.gameID = g.gameID
JOIN 
    teams t ON g.hometeamID = t.teamID OR g.awayteamID = t.teamID
WHERE 
    t.name = 'Liverpool'
GROUP BY 
    p.name 
ORDER BY 
    TotalYellowCards DESC, TotalRedCards DESC  
LIMIT 5;
```
**Output:**
| Player Name      | Total Yellow Cards | Total Red Cards |
|------------------|--------------------|-----------------|
| James Milner     | 30                 | 2               |
| Emre Can         | 25                 | 1               |
| Jordan Henderson | 19                 | 1               |
| Fabinho          | 18                 | 0               |
| Dejan Lovren     | 18                 | 0               |

