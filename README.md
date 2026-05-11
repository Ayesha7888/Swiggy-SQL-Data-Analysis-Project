## SWIGGY DATA ANALYSIS PROJECT
## Database: PostgreSQL
## Dataset Size: 50,000+ Records

## Project Overview

This project focuses on analyzing Swiggy restaurant and menu data using SQL in PostgreSQL.  
The objective of this project is to solve real-world business problems related to restaurant performance, pricing, cuisines, ratings, and customer food preferences.

The project demonstrates SQL skills required for Data Analyst roles, including:
- Aggregate Functions
- JOINS
- Subqueries
- Common Table Expressions (CTEs)
- Window Functions
- CASE Statements
- Data Filtering and Sorting

---

## Tools & Technologies Used

- PostgreSQL
- SQL
- Power BI (Optional Dashboard)
- GitHub

---

## Dataset Information

The dataset contains restaurant-level and menu-level information such as:
- Restaurant Name
- City
- Cuisine
- Rating
- Cost Per Person
- Menu Category
- Item Name
- Item Price
- Veg / Non-Veg Type

---

## Business Problems Solved

### Restaurant & Rating Analysis
1. Count of restaurants with rating greater than 4.5
   
SELECT COUNT(DISTINCT restaurant_no) AS high_rated_restaurants
FROM swiggy
WHERE rating > 4.5;

<img width="302" height="102" alt="rating greater than 4 5" src="https://github.com/user-attachments/assets/44020030-5349-4794-80c2-e9b2f00586c3" />

2. City with highest number of restaurants

SELECT city,
       COUNT(DISTINCT restaurant_name) AS restaurant_count
FROM swiggy
GROUP BY city
ORDER BY restaurant_count DESC
LIMIT 1;

<img width="422" height="112" alt="city has the highest number of restaurants" src="https://github.com/user-attachments/assets/4c6f4cdd-9f3f-438e-b2fa-23812ccf43e2" />

3. Restaurants containing "Pizza" in their name

SELECT COUNT(DISTINCT restaurant_name) AS pizza_restaurant_count
FROM swiggy
WHERE restaurant_name ILIKE '%Pizza%';

<img width="467" height="226" alt="Restaurants containing Pizza in their name" src="https://github.com/user-attachments/assets/a3a66d7f-89fe-467e-998b-42f0c1e6c44d" />


4. Average restaurant rating by city

SELECT 
    city,
    ROUND(AVG(rating), 2) AS average_rating
FROM swiggy
GROUP BY city
ORDER BY average_rating DESC;
<img width="457" height="131" alt="Average restaurant rating by city" src="https://github.com/user-attachments/assets/1ca067ae-1b3a-475d-afa3-52b585967253" />


5. Restaurant ranking within each city

 WITH rating_rank_by_city AS ( SELECT restaurant_name, city, rating, 
DENSE_RANK() OVER (PARTITION BY city ORDER BY rating DESC) AS rating_rank 
FROM swiggy
)
SELECT restaurant_name, city, rating, rating_rank 
FROM rating_rank_by_city 
WHERE rating_rank = 1;
<img width="732" height="382" alt="Restaurant ranking within each city" src="https://github.com/user-attachments/assets/c9609d9c-81e9-4ddc-99e7-04ef76eb75dc" />



### Cuisine & Food Analysis
6. Most common cuisine

SELECT 
    cuisine,
    COUNT(*) AS cuisine_count
FROM swiggy
GROUP BY cuisine
ORDER BY cuisine_count DESC
LIMIT 1;
<img width="412" height="92" alt="Most common cuisine" src="https://github.com/user-attachments/assets/7b1438a1-cf9b-48f5-940b-3dbac351a7b8" />


7. Restaurants with 100% vegetarian menu

SELECT 
    restaurant_name,
    ROUND(
        COUNT(CASE WHEN veg_or_nonveg = 'Veg' THEN 1 END) * 100.0 / COUNT(*),
        2
    ) AS vegetarian_percentage
FROM swiggy
GROUP BY restaurant_name
HAVING COUNT(CASE WHEN veg_or_nonveg = 'Veg' THEN 1 END) * 100.0 / COUNT(*) = 100
ORDER BY restaurant_name;
<img width="412" height="92" alt="Most common cuisine" src="https://github.com/user-attachments/assets/48b5e502-b6f8-4904-805f-7c0d8051bac5" />



8. Restaurant with highest percentage of non-vegetarian items
   
