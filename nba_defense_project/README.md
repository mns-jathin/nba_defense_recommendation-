# NBA Defensive Analytics & Player Recommendation System

## Project Overview

This project is a **MySQL-based NBA defensive analytics and player recommendation system** built using NBA team, player, defensive statistics, and salary data.

The system analyzes defensive performance at both the **team and player level**, identifies defensive weaknesses, evaluates player defensive strengths, and recommends suitable players for teams based on their defensive needs and salary constraints.

No machine learning model is used in the final project. The recommendation system is implemented entirely using **SQL queries, views, window functions, joins, conditional logic, ranking, and a stored procedure**.

---

## Objectives

The project aims to:

1. Analyze NBA team defensive performance.
2. Identify specific defensive weaknesses for each team.
3. Measure individual player defensive strengths.
4. Compare player salaries with defensive value.
5. Recommend suitable defenders for a target team.
6. Provide ranked recommendations based on a calculated match score.

---

## Database

**Database:** `nba_defense`

### Main Base Tables

- `teams`
- `players`
- `salaries`
- `defensive_stats`
- `team_defense`

### Analytical Views

- `team_defensive_needs`
- `player_defensive_strength`
- `defensive_recommendations`
- `final_defensive_recommendations`

---

## Data Analysis

### Team Defensive Statistics

The `team_defense` table contains metrics such as:

- Defensive rebounds
- Steals
- Blocks
- Defensive rating
- Defensive rebound percentage
- Opponent points off turnovers
- Opponent second-chance points
- Opponent fast-break points
- Opponent paint points

Example:

```sql
SELECT
    team_name,
    dreb,
    stl,
    blk
FROM team_defense
ORDER BY dreb DESC;
```

This query ranks teams by defensive rebounds.

---

## Player Defensive Statistics

The `defensive_stats` table contains player-level defensive metrics including:

- Defensive rebounds per game
- Steals per game
- Blocks per game
- Defensive rating
- Defensive rebound percentage
- Steal percentage
- Block percentage
- Opponent scoring metrics

Example:

```sql
SELECT
    player_name,
    team_name,
    dreb_per_game,
    stl_per_game,
    blk_per_game
FROM defensive_stats
ORDER BY def_rtg
LIMIT 10;
```

---

## Player Defensive Strength

The `player_defensive_strength` view converts important player statistics into percentile-based strength scores.

The three major components are:

- `rebounding_strength`
- `perimeter_strength`
- `rim_strength`

These scores use SQL window functions such as `PERCENT_RANK()`.

Example:

```sql
SELECT
    player_name,
    team_name,
    dreb_per_game,
    stl_per_game,
    blk_per_game,
    rebounding_strength,
    perimeter_strength,
    rim_strength
FROM player_defensive_strength
ORDER BY
    (rebounding_strength + perimeter_strength + rim_strength) DESC
LIMIT 10;
```

This produces a ranked list of strong defensive players.

---

## Team Defensive Needs

The `team_defensive_needs` view compares each team's defensive metrics against league averages.

It classifies teams into categories such as:

- Good Rebounding / Weak Rebounding
- Good Perimeter Defense / Weak Perimeter Defense
- Good Rim Protection / Weak Rim Protection
- Good 2nd Chance Defense / Allows Too Many 2nd Chance Points
- Good Paint Defense / Allows Too Many Paint Points

Example:

```sql
SELECT *
FROM team_defensive_needs;
```

This provides a defensive profile for every team.

---

## Player Recommendation System

The `defensive_recommendations` view matches players to teams based on the defensive weaknesses identified in `team_defensive_needs`.

The recommendation score considers:

- Defensive rating
- Rebounding ability
- Steal ability
- Block ability
- Defensive rebound percentage
- Team defensive weakness
- Player salary

A higher `match_score` indicates a stronger fit for the target team's defensive needs.

---

## Final Recommendations

