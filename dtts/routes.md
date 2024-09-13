---
title: Routes
description: All the route and webhook communication specification.
published: true
date: 2024-09-13T21:54:13.705Z
tags: 
editor: markdown
dateCreated: 2024-09-13T21:54:13.705Z
---

# Routes
## Frontend routes
These routes just return HTML and shall be requested with the **GET** method. The backend does not fill templates with data. All data is gathered afterwards through an additional REST API request.

Click on each route to view a design of the page. The designs linked may differ from the final implementation and only serve as a reference.

### Runs
**[/runs](https://assets.double-d.software/api/public/dl/_zdoj2RD?inline=true)**: Returns the HTML page to view all runs.

**[/runs/view?id=[idOfRun]](https://assets.double-d.software/api/public/dl/bawiD6li?inline=true)**: Returns the HTML page to view the specified run.

**[/runs/view?id=[idOfRun]&student=[idOfStudent]](https://assets.double-d.software/api/public/dl/Q1Drov9t?inline=true)**: Returns the HTML page to view the laps for one student.

**[/runs/new](https://assets.double-d.software/api/public/dl/gdaqM_PY?inline=true)**: Returns the HTML page to create a new run.

### Students
**[/students/view?id=[idOfStudent]](https://assets.double-d.software/api/public/dl/NhP-TrT7?inline=true)**: Returns the HTML page to view one student.

**[/students/new](https://assets.double-d.software/api/public/dl/lQG2UdMP?inline=true)**: Returns the HTML page to create a student.

**[/students/edit?id=[idOfStudent]](https://assets.double-d.software/api/public/dl/JAmcromH?inline=true)**: Returns the HTML page to edit a student.

### Classes
**[/classes](https://assets.double-d.software/api/public/dl/Er0rHV0D?inline=true)**: Returns the HTML page to view all classes.

**[/classes/view?id=[idOfClass]](https://assets.double-d.software/api/public/dl/OMxuNNeM?inline=true)**: Returns the HTML page to view the specified class. ([Students view](https://assets.double-d.software/api/public/dl/zATYI9PV?inline=true))

**[/classes/new](https://assets.double-d.software/api/public/dl/lpxOGIyp?inline=true)**: Returns the HTML page to create a class.

**[/classes/edit?id=[idOfClass]](https://assets.double-d.software/api/public/dl/_3xtWBiI?inline=true)**: Returns the HTML page to edit a class.

### Grading keys
**[/gradingkeys](https://assets.double-d.software/api/public/dl/zEVEAtoS?inline=true)**: Returns the HTML page to view all grading keys.

**[/gradingkeys/view?id=[idOfGradingKey]](https://assets.double-d.software/api/public/dl/Bvndzs5p?inline=true)**: Returns the HTML page to view the specified grading key.

**[/gradingkeys/new](https://assets.double-d.software/api/public/dl/YEZwJOgD?inline=true)**: Returns the HTML page to create a grading key.

**[/gradingkeys/edit?id=[idOfGradingKey]](https://assets.double-d.software/api/public/dl/LaP1hDos?inline=true)**: Returns the HTML page to edit a grading key.

### Teachers
**[/teachers](https://assets.double-d.software/api/public/dl/K8jLjRdu?inline=true)**: Returns the HTML page to view all teachers.

**[/teachers/view?id=[idOfTeacher]](https://assets.double-d.software/api/public/dl/-52W0aP8?inline=true)**: Returns the HTML page to view the specified teacher's profile.

**[/teachers/new](https://assets.double-d.software/api/public/dl/oYP5B4rx?inline=true)**: Returns the HTML page to create a teacher profile.

**[/teachers/edit?id=[idOfTeacher]](https://assets.double-d.software/api/public/dl/jwoyqdlJ?inline=true)**: Returns the HTML page to edit a teacher's profile.

### Active (right before and during a run)
**[/active/tags](https://assets.double-d.software/api/public/dl/El7Xr-Nr?inline=true)**: Returns the HTML page to assign the tags. ([Tag detected](https://assets.double-d.software/api/public/dl/FvjM25R7?inline=true))

**[/active/dash](https://assets.double-d.software/api/public/dl/FMx5xxUd?inline=true)**: Returns the HTML page to monitor the run. ([When started](https://assets.double-d.software/api/public/dl/YU__D_9J?inline=true))

### Login
**[/login](https://assets.double-d.software/api/public/dl/jF14Di68?inline=true)**: Returns the HTML page to log in.

## Websocket connections
### Active run
This messaging routine is used while a run is currently in action. It will be used to regularly synchronize the time and the people that have finished.

![Active run websocket connection](https://assets.double-d.software/api/public/dl/54GOKRpb/DTTS/active_run_wsconn.svg) 

The finishers are sent in the following format:

```yaml
{
  "finished": {
    "0": {
      "name": "Bali Schmidt",
      "time": 14200
    },
    "1": {
      "name": "Ali Baba",
      "time": 16200
    } 
  }
}
```

### Tag assignment
This messaging routine is used after configuring a run and before starting it. It is used while assigning the tags to the respective students.

![Tag assignment](https://assets.double-d.software/api/public/dl/S0X5Ss3S/DTTS/tag_assign_wsconn.svg)

## API routes
This contains all API routes for REST operations. The API endpoints are used in the frontend to gather the required data.

### Data types
There are certain data types used for certain data. That includes:

**Date**: Julian Day (i.e. `06.11.2024 = 2460620`)

**Time**: Milliseconds (i.e. `14,90s = 149000`)

**Length**: Meters (i.e. `3500m = 3500`)

**Gender**: `0` (male), `1` (female)

**Run Type**: `0` (sprint), `1` (lap run)

### Runs
**GET /api/runs?date=[julianDay]**

Returns the list of runs on that particular Julian Day or a **404 Not Found** satus code in case of a lack of runs. The map key is the id of the run, not an index.

Example return body:
```yaml
{
  "0": {
    "type": 0,
    "length": 100,
    "teacher": "Hr. Schwarzenegger",
    "class": "9y",
    "avg_grade": "2.75",
    "avg_time": 14900
  },
  "1": {
    "type": 1,
    "length": 3200,
    "teacher": "Hr. Schwarzenegger",
    "class": "9y",
    "avg_grade": "2.25",
    "avg_time": 1024000
  }
}
```

---

**GET /api/runs?id=[idOfRun]**

Returns details of the specified run run including students, their times and grades. The map key is the id of the student, not an index.

If the run doesn't exist in the database, a **404 Not Found** status code is returned.

Example return body:
```yaml
{
  "type": 0,
  "length": 100,
  "date": 2460336,
  "teacher": "Hr. Schwarzenegger",
  "class": "9y",
  "grading_key_male": "9 Sprint (m)",
  "grading_key_female": "9 Sprint (w)",
  "avg_grade": "2.75",
  "avg_time": 14900,
  "students": {
    "7": {
      "name": "Bali Schmidt",
      "time": 14200,
      "gender": 0,
      "grade": "2.00"
    },
    "10": {
      "name": "Ali Baba",
      "time": 16200,
      "gender": 0,
      "grade": "3.90"
    }
  }
}
```

---

**GET /api/runs?id=[idOfRun]&student=[idOfStudent]**

Returns the info for the specified student who participated in the specified run which includes the laps. This request can only be performed on lap runs, not sprints. The key of the map is not an id, it is the index, starting regularily at `1` counting the laps.

If the run or the student don't exist in the database, a **404 Not Found** is returned. If the specified run is a sprint, a **406 Not Acceptable** status code is returned.

Example return body:
```yaml
{
  "name": "Bali Schmidt",
  "time": 770000,
  "grade": "2.00",
  "length": 750,
  "rounds": {
    "1": {
      "length": 150,
      "time": 131000
    },
    "2": {
      "length": 300,
      "time": 312000
    },
    "3": {
      "length": 300,
      "time": 327000
    }
  }
}
```

---

**DELETE /api/runs?id=[idOfRun]**

Deletes the run and all data associated with it. Either returns a **200 OK** status code if the operation was completed successfully or a **404 Not Found** status code if the run doesn't exist. This endpoint doesn't return a response body.

---

**PUT /api/runs**

Creates a run with the specified parameters. Returns a **200 OK** status code if the run has been created successfully and the client can continue to the tag assigning process or a **400 Bad Request** if the request body is insufficient. The laps paraneter is set to `-1.0` if the type is a sprint and no laps are required. This endpoint doesn't respond with the id of the created run, as the client can immediately redirect to the `/active/tags` endpoint to the tag assigning process. 

Example request body:
```yaml
{
  "type": 0,
  "length": 100,
  "date": 2460336,
  "class_id": 0,
  "grading_key_male_id": 0,
  "grading_key_female_id": 1,
  "laps": "-1.0",
  "participants": [0, 1]
}
```

### Students

**GET /api/students?id=[idOfStudent]**

Returns information about a student including the runs the student has done or a **404 Not Found** if the student doesn't exist in the database. The key of the `runs` map isn't an index, it is the id of the run.

Example return body:
```yaml
{
  "name": "Bali Schmidt",
  "gender": 0,
  "class": "8o",
  "global_avg_grade": "3.30",
  "sprint": {
    "avg_grade": "2.75",
    "avg_time": 14900
  },
  "lap_run": {
    "avg_grade": "3.90",
    "avg_time": 1187000
  },
  "runs": {
    "0": {
      "type": 0,
      "length": 100,
      "grade": "2.75",
      "time": 14900,
      "date": 2460645
    },
    "1": {
      "type": 1,
      "length": 3500,
      "grade": "3.90",
      "time": 1187000,
      "date": 2460589
    }
  }
}
```

---

**DELETE /api/students?id=[idOfStudent]**

Deletes all data containing this student. Any runs associated with this student will also get deleted, while the data for the other students that also participated in that run will stay. This action is irreversible. Returns a **200 OK** status code if the operation was completed successfully, and a **404 Not Found** status code if the student doesn't exist in the database.

---

**PUT /api/students**

Creates a new student with the specified parameters. Returns a **201 Created** status code containing the id of the newly created student in the response body if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, or a **409 Conflict** status code if the set name already exists in the specified class.

Example request body:
```yaml
{
  "name": "Bali Schmidt",
  "gender": 0,
  "class_id": 0
}
```

Example response body:
```yaml
{
  "id": 1
}
```

---

**PATCH /api/students?id=[idOfStudent]**

Edits/updates the specified student. A **GET** request is recommended to retrieve the *current* information as all provided values will get updated. Returns a **200 OK** status code if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, a **404 Not Found** status code if the specified student doesn't exist or a **409 Conflict** if the specified name already exists in the specified class. The response doesn't have a response body.

Example request body:
```yaml
{
  "name": "Bali Schmidt",
  "class_id": 0
}
```

### Grading keys

**GET /api/gradingkeys**

Returns all current grading keys with some information and their ids.

Example return body:
```yaml
{
  "0": {
    "name": "8 Sprint",
    "type": 0,
    "gender": 0,
    "min_time": 12270,
    "length": 100
  },
  "1": {
    "name": "8 Rundenlauf",
    "type": 1,
    "gender": 0,
    "min_time": 1009000,
    "length": 3500
  }
}
```

---

**GET /api/gradingkeys?id=[idOfKey]**

Returns information about a grading key or a **404 Not Found** if the grading key doesn't exist in the database.

Example return body:
```yaml
{
  "name": "8 Sprint",
  "type": 0,
  "length": 75,
  "gender": 0,
  "grades": {
    "1.00": 10600,
    "1.25": 10800,
    "1.50": 11000,
    "1.75": 11200,
    "2.00": 11300,
    "2.25": 11500,
    "2.50": 11600,
    "2.75": 11700,
    "3.00": 11900,
    "3.25": 12000,
    "3.50": 12100,
    "3.75": 12200,
    "4.00": 12700,
    "4.25": 12900,
    "4.50": 13000,
    "4.75": 13100,
    "5.00": 13300,
    "5.25": 13400,
    "5.50": 13500,
    "5.75": 13600,
    "6.00": 13700
  }
}

```

---

**DELETE /api/gradingkeys?id=[idOfKey]**

Deletes the specified grading keys. All runs using this key won't change as the grades have been calculated beforehand. This action is irreversible and the grading cannot be edited anymore. Returns a **200 OK** status code if the operation was completed successfully, and a **404 Not Found** status code if the grading key doesn't exist in the database.

---

**PUT /api/gradingkeys**

Creates a new grading key with the specified parameters. Returns a **201 Created** status code containing the id of the newly created grading key in the response body if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, or a **409 Conflict** status code if the set name already exists. 

All the entered times get translated to milliseconds on the client before being sent to the server.

Example request body:
```yaml
{
  "name": "8 Sprint",
  "type": 0,
  "length": 75,
  "gender": 0,
  "grades": {
    "1.00": 10600,
    "1.25": 10800,
    "1.50": 11000,
    "1.75": 11200,
    "2.00": 11300,
    "2.25": 11500,
    "2.50": 11600,
    "2.75": 11700,
    "3.00": 11900,
    "3.25": 12000,
    "3.50": 12100,
    "3.75": 12200,
    "4.00": 12700,
    "4.25": 12900,
    "4.50": 13000,
    "4.75": 13100,
    "5.00": 13300,
    "5.25": 13400,
    "5.50": 13500,
    "5.75": 13600,
    "6.00": 13700
  }
}

```

Example response body:
```yaml
{
  "id": 1
}
```

---

**PATCH /api/gradingkeys?id=[idOfKey]**

Edits/updates the specified grading key. A **GET** request is recommended to retrieve the *current* information as all provided values will get updated. Returns a **200 OK** status code if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, a **404 Not Found** status code if the specified grading key doesn't exist or a **409 Conflict** if the specified name already exists. The response doesn't have a response body. 

The grades of finished runs will *not* be recalculated, only following runs will reflect the change. The grading key run type cannot be edited.

Example request body:
```yaml
{
  "name": "8 Sprint",
  "length": 75,
  "grades": {
    "1.00": 10600,
    "1.25": 10800,
    "1.50": 11000,
    "1.75": 11200,
    "2.00": 11300,
    "2.25": 11500,
    "2.50": 11600,
    "2.75": 11700,
    "3.00": 11900,
    "3.25": 12000,
    "3.50": 12100,
    "3.75": 12200,
    "4.00": 12700,
    "4.25": 12900,
    "4.50": 13000,
    "4.75": 13100,
    "5.00": 13300,
    "5.25": 13400,
    "5.50": 13500,
    "5.75": 13600,
    "6.00": 13700
  }
}

```

---

**GET /api/gradingkeys?type=[runType]&length=[runLength]**

Retrieves all grading keys that match the specified filter for both genders, male and female. This should only respond with a **400 Bad Request** status code, if the run type is invalid. If there are no grading keys matching the criteria, simply return an empty map for the gender. Otherwise return a **200 OK** status code.

This is used to search for the correct grading keys while creating a run.

Example return body:
```yaml
{
  "male": {
      "0": "8 Sprint (m)",
      "1": "9 Sprint (m)"
  },
  "female": {
      "2": "8 Sprint (w)",
      "3": "9 Sprint (w)"
  }
}
```

### Classes

**GET /api/classes**

Returns all current classes with some information and their ids.

Example return body:
```yaml
{
  "0": {
    "name": "8o",
    "size": 23,
    "sprint": {
      "avg_grade": "2.75",
      "avg_time": 14900
    },
    "lap_run": {
      "avg_grade": "3.90",
      "avg_time": 1187000
    }
  }
}
```

---

**GET /api/classes?id=[idOfClass]**

Returns information about a class including the runs and the students or a **404 Not Found** if the class doesn't exist in the database. The key of the `runs` map and of the `students` map isn't an index, it is the id of the runs or students respectively.

Example return body:
```yaml
{
  "name": "8o",
  "global_avg_grade": "3.30",
  "sprint": {
    "avg_grade": "2.75",
    "avg_time": 14900
  },
  "lap_run": {
    "avg_grade": "3.90",
    "avg_time": 1187000
  },
  "runs": {
    "0": {
      "type": 0,
      "length": 100,
      "avg_grade": "2.75",
      "avg_time": 14900,
      "date": 2460645
    },
    "1": {
      "type": 1,
      "length": 3500,
      "avg_grade": "3.90",
      "avg_time": 1187000,
      "date": 2460620
    }
  },
  "students": {
    "0": {
      "name": "Bali Schmidt",
      "gender": 0,
      "avg_grade": "2.00"
    },
    "1": {
      "name": "Ali Baba",
      "gender": 0,
      "avg_grade": "3.90"
    }
  }
}
```

---

**DELETE /api/classes?id=[idOfClass]**

Deletes all data containing this class and the class itself. Any runs and students and runs of the students associated with this class will also get deleted. This action is irreversible. Returns a **200 OK** status code if the operation was completed successfully, and a **404 Not Found** status code if the class doesn't exist in the database.

---

**PUT /api/classes**

Creates a new class with the specified parameters. Returns a **201 Created** status code containing the id of the newly created class in the response body if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, or a **409 Conflict** status code if the set name already exists.

Example request body:
```yaml
{
  "name": "8o"
}
```

Example response body:
```yaml
{
  "id": 1
}
```

---

**PATCH /api/classes?id=[idOfClass]**

Edits/updates the specified class. A **GET** request is recommended to retrieve the *current* information as all provided values will get updated. Returns a **200 OK** status code if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, a **404 Not Found** status code if the specified class doesn't exist or a **409 Conflict** if the specified name already exists. The response doesn't have a response body.

Example request body:
```yaml
{
  "name": "8o"
}
```

---

**GET /api/classes?id=[idOfClass]&studentsOnly=1**

This endpoint gets all students within a class in a map where the key is the id of the student and the value the name of the student.
This should only respond with a **400 Bad Request** status code if the value `studentsOnly` is anything other than `1`. 
Otherwise it should always return a **200 OK** status code, even if that means responding with an empty map. This endpoint is related to searching.

Example response body:
```yaml
{
  "0": "Bali Schmidt",
  "1": "Ali Baba"
}
```

---

**GET /api/classes?namesOnly=1**

This endpoint retrieves all classes in a map where the key is the id of the class and the value the name to display.
This should only respond with a **400 Bad Request** status code if the value `namesOnly` is anything other than `1`. 
Otherwise it should always return a **200 OK** status code, even if that means responding with an empty map. This endpoint is related to searching.

Example response body:
```yaml
{
  0: "8o",
  1: "8c"
}
```

### Teachers

**GET /api/teachers**

Returns all current teachers with some information and their ids.

Example return body:
```yaml
{
  "0": {
    "name": "Hr. Schwarzenegger"
  },
  "1": {
    "name": "Hr. Cruise"
  }
}
```

---

**GET /api/teachers?id=[idOfTeacher]**

Returns information about a teacher or a **404 Not Found** if the teacher doesn't exist in the database.

Example return body:
```yaml
{
  "name": "Hr. Schwarzenegger",
  "username": "SchwarzeneggerA-MPG"
}
```

---

**DELETE /api/teacher?id=[idOfTeacher]**

Deletes the specified teacher. Associated runs won't be deleted, but `N/A` will be displayed as the teacher of the runs. This action is irreversible. Returns a **200 OK** status code if the operation was completed successfully, and a **404 Not Found** status code if the teacher doesn't exist in the database.

This endpoint is reserved for the administrator account. If the user is not an administrator, a **403 Forbidden** status code shall be returned.

---

**PUT /api/teachers**

Creates a new teacher with the specified parameters. Returns a **201 Created** status code containing the id of the newly created teacher in the response body if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, or a **409 Conflict** status code if the set name or username already exists.

This endpoint is reserved for the administrator account. If the user is not an administrator, a **403 Forbidden** status code shall be returned.

Example request body:
```yaml
{
  "name": "Hr. Schwarzenegger",
  "username": "SchwarzeneggerA-MPG",
  "password": "Bananenkobold86!!"
}
```

Example response body:
```yaml
{
  "id": 1
}
```

---

**PATCH /api/teachers?id=[idOfTeacher]**

Edits/updates the specified teacher. A **GET** request is recommended to retrieve the *current* information as all provided values will get updated. Returns a **200 OK** status code if the operation was completed successfully, a **400 Bad Request** status code if the request body is insufficient, a **404 Not Found** status code if the specified teacher doesn't exist or a **409 Conflict** if the specified name or username already exists. The response doesn't have a response body.

This endpoint is reserved for the administrator account. If the user is not an administrator, a **403 Forbidden** status code shall be returned.

Example request body:
```yaml
{
  "name": "Hr. Schwarzenegger",
  "username": "SchwarzeneggerA-MPG",
  "password": "Bananenkobold86!!"
}
```

### Authentication

**POST /api/login**

This endpoint covers the login. This also provides the system with the current date (Julian Day) and determines if the user is an administrator. Returns a **200 OK** status code if the credentials are correct and sets the session cookies, otherwise the endpoint returns a **401 Unauthorized** status code. The cookie will also contain the information if the logged in user is the administrator. 

Example request body:
```yaml
{
  "username": "SchwarzeneggerA-MPG",
  "password": "Bananenkobold86!!",
  "date": 2460369
}
```

---

**GET /api/whoami**

To provide a pleasant UI experience to non-admin users, this endpoint returns whether the requesting user is admin or not. If the user isn't the frontend can hide certain buttons related to teacher creation, modification and deletion.

Example return body:
```yaml
{
  "admin": true
}
```

### Active (during or right before a run)

**GET /api/active**

The server checks if a run is in process and in which state it is: `tag` or `run` state. In both cases a websocket connection is established if a **200 OK** status code is sent. The endpoint returns a **404 Not Found** status code if no run is currently active.

If the run is currently in the `run` phase after the tag assigning process, the server sends a response body containing information which should be displayed.

For a websocket reference check the [websocket communcation documentation](#websocket-connections).

Example return body (when in `run` state):
```yaml
{
  "type": 0,
  "length": 100,
  "date": 2460336,
  "class": "8o",
  "grading_key_male": "8 Sprint (m)",
  "grading_key_female": "8 Sprint (w)",
  "teacher": "Hr. Schwarzenegger"
}
```

### Administrative Functions

**POST /api/admin/studentsReset**

Once this endpoint is executed, the system performs a reset of all student data. Classes, teachers and grading keys stay intact, while all runs and students are ereased from the database.

This endpoint can only be hit by the administrative user. Additionally, the password provided in the request body is to be compared to the password stored in the database as an additional security measure.

If the user is not logged in, a **401 Unauthorized** status code shall be returned. If the user is not an administrator, a **403 Forbidden** status code shall be returned. If the confirmation password does not match the saved one, a **400 Bad Request** status code is to be returned. 

Example request body:
```yaml
{
  "password": "Bananenkobold86!!"
}
```

---

**POST /api/admin/factoryReset**

Once this endpoint is executed, the system performs a factory reset. All database data is wiped. No configuration remains.

This endpoint can only be hit by the administrative user. Additionally, the password provided in the request body is to be compared to the password stored in the database as an additional security measure.

If the user is not logged in, a **401 Unauthorized** status code shall be returned. If the user is not an administrator, a **403 Forbidden** status code shall be returned. If the confirmation password does not match the saved one, a **400 Bad Request** status code is to be returned. 

Example request body:
```yaml
{
  "password": "Bananenkobold86!!"
}
```