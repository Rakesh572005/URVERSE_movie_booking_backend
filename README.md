#  Movie Ticket Booking Backend (SQL + Java)

This backend powers a **Movie Ticket Booking System** that allows:
- Users to **register & log in**
- Admins to **add movies** and manage seats
- Customers to **view movies** and book tickets
- Admins to **view all bookings**

The system uses **Java + JDBC** to connect to a relational database and perform operations with SQL queries.

---

##  Database Tables Overview

### 1. `NUSER_DETAILS`
Stores user credentials.

| Column   | Type              | Description                                    |
|----------|-------------------|------------------------------------------------|
| NUSERID  | NUMBER (PK)       | Unique user ID                                  |
| UNAME    | VARCHAR2(4000)    | Username                                        |
| PASSWORD | VARCHAR2(4000)    | User's password (hashed recommended)            |

#### Query to create the table:
CREATE TABLE NUSER_DETAILS (
    NUSERID NUMBER PRIMARY KEY,
    UNAME VARCHAR2(4000),
    PASSWORD VARCHAR2(4000)
);

---

### 2. `ADMIN_DETAILS`
Stores admin credentials.

| Column   | Type              | Description                                    |
|----------|-------------------|------------------------------------------------|
| ADMINID  | NUMBER (PK)       | Unique admin ID                                 |
| UNAME    | VARCHAR2(4000)    | Admin username                                  |
| PASSWORD | VARCHAR2(4000)    | Admin password (hashed recommended)             |

#### Query to create the table:
CREATE TABLE ADMIN_DETAILS (
    ADMINID NUMBER PRIMARY KEY,
    UNAME VARCHAR2(4000),
    PASSWORD VARCHAR2(4000)
);

---

### 3. `MOVIE_DESC`
Stores movie details.

| Column         | Type              | Description                                   |
|----------------|-------------------|-----------------------------------------------|
| MOVIE_DESC_ID  | NUMBER (PK)       | Unique movie description ID                   |
| MOVIE_DESC     | VARCHAR2(2000)    | Description of the movie                      |
| TIME           | VARCHAR2(4000)    | Show time                                     |
| GENRE          | VARCHAR2(4000)    | Genre                                         |
| RATING         | VARCHAR2(4000)    | Rating (e.g., IMDb score)                     |
| LIKES          | VARCHAR2(4000)    | Likes count                                   |
| YEAR           | VARCHAR2(4000)    | Release year                                  |
| IMAGE          | VARCHAR2(4000)    | Movie poster URL                              |
| MOVIENAME      | VARCHAR2(4000)    | Movie name                                    |

#### Query to create the table:
CREATE TABLE MOVIE_DESC (
    MOVIE_DESC_ID NUMBER PRIMARY KEY,
    MOVIE_DESC VARCHAR2(2000),
    TIME VARCHAR2(4000),
    GENRE VARCHAR2(4000),
    RATING VARCHAR2(4000),
    LIKES VARCHAR2(4000),
    YEAR VARCHAR2(4000),
    IMAGE VARCHAR2(4000),
    MOVIENAME VARCHAR2(4000)
);

---

### 4. `MASTER1`
Stores movie show metadata.

| Column        | Type              | Description                                   |
|---------------|-------------------|-----------------------------------------------|
| MOVIEID       | NUMBER (PK)       | Unique movie show ID                          |
| MOVIE_NAME    | VARCHAR2(100)     | Movie name                                    |
| THEATRE_NAME  | VARCHAR2(4000)    | Theatre name                                  |
| TIMINGS       | VARCHAR2(4000)    | Show time                                     |

#### Query to create the table:
CREATE TABLE MASTER1 (
    MOVIEID NUMBER PRIMARY KEY,
    MOVIE_NAME VARCHAR2(100),
    THEATRE_NAME VARCHAR2(4000),
    TIMINGS VARCHAR2(4000)
);

---

### 5. `SLAVE1`
Stores seat availability and ticket prices.

| Column         | Type              | Description                                   |
|----------------|-------------------|-----------------------------------------------|
| MOVIEID        | NUMBER (FK)       | Linked movie show ID                          |
| A_CLASS        | NUMBER(10,0)      | Available A-class seats                       |
| B_CLASS        | NUMBER            | Available B-class seats                       |
| C_CLASS        | NUMBER(10,0)      | Available C-class seats                       |
| A_CLASS_COST   | NUMBER(10,0)      | Ticket price for A-class                      |
| B_CLASS_COST   | NUMBER(10,0)      | Ticket price for B-class                      |
| C_CLASS_COST   | NUMBER(10,0)      | Ticket price for C-class                      |

#### Query to create the table:
CREATE TABLE SLAVE1 (
    MOVIEID NUMBER,
    A_CLASS NUMBER(10,0),
    B_CLASS NUMBER,
    C_CLASS NUMBER(10,0),
    A_CLASS_COST NUMBER(10,0),
    B_CLASS_COST NUMBER(10,0),
    C_CLASS_COST NUMBER(10,0),
    CONSTRAINT fk_slave_movie FOREIGN KEY (MOVIEID) REFERENCES MASTER1(MOVIEID)
);

---

### 6. `BOOK_DETAILS`
Stores booking records.

