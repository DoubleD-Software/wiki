---
title: Database
description: The table layout for the DTTS SQLite database.
published: true
date: 2024-09-13T21:58:04.068Z
tags: 
editor: markdown
dateCreated: 2024-09-13T21:51:20.633Z
---

# Database

The database is a simple SQLite implementation on the ESP32 which is written to the connected SD card.

## Schema

The following schema is being used to store the data of the time tracking system.

![Database schema](https://assets.double-d.software/api/public/dl/0WiV6Pwx?inline=true)

### DBML code

```
Table runs {
  id int pk
  type int
  date int
  class_id int [ref: > classes.id]
  grading_key_m_id int [ref: > grading_keys.id]
  grading_key_f_id int [ref: > grading_keys.id]
  teacher_id int [ref: > teachers.id]
  length int
  laps real
}

Table participants {
  student_id int [ref: > students.id]
  run_id int [ref: > runs.id]
}

Table results {
  id int pk
  type int
  run_id int [ref: > runs.id]
  student_id int [ref: > students.id]
  time real
  grade real
  date int
}

Table laps {
  lap_num int
  result_id int [ref: > results.id]
  time real
  length int
}

Table students {
  id int pk
  name varchar
  gender int
  class_id int [ref: > classes.id]
}

Table classes {
  id int pk
  name varchar
  sprint_avg_grade real
  sprint_avg_time int
  lap_run_avg_grade real
  lap_run_avg_time int
}

Table teachers {
  id int pk
  name varchar
  username varchar
  password varchar
  administrator int
}

Table grading_keys {
  id int pk
  name varchar
  type int
  length int
  gender int
}

Table grading_keys_grades {
  grading_key_id int [ref: > grading_keys.id]
  time real
  grade real
}
```