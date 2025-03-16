# JSON Data ETL Processing using Talend  

## Project Overview  
This project involves processing **JSON files** containing **Actors, Directors, and Movies** data using **Talend**. The extracted data is loaded into a **MySQL, SQL Server, or Azure Database**, maintaining relationships between **Movies, Writers, Actors, and Categories**.  

The **Talend job** performs the following tasks:
- **Extract JSON data** from multiple files.
- **Transform and normalize the data** to establish relationships between entities.
- **Load the processed data** into a **relational database**.

---

## Project Objectives  
**Load JSON data** into a relational database.  
**Normalize relationships** between **Movies, Writers, Actors, and Categories**.  
**Use `tNormalize`** to separate multiple entries within a single JSON field.  
**Implement an optimized database schema** for efficient querying.  
**Write SQL queries** to extract meaningful insights from the database.  

---


## JSON Data Processing Steps  

### Load JSON Files  
- The **JSON files** for **Actors, Directors, and Movies** are loaded into Talend.  
- The component **`tFileInputJSON`** is used to read the JSON structure.


---

### Transform & Normalize Data  
- **`tMap`** is used to map and transform fields from JSON into structured columns.  
- **`tNormalize`** is applied to split multi-value fields like:
  - **Movies → Writers**
  - **Movies → Actors**
  - **Movies → Categories**  
- Relationships between movies and other entities are **maintained using foreign keys**.

 ![image](https://github.com/user-attachments/assets/12f5dfbb-1411-4b69-9713-5e877adfa6e6)

  ![image](https://github.com/user-attachments/assets/4668b5ff-f4e9-476c-9825-c8140a3d981f)

  ![image](https://github.com/user-attachments/assets/9c4da9fe-e05f-4001-b502-7b448c270ab9)

  ![image](https://github.com/user-attachments/assets/0c6e6c2c-e2f3-48f4-92a8-a07b47b42e6e)


---

### Load Data into MySQL / SQL Server / Azure DB  
- The **processed data** is loaded into database tables using **`tDBOutput`**.  
- **Referential integrity** is maintained between tables.  

---

## Database Schema  

The following tables store the transformed JSON data:

### **Movies Table**
| Column Name  | Data Type  | Description |
|-------------|-----------|-------------|
| `movie_id`  | INT (PK)  | Unique Movie ID |
| `title`     | VARCHAR   | Movie Title |
| `release_year` | INT    | Year of Release |

---

### **Actors Table**
| Column Name | Data Type | Description |
|------------|----------|-------------|
| `actor_id` | INT (PK) | Unique Actor ID |
| `actor_name` | VARCHAR | Actor Name |

---

### **Writers Table**
| Column Name | Data Type | Description |
|------------|----------|-------------|
| `writer_id` | INT (PK) | Unique Writer ID |
| `writer_name` | VARCHAR | Writer Name |

---

### **Categories Table**
| Column Name | Data Type | Description |
|------------|----------|-------------|
| `category_id` | INT (PK) | Unique Category ID |
| `category_name` | VARCHAR | Genre of the Movie |

---

### **Movie_Actors Table**
| Column Name | Data Type | Description |
|------------|----------|-------------|
| `movie_id` | INT (FK) | References Movies Table |
| `actor_id` | INT (FK) | References Actors Table |

---

### **Movie_Writers Table**
| Column Name | Data Type | Description |
|------------|----------|-------------|
| `movie_id` | INT (FK) | References Movies Table |
| `writer_id` | INT (FK) | References Writers Table |

---

### **Movie_Categories Table**
| Column Name | Data Type | Description |
|------------|----------|-------------|
| `movie_id` | INT (FK) | References Movies Table |
| `category_id` | INT (FK) | References Categories Table |

---

##SQL Queries  

### **Show Category-wise Movies with More Than 4 Writers & 3 Actors**
```sql
SELECT 
    c.category_name, 
    m.title, 
    COUNT(DISTINCT mw.writer_id) AS writer_count, 
    COUNT(DISTINCT ma.actor_id) AS actor_count
FROM Movies m
JOIN Movie_Writers mw ON m.movie_id = mw.movie_id
JOIN Movie_Actors ma ON m.movie_id = ma.movie_id
JOIN Movie_Categories mc ON m.movie_id = mc.movie_id
JOIN Categories c ON mc.category_id = c.category_id
GROUP BY c.category_name, m.title
HAVING COUNT(DISTINCT mw.writer_id) > 4 AND COUNT(DISTINCT ma.actor_id) > 3;
```

### **List Year-Wise Movie Titles**
```sql
SELECT release_year, GROUP_CONCAT(title ORDER BY title ASC SEPARATOR ', ') AS movie_titles
FROM Movies
GROUP BY release_year
ORDER BY release_year ASC;
```