SELECT 
    restaurant_name,
    ROUND(
        COUNT(CASE WHEN veg_or_nonveg = 'Non-veg' THEN 1 END) * 100.0 / COUNT(*),
        2
    ) AS non_vegetarian_percentage
FROM swiggy
GROUP BY restaurant_name
ORDER BY non_vegetarian_percentage DESC
LIMIT 1;
<img width="526" height="112" alt="Restaurant with highest percentage of non-vegetarian items" src="https://github.com/user-attachments/assets/016abdec-de36-4c32-96d0-509867f91b3d" />



### Pricing Analysis
9. Highest-priced recommended item for each restaurant

SELECT 
    restaurant_name,
    MAX(price) AS highest_price
FROM swiggy
WHERE menu_category = 'Recommended'
GROUP BY restaurant_name
ORDER BY highest_price DESC;
<img width="542" height="300" alt="Highest-priced recommended item" src="https://github.com/user-attachments/assets/ffa523da-d472-4344-b3f2-96d084c4c065" />


10. Top 5 most expensive non-Indian restaurants

SELECT DISTINCT
    restaurant_name,
    cuisine,
    cost_per_person
FROM swiggy
WHERE cuisine <> 'Indian'
ORDER BY cost_per_person DESC
LIMIT 5;
<img width="402" height="215" alt="Top 5 most expensive non-Indian restaurants" src="https://github.com/user-attachments/assets/99d4d886-7c09-4dc4-804f-a6c6e82cb702" />


11. Restaurant with lowest average item price

SELECT 
    restaurant_name,
    ROUND(AVG(price), 2) AS average_item_price
FROM swiggy
GROUP BY restaurant_name
ORDER BY average_item_price ASC
LIMIT 1;
<img width="557" height="362" alt="Restaurant with lowest average item price" src="https://github.com/user-attachments/assets/d8228a0a-c72d-4f49-b211-e7eaa58f2470" />


12. Most expensive and least expensive cities for dining

WITH city_expense AS
(
    SELECT 
        city,
        MAX(cost_per_person) AS max_cost,
        MIN(cost_per_person) AS min_cost
    FROM swiggy
    GROUP BY city
)

SELECT *
FROM city_expense
ORDER BY max_cost DESC;

<img width="447" height="161" alt="Most expensive and least expensive cities for dining" src="https://github.com/user-attachments/assets/73ac3981-9589-4023-abe2-35197bd386d8" />


### Menu Analysis
13. Restaurant offering most "Main Course" items

SELECT 
    restaurant_name,
    COUNT(item) AS total_main_course_items
FROM swiggy
WHERE menu_category = 'Main Course'
GROUP BY restaurant_name
ORDER BY total_main_course_items DESC
LIMIT 1;
<img width="365" height="95" alt="Restaurant offering most Main Course items" src="https://github.com/user-attachments/assets/ebb4c768-4a45-4878-a8a6-e2538fc057f5" />

14. Top 5 restaurants with highest menu categories

SELECT 
    restaurant_name,
    COUNT(DISTINCT menu_category) AS total_categories
FROM swiggy
GROUP BY restaurant_name
ORDER BY total_categories DESC
LIMIT 5;

<img width="392" height="237" alt="Top 5 restaurants with highest menu categories" src="https://github.com/user-attachments/assets/060f977f-2d83-477d-95e2-c94e20a8667a" />

### Advanced SQL Analysis
15. Restaurants with average cost higher than overall average

SELECT 
    restaurant_name,
    ROUND(AVG(cost_per_person), 2) AS avg_cost
FROM swiggy
GROUP BY restaurant_name
HAVING AVG(cost_per_person) >
(
    SELECT AVG(cost_per_person)
    FROM swiggy
)
ORDER BY avg_cost DESC;
<img width="437" height="337" alt="Restaurants with average cost higher than overall average" src="https://github.com/user-attachments/assets/106d3485-921b-44fa-afa8-08965d5ac0c8" />


16. Restaurants sharing same name across different cities

SELECT DISTINCT
    t1.restaurant_name,
    t1.city AS city_1,
    t2.city AS city_2
FROM swiggy t1
JOIN swiggy t2
    ON t1.restaurant_name = t2.restaurant_name
   AND t1.city <> t2.city
ORDER BY restaurant_name;
<img width="637" height="330" alt="Restaurants sharing same name across different cities" src="https://github.com/user-attachments/assets/f88076c7-64ef-414e-bc00-ef4d4cd97f87" />