| Column        | Type              | Description                                   |
|---------------|-------------------|-----------------------------------------------|
| ID            | NUMBER(10,0) (PK) | Booking ID                                    |
| MOVIENAME     | VARCHAR2(4000)    | Movie name                                    |
| THEATRENAME   | VARCHAR2(4000)    | Theatre name                                  |
| TIMINGS       | VARCHAR2(4000)    | Show time                                     |
| SEATS_BOOKED  | VARCHAR2(4000)    | Seat numbers                                  |
| TOTAL_PRICE   | NUMBER(10,0)      | Total booking cost                            |
| URL           | VARCHAR2(4000)    | Ticket or QR code URL                         |

#### Query to create the table:
CREATE TABLE BOOK_DETAILS (
    ID NUMBER(10,0) PRIMARY KEY,
    MOVIENAME VARCHAR2(4000),
    THEATRENAME VARCHAR2(4000),
    TIMINGS VARCHAR2(4000),
    SEATS_BOOKED VARCHAR2(4000),
    TOTAL_PRICE NUMBER(10,0),
    URL VARCHAR2(4000)
);

---

##  SQL Queries and Explanations

### 1. **User Registration**

#### Query:
INSERT INTO NUSER_DETAILS(uname, password) VALUES (?, ?);

- Inserts a new user’s credentials into the `NUSER_DETAILS` table.
- **Parameters:**
  - `uname`: The username entered by the user.
  - `password`: The password entered by the user (should be hashed before storage).
- **Purpose:** Allows new users to register for the movie ticket booking system.

---

### 2. **User Authentication**
#### Query:
SELECT * FROM NUSER_DETAILS WHERE uname = ? AND password = ?;

- Checks if the provided username and password exist in the `NUSER_DETAILS` table.
- **Parameters:**
  - `uname`: Username entered by the user.
  - `password`: Password entered by the user.
- **Purpose:** To verify whether a user is authenticated before allowing access to the system.

---

### 3. **Add Movie Details (Admin)**
#### Query:
INSERT INTO MOVIE_DESC(movie_desc, time, genre, rating, likes, year, image, moviename)
VALUES (?, ?, ?, ?, ?, ?, ?, ?);

- Inserts a new movie record into the `MOVIE_DESC` table.
- **Parameters:**
  - `movie_desc`: Description of the movie.
  - `time`: Show time for the movie.
  - `genre`: Movie genre (e.g., Action, Drama).
  - `rating`: Rating of the movie (e.g., IMDb score).
  - `likes`: Number of likes or upvotes for the movie.
  - `year`: Release year of the movie.
  - `image`: URL/path to the movie's poster image.
  - `moviename`: Name of the movie.
- **Purpose:** Allows the admin to add new movie releases into the system.

---

### 4. **Fetch All Movies**
#### Query:
SELECT * FROM MOVIE_DESC;

- Retrieves all records from the `MOVIE_DESC` table.
- **Purpose:** Displays all available movies to users so they can browse and choose shows for booking.

---

### 5. **Get Seat Availability (JOIN Query)**
#### Query:
SELECT 
    s.A_CLASS, 
    s.B_CLASS, 
    s.C_CLASS, 
    s.A_CLASS_COST, 
    s.B_CLASS_COST, 
    s.C_CLASS_COST, 
    s.MOVIEID
FROM SLAVE1 s
JOIN MASTER1 m ON s.MOVIEID = m.MOVIEID
WHERE m.MOVIE_NAME = ? AND m.THEATRE_NAME = ? AND m.TIMINGS = ?;

- Retrieves seat availability and ticket prices for a specific movie, theatre, and show time.
- **JOIN Explanation:**
  - The query joins two tables:  
    - `MASTER1` → Stores show metadata such as movie name, theatre name, and timings.  
    - `SLAVE1` → Stores seat counts and pricing for each ticket class.  
  - Both tables are linked using the `MOVIEID` column, ensuring that seat information is matched to the correct show details.
- **Parameters:**
  - `MOVIE_NAME`: Name of the movie.
  - `THEATRE_NAME`: Name of the theatre.
  - `TIMINGS`: Show timing.
- **Purpose:** Combines seat availability with pricing so the system can show users complete booking information for a selected show.

---

### 6. **Update Seat Availability**
#### Query:
UPDATE SLAVE1 
SET A_CLASS = ?, B_CLASS = ?, C_CLASS = ?
WHERE MOVIEID = ?;

- Updates the available seat counts for a specific movie show.
- **Parameters:**
  - `A_CLASS`: New number of A-class seats available.
  - `B_CLASS`: New number of B-class seats available.
  - `C_CLASS`: New number of C-class seats available.
  - `MOVIEID`: Unique identifier for the movie show.
- **Purpose:** Ensures that the seat inventory remains accurate after bookings or cancellations.

---

### 7. **Get Booking Details (Admin Only)**
#### Query:
SELECT * FROM BOOK_DETAILS WHERE id = ?;

- Retrieves complete booking information for a given booking ID.
- **Parameters:**
  - `id`: Unique booking ID.
- **Purpose:** Allows admin users to view the details of a specific ticket booking for verification, customer support, or reporting.

---

### 8. **Get Latest Booking ID**
#### Query:
SELECT MAX(id) AS last_booking_id FROM BOOK_DETAILS;

-- Retrieves the latest booking record
- **Parameters:**
  - `id`: Unique identifier of booking records.
- **Purpose:** Allows user to check the latest booking.

---

### 9. **Get All Bookings by User ID**
#### Query:
SELECT * 
FROM BOOK_DETAILS
WHERE user_id = :user_id;

-- Retrieves all booking records associated with a given user.
- **Parameters:**
  - `user_id`: Unique identifier of user.
- **Purpose:** Allows users or admins to view all past and current bookings made by a specific user.





