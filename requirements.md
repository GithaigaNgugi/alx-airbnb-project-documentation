## **1. User Authentication**

### API Endpoints

* `POST /users/` – Register a new user
* `GET /users/{user_id}/` – Retrieve a specific user
* `PUT /users/{user_id}/` – Update a user profile
* `DELETE /users/{user_id}/` – Delete a user
* `POST /auth/login/` – Authenticate user and return JWT token

### Input Specifications

**User Registration**

```json
{
  "first_name": "Brian",
  "last_name": "Ngugi",
  "email": "brian@example.com",
  "password": "StrongPass123!",
  "phone_number": "+254712345678",
  "role": "guest"
}
```

**Login**

```json
{
  "email": "brian@example.com",
  "password": "StrongPass123!"
}
```

### Output Specifications

**Registration Success**

```json
{
  "user_id": "uuid",
  "message": "User created successfully."
}
```

**Login Success**

```json
{
  "access_token": "jwt_token",
  "user_id": "uuid",
  "role": "guest"
}
```

### Validation Rules

* Email must be unique and follow a valid format
* Password must be at least 8 characters, contain an uppercase letter, lowercase letter, and number
* Role must be one of: `guest`, `host`, `admin`
* Phone number is optional but must match international format if provided

### Performance Criteria

* Authentication actions should return within 300 milliseconds
* Passwords must be securely hashed (e.g., using bcrypt or Argon2)
* JWT tokens should be signed and validated securely

---

## **2. Property Management**

### API Endpoints

* `GET /properties/` – List all properties
* `POST /properties/` – Create a property listing
* `GET /properties/{property_id}/` – Retrieve a specific property
* `PUT /properties/{property_id}/` – Update a listing
* `DELETE /properties/{property_id}/` – Delete a listing

### Input Specifications

**Create Property**

```json
{
  "host_id": "uuid",
  "name": "Beach Villa",
  "description": "Oceanfront view with 3 bedrooms",
  "location": "Diani, Kenya",
  "pricepernight": 100.00
}
```

### Output Specifications

```json
{
  "property_id": "uuid",
  "message": "Property created successfully"
}
```

### Validation Rules

* `host_id` must reference an existing user with the role of `host`
* `pricepernight` must be a positive decimal value
* `name`, `description`, and `location` are required
* Duplicate listings with the same name and host in the same location should be prevented

### Performance Criteria

* Listing retrieval must support pagination and filters
* Searchable fields (like `location` and `pricepernight`) should be indexed
* Description should support full-text search for efficient query results

---

## **3. Booking System**

### API Endpoints

* `GET /bookings/` – List all bookings
* `POST /bookings/` – Create a booking
* `GET /bookings/{booking_id}/` – Retrieve a booking
* `PUT /bookings/{booking_id}/` – Update a booking
* `DELETE /bookings/{booking_id}/` – Cancel or delete a booking

### Input Specifications

**Create Booking**

```json
{
  "property_id": "uuid",
  "user_id": "uuid",
  "start_date": "2025-08-01",
  "end_date": "2025-08-05"
}
```

### Output Specifications

```json
{
  "booking_id": "uuid",
  "total_price": 400.00,
  "status": "pending",
  "message": "Booking request created"
}
```

### Validation Rules

* `property_id` must exist and be available for the given dates
* `start_date` must be before `end_date`
* Bookings must not overlap with existing confirmed bookings
* `total_price` should equal the nightly rate multiplied by the number of nights
* `status` must be one of: `pending`, `confirmed`, `canceled`, `completed`

### Performance Criteria

* Booking creation should complete within 500 milliseconds
* Use Redis for caching property availability
* Ensure fast date-based queries with BTREE indexing on `start_date` and `end_date`

---
