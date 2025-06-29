## Entities and Attributes

1. User

   - **user_id**: Primary Key, UUID, Indexed
   - **first_name**: VARCHAR, NOT NULL
   - **last_name**: VARCHAR, NOT NULL
   - **email**: VARCHAR, UNIQUE, NOT NULL
   - **password_hash**: VARCHAR, NOT NULL
   - **phone_number**: VARCHAR, NULL
   - **role**: ENUM (guest, host, admin), NOT NULL
   - **created_at**: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

2. Location

   - **location_id**: Primary Key, UUID, Indexed
   - **city**: VARCHAR, NOT NULL
   - **state_province**: VARCHAR, NOT NULL
   - **country**: VARCHAR, NOT NULL
   - **postal_code**: VARCHAR, NULL
   - **latitude**: DECIMAL(10,8), NULL
   - **longitude**: DECIMAL(11,8), NULL
   - **created_at**: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

3. Property

   - **property_id**: Primary Key, UUID, Indexed
   - **host_id**: Foreign Key, references User(user_id)
   - **location_id**: Foreign Key, references Location(location_id)
   - **name**: VARCHAR, NOT NULL
   - **description**: TEXT, NOT NULL
   - **pricepernight**: DECIMAL, NOT NULL
   - **created_at**: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP
   - **updated_at**: TIMESTAMP, ON UPDATE CURRENT_TIMESTAMP

4. Booking

   - **booking_id**: Primary Key, UUID, Indexed
   - **property_id**: Foreign Key, references Property(property_id)
   - **user_id**: Foreign Key, references User(user_id)
   - **start_date**: DATE, NOT NULL
   - **end_date**: DATE, NOT NULL
   - **total_price**: DECIMAL, NOT NULL
   - **status**: ENUM (pending, confirmed, canceled), NOT NULL
   - **created_at**: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

5. Payment

   - **payment_id**: Primary Key, UUID, Indexed
   - **booking_id**: Foreign Key, references Booking(booking_id)
   - **amount**: DECIMAL, NOT NULL
   - **payment_date**: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP
   - **payment_method**: ENUM (credit_card, paypal, stripe), NOT NULL

6. Review

   - **review_id**: Primary Key, UUID, Indexed
   - **property_id**: Foreign Key, references Property(property_id)
   - **user_id**: Foreign Key, references User(user_id)
   - **rating**: INTEGER, CHECK: rating >= 1 AND rating <= 5, NOT NULL
   - **comment**: TEXT, NOT NULL
   - **created_at**: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

7. Message

   - **message_id**: Primary Key, UUID, Indexed
   - **sender_id**: Foreign Key, references User(user_id)
   - **recipient_id**: Foreign Key, references User(user_id)
   - **message_body**: TEXT, NOT NULL
   - **sent_at**: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP

## Constraints

1. User Table

   - **Unique** constraint on email.
   - **Non**-null constraints on required fields.

2. Location Table

   - **Non**-null constraints on city, state_province, country.
   - **Unique** constraint on combination of city, state_province, country, postal_code.

3. Property Table

   - **Foreign** key constraint on host_id.
   - **Foreign** key constraint on location_id.
   - **Non**-null constraints on essential attributes.

4. Booking Table

   - **Foreign** key constraints on property_id and user_id.
   - **status** must be one of pending, confirmed, or canceled.

5. Payment Table

   - **Foreign** key constraint on booking_id, ensuring payment is linked to valid bookings.

6. Review Table

   - **Constraints** on rating values (1-5).
   - **Foreign** key constraints on property_id and user_id.

7. Message Table

   - **Foreign** key constraints on sender_id and recipient_id.

8. Indexing

   - **Primary** Keys: Indexed automatically.
   - **Additional** Indexes:
   - **email** in the User table.
   - **location_id** in the Location and Property tables.
   - **property_id** in the Property and Booking tables.
   - **booking_id** in the Booking and Payment tables.
