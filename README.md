# Vehicle Rental System - Database Design & SQL Queries

## 📌 Project Overview

This project is a simplified **Vehicle Rental System** designed to manage users, vehicles, and the booking process. The goal is to demonstrate a robust relational database design, establishing clear relationships between entities and performing complex data retrieval using SQL.

The system handles:

- **User Management:** Tracking Admins and Customers with unique credentials.
- **Vehicle Fleet:** Managing different types of vehicles (cars, bikes, trucks) and their real-time availability.
- **Booking Workflow:** Handling the rental process from start to finish, calculating costs, and maintaining booking history.

---

## 🛠 Database Schema & Relationships

The database is built on three core entities:

1.  **Users:** Stores personal details and roles.
2.  **Vehicles:** Contains technical specifications, pricing, and status.
3.  **Bookings:** The bridge between Users and Vehicles, capturing transaction details.

### Entity Relationships:

- **One-to-Many (User → Bookings):** One user can make multiple bookings over time.
- **Many-to-One (Bookings → Vehicle):** Many booking records can refer to a single specific vehicle (across different time slots).
- **Primary & Foreign Keys:** Each table uses a Primary Key (PK) for uniqueness, while `user_id` and `vehicle_id` act as Foreign Keys (FK) in the Bookings table to maintain referential integrity.

---

## 🎨 Entity Relationship Diagram (ERD)

The database structure and relationships are visually represented in the ERD. You can view the live interactive diagram via the link below:

**🔗 View ERD on DrawSQL:** [Vehicle Rental System Diagram](https://drawsql.app/teams/mahbub-hossen/diagrams/vehicle-rental-system)

---

## SQL Queries & Explanations

Below are the specialized SQL queries implemented to handle business logic requirements.

### 1. Booking Information Retrieval (JOIN)

**Objective:** Fetch detailed booking records including customer and vehicle names.

```sql
SELECT
    b.booking_id,
    u.name AS customer_name,
    v.name AS vehicle_name,
    b.start_date,
    b.end_date,
    b.status
FROM bookings b
INNER JOIN users u ON b.user_id = u.user_id
INNER JOIN vehicles v ON b.vehicle_id = v.vehicle_id;
```

- **Explanation:** I use `INNER JOIN` to combine three tables. It matches rows only where there is a corresponding ID in both tables, ensuring we only see bookings that have valid users and vehicles assigned.

### 2. Identifying Unbooked Vehicles (EXISTS)

**Objective:** Find all vehicles that have never been rented out.

```sql
SELECT *
FROM vehicles v
WHERE NOT EXISTS (
    SELECT 1
    |FROM bookings b
    WHERE b.vehicle_id = v.vehicle_id
);
```

- **Explanation:** The `NOT EXISTS` clause is highly efficient for subqueries. It checks the `bookings` table for each vehicle; if no matching `vehicle_id` is found in the booking records, that vehicle is returned.

### 3. Available Vehicles Filter (WHERE)

**Objective:** List specific vehicle types (e.g., 'car') that are currently available for rent.

```sql
SELECT *
FROM vehicles
WHERE type = 'car' AND status = 'available';
```

- **Explanation:** This uses basic filtering. The `AND` operator ensures that both conditions—the vehicle being a car and its status being available—must be met.

### 4. High-Demand Vehicles (GROUP BY & HAVING)

**Objective:** Identify vehicles that have been booked more than twice.

```sql
SELECT
    v.name AS vehicle_name,
    COUNT(b.booking_id) AS total_bookings
FROM bookings b
INNER JOIN vehicles v ON b.vehicle_id = v.vehicle_id
GROUP BY v.name
HAVING COUNT(b.booking_id) > 2;
```

- **Explanation:** Here, `GROUP BY` aggregates the data by vehicle name. Since we cannot use a `WHERE` clause on aggregated data (like `COUNT`), we use `HAVING` to filter groups that meet the criteria of having more than 2 bookings.

---
