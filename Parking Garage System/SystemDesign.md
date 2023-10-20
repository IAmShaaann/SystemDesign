# System Design - Parking Garage System.

# How will you design a parking garage system?

## Requirements →

- Reserve a parking spot and get a ticket for that spot.
- Need to make payment for the parking spot.
- System needs to have a higher consistency (No 2 people should be able to book the same parking spot)
- 3 types of vehicles (Compact, Regular and Large)
- Flat rates based upon time, but different rates based upon different types of parking spots.

## Public Endpoints →

1. /reserve

   Parameters - garage_id, start_time, end_time, vehicle_type

   Returns - ( spot_id , reservation_id )

2. /payment

   Parameters - reservation_id

   Note - Payments will be handled by a Third party library (Stripe, Razorpay)

3. /cancel

   Parameters - reservation_id

## Internal Endpoints →

1. /calculate_payment

   Parameters - reservation_id

2. /free_spots

   Parameters - garage_id, vehicle_type, booking_time

   Note - Smaller vehicles can fit into larger spots for if its a small vehicle allocation of the large spot should be allowed.

3. /allocate_spots

   Parameters - garage_id, vehicle_type, booking_time

4. /create_account

   Parameters - email, password, first_name, last_name.

   Note - We can also add some hashing and single sign in feature like google and others.

5. /login

   Parameters - email, password

## Datatypes →

**Reservation** -

- id - Primary Key, Auto Increment
- garage_id - Foreign Key
- spot_id - Foreign Key
- start_time - timestamp
- end_time - timestamp
- users - Foreign Key
- payment_made - Boolean

**Garage** -

- id - Primary Key, Auto increment
- zipcode - varchar
- pricing_compact - decimal
- pricing_regular - decimal
- pricing_large - decimal

**Spot** -

- id - Primary Key, Auto Increment
- garage_id - Foreign Key
- vehicle_type - enum(Compact/Regular/Large)
- availability_status - enum

**User** -

- id - Primary Key, Auto Increment
- email - varchar
- password - varchar (Encrypted with SHA - 256)
- first_name - varchar, nullable
- last_name - varchar, nullable

**Vehicle** -

- id - Primary key , Auto increment
- user_id - Foreign key
- type - enum(Compact/Regular/Large)
- license - varchar

## High Level Design →

![Screenshot 2023-10-04 at 11.13.50 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/4fd32f6d-2f23-4952-97e4-9d87cf957351/be8785ce-07b8-49ed-bdf7-49b1d8ee4a3f/Screenshot_2023-10-04_at_11.13.50_PM.png)

## Followup Questions -

1. What is Race condition?

   A race condition is when 2 or more components in a program are fighting for the same resources at the same time. So they clash.

2. What is ENUM and what are the tradeoffs of using ENUM in relational database?

   enum is a special data type that can hold predefined values in the database.

   Pros -

   1. More readable and understandable - like instead of using numeric codes for gender, you can predefine the values to male or female.
   2. Data Integrity - Because the data is predefined it ensures data integrity.
   3. Storage Efficiency - enums are stored in integer format making it more storage efficient that normal strings.

   Cons -

   1. Portability - enums are not supported by all the database systems.
   2. Maintainability - Adding, Removing and updating values can be very challenging.
   3. Limited Functionality - enums have very limited functionality, you could use reference table and store even more metadata.

3. What is the difference between Read Replicas and Write Replicas?

   Read Replicas -

   **Purpose:** Read replicas are primarily used for improving read performance in database systems.

   **Read Operations**: Read replicas can process read-intensive queries such as SELECT statements.

   **Write Operations**: Read replicas can only process read operation like SELECT not write operation like INSERT, UPDATE, DELETE

   **Latency**: Read replicas can reduce read query latency because they allow applications to read data from the replica closest to them.

   **Data Consistency**: Read replicas are eventually consistent with the primary database. This means there might be a slight delay in data replication, and applications may read slightly stale data.

   Write Replicas -

   **Data Consistency**: Write replicas are generally kept in sync with the primary database, ensuring that data consistency is maintained across all replicas.

4. Strong Consistency VS Eventual Consistency -

   Strong Consistency- In a system having strong consistency, every read ensures that is returns most recent write and the data is never stale. Which ensure all the nodes in the system will have the latest data.

   Eventual Consistency- In a system having eventual consistency, every read ensures that it returns some data it might be stale though. reducing the latency as it does not waits for global synchronisation of data.
