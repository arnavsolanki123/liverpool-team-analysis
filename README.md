# liverpool-team-analysis

# Team Analysis SQL Queries

This repository contains a set of SQL queries used to analyze the performance and statistics of a specific football team, in this case, Liverpool. Below are the queries categorized by the type of analysis they perform:

## Query 1: Identify the Team Being Analyzed
```sql
SELECT name, teamid 
FROM teams
WHERE name = 'Liverpool';
```

## Query 2: Team Performance Metrics
```sql
SELECT t.name,
       SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS win, 
       SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS loss, 
       SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS draw
FROM teamstats t1
JOIN teams t USING(teamid)
WHERE t1.teamid = 87
GROUP BY t.name;
```

## Query 3: Season-wise Goal Comparison
```sql
SELECT t.season, SUM(t.goals), t.teamid 
FROM teamstats t
JOIN games g USING(gameid)
WHERE t.teamid = 87
GROUP BY t.season;
```

## Query 4: Season Wins and Losses Comparison
```sql
SELECT t1.season,
       SUM(CASE WHEN result = 'W' THEN 1 ELSE 0 END) AS win, 
       SUM(CASE WHEN result = 'L' THEN 1 ELSE 0 END) AS loss, 
       SUM(CASE WHEN result = 'D' THEN 1 ELSE 0 END) AS draw
FROM teamstats t1
WHERE t1.teamid = 87
GROUP BY t1.season;
```

## Query 5: Results as a Home Team
```sql
SELECT gameid, season, 
       (CASE 
            WHEN homeTeamID = 87 AND homeGoals > awayGoals THEN 'W'  
            WHEN homeTeamID = 87 AND homeGoals < awayGoals THEN 'L'  
            WHEN homeTeamID = 87 AND homeGoals = awayGoals THEN 'D' 
        END) AS hometeamsresults 
FROM games
WHERE homeTeamID = 87;
```

## Query 6: Results as an Away Team
```sql
SELECT gameid, season, 
       (CASE 
            WHEN awayTeamID = 87 AND homeGoals > awayGoals THEN 'L'  
            WHEN awayTeamID = 87 AND homeGoals < awayGoals THEN 'W'  
            WHEN awayTeamID = 87 AND homeGoals = awayGoals THEN 'D'  
        END) AS awayteamresults
FROM games
WHERE awayTeamID = 87;
```

## Query 7: Average Shots on Target (Season-wise)
```sql
SELECT season, AVG(shotsontarget) 
FROM teamstats
WHERE teamid = 87
GROUP BY season;
```

## Query 8: Average Shots on Target (All Seasons)
```sql
SELECT teamid, AVG(shotsontarget) 
FROM teamstats
WHERE teamid = 87
GROUP BY teamid;
```

## Query 9: Top 5 Players with Shots on Target
```sql
SELECT p.name,
       COUNT(CASE 
                 WHEN s.shotResult IN ('SavedShot', 'BlockedShot', 'Goal') THEN 1 
             END) AS shot_on_target 
FROM shots s
JOIN teamstats t ON t.gameid = s.gameID
JOIN players p ON p.playerid = s.shooterid
WHERE t.teamid = 87
GROUP BY p.name 
ORDER BY shot_on_target DESC  
LIMIT 5;
```

## Query 10: Top 5 Players with Shots off Target
```sql
SELECT p.name,
       COUNT(CASE 
                 WHEN s.shotResult IN ('MissedShots', 'ShotOnPost', 'OwnGoal') THEN 1 
             END) AS shot_off_target
FROM shots s
JOIN teamstats t ON t.gameid = s.gameID
JOIN players p ON p.playerid = s.shooterid
WHERE t.teamid = 87
GROUP BY p.name 
ORDER BY shot_off_target DESC 
LIMIT 5;
```

## Query 11: Disciplinary Analysis
```sql
SELECT p.name AS PlayerName, 
       SUM(a.yellowCard) AS TotalYellowCards, 
       SUM(a.redCard) AS TotalRedCards
FROM appearances a
JOIN players p ON a.playerID = p.playerID
JOIN games g ON a.gameID = g.gameID
JOIN teams t ON g.hometeamID = t.teamID OR g.awayteamID = t.teamID
WHERE t.name = 'Liverpool'
GROUP BY p.name 
ORDER BY TotalYellowCards DESC, TotalRedCards DESC  
LIMIT 5;