The `final_defensive_recommendations` view ranks recommendations for each target team and returns the best available match within the salary constraint.

Example:

```sql
SELECT *
FROM final_defensive_recommendations
ORDER BY target_team, match_score DESC;
```

The output contains:

- `target_team`
- `player_name`
- `current_team`
- `salary_millions`
- `match_score`

---

## Stored Procedure

The project includes a stored procedure:

```sql
CALL recommend_defenders(target_team, max_salary, number_of_players);
```

### Example 1

```sql
CALL recommend_defenders('LAL', 12.5, 4);
```

This requests the top 4 defensive recommendations for the Los Angeles Lakers with a maximum salary of $12.5 million.

### Example 2

```sql
CALL recommend_defenders('BKN', 20, 4);
```

This requests the top 4 defensive recommendations for Brooklyn with a maximum salary of $20 million.

---

## Example Results

### LAL Recommendation

For:

```sql
CALL recommend_defenders('LAL', 12.5, 4);
```

Top recommendations included:

| Player | Current Team | Salary ($M) | Match Score |
|---|---|---:|---:|
| Walker Kessler | UTA | 7.06 | 68.03 |
| Alex Sarr | WAS | 12.37 | 63.42 |
| Ausar Thompson | DET | 11.12 | 62.80 |
| Derik Queen | NOP | 5.42 | 62.14 |

### BKN Recommendation

For:

```sql
CALL recommend_defenders('BKN', 20, 4);
```

Top recommendations included:

| Player | Current Team | Salary ($M) | Match Score |
|---|---|---:|---:|
| Walker Kessler | UTA | 7.06 | 67.26 |
| Onyeka Okongwu | ATL | 16.12 | 64.47 |
| Cooper Flagg | DAL | 14.52 | 64.23 |
| Derik Queen | NOP | 5.42 | 62.95 |

---

## SQL Concepts Used

The project demonstrates the following SQL concepts:

- `SELECT`
- `WHERE`
- `ORDER BY`
- `GROUP BY`
- `JOIN`
- `LEFT JOIN`
- `CASE WHEN`
- Aggregate functions such as `AVG()`
- Common table expressions (`WITH`)
- Window functions
- `PERCENT_RANK()`
- `ROW_NUMBER()`
- Views
- Stored procedures
- Salary filtering
- Ranking
- Calculated columns
- Conditional scoring

---

## Project Workflow

```text
NBA Data
   |
   v
Base Tables
   |
   +--> Team Defensive Statistics
   |
   +--> Player Defensive Statistics
   |
   v
Team Defensive Needs
   |
   v
Player Defensive Strength
   |
   v
Player-to-Team Matching
   |
   v
Salary Filtering
   |
   v
Match Score
   |
   v
Final Ranked Recommendations
```

---

## How to Run

### 1. Open MySQL

Open the MySQL command-line client.

### 2. Select the database

```sql
USE nba_defense;
```

### 3. Verify the tables

```sql
SHOW TABLES;
```

### 4. Verify the analytical views

```sql
SHOW FULL TABLES;
```

The database should contain the base tables and analytical views listed above.

### 5. Run analysis queries

For example:

```sql
SELECT *
FROM team_defensive_needs;
```

or:

```sql
SELECT *
FROM player_defensive_strength
ORDER BY
    (rebounding_strength + perimeter_strength + rim_strength) DESC
LIMIT 10;
```

### 6. Run the recommendation procedure

```sql
CALL recommend_defenders('LAL', 12.5, 4);
```

or:

```sql
CALL recommend_defenders('BKN', 20, 4);
```

---

## Conclusion

This project demonstrates how SQL can be used to build a complete analytical recommendation system without machine learning.

The system transforms raw NBA defensive statistics into meaningful team defensive profiles, evaluates player defensive strengths, incorporates salary constraints, and produces ranked player recommendations for teams.

The final result is a practical **NBA Defensive Analytics and Player Recommendation System implemented entirely in MySQL**.
