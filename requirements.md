# 📄 Backend Requirement Specifications – Airbnb Clone

This document outlines the technical and functional requirements for three key backend features of the Airbnb Clone project: **User Authentication**, **Property Management**, and **Booking System**. Each section includes API endpoints, input/output specifications, validation rules, and performance considerations.

---

## 1. 🔐 User Authentication

### 📌 Description
Handles secure user registration, login, and token-based authentication using JWT. Supports both guests and hosts.

### 📥 Endpoints

| Method | Endpoint           | Description            |
|--------|--------------------|------------------------|
| POST   | `/api/auth/register/` | Register a new user (guest or host) |
| POST   | `/api/auth/login/`    | Log in with email and password |
| GET    | `/api/auth/profile/`  | Fetch authenticated user profile |
| PATCH  | `/api/auth/profile/`  | Update user profile information |

### ✅ Input Fields

| Field       | Type     | Required | Validation Rules                     |
|-------------|----------|----------|--------------------------------------|
| email       | string   | ✅       | Must be a valid email address        |
| password    | string   | ✅       | Minimum 8 characters, alphanumeric   |
| role        | enum     | ✅       | `guest` or `host`                    |
| name        | string   | ✅       | Not empty                            |
| profile_img | file/url | optional | Valid image format                   |

### 🔁 Output (Success Response – Login)

```json
{
  "token": "JWT_TOKEN",
  "user": {
    "id": 1,
    "name": "Jane Doe",
    "email": "jane@example.com",
    "role": "host"
  }
}
```

### 🛡 Security & Performance
- JWT expiration and refresh
- Rate limiting for login attempts
- Password hashing (e.g., PBKDF2 or bcrypt)
- Token revocation on logout

---

## 2. 🏡 Property Management

### 📌 Description
Allows hosts to list, update, and delete properties they own, including photos, pricing, and availability.

### 📥 Endpoints

| Method | Endpoint               | Description                |
|--------|------------------------|----------------------------|
| POST   | `/api/properties/`     | Create a new property      |
| GET    | `/api/properties/`     | Retrieve all properties (with filters) |
| GET    | `/api/properties/<id>/`| Retrieve single property    |
| PUT    | `/api/properties/<id>/`| Update property details     |
| DELETE | `/api/properties/<id>/`| Delete a property listing   |

### ✅ Input Fields

| Field         | Type     | Required | Validation Rules                 |
|---------------|----------|----------|----------------------------------|
| title         | string   | ✅       | Max 150 characters               |
| description   | text     | ✅       | Not empty                        |
| location      | string   | ✅       | City, country, or address        |
| price         | decimal  | ✅       | Must be > 0                      |
| amenities     | array    | optional | e.g., ["wifi", "pool"]          |
| availability  | date[]   | ✅       | Array of available dates         |
| image         | file/url | ✅       | Valid image file or URL         |

### 🔁 Output (Success Response – Create)

```json
{
  "id": 10,
  "title": "Beachside Bungalow",
  "host_id": 1,
  "price": 250.00,
  "location": "Cape Town, South Africa"
}
```

### 📈 Performance & Validation
- Paginate property listings
- Indexing on location and price
- Validate that only hosts can create or update listings

---

## 3. 📆 Booking System

### 📌 Description
Enables guests to reserve available properties and hosts to view/manage bookings. Prevents overlapping bookings and supports booking status changes.

### 📥 Endpoints

| Method | Endpoint                     | Description                  |
|--------|------------------------------|------------------------------|
| POST   | `/api/bookings/`             | Create a new booking         |
| GET    | `/api/bookings/`             | Retrieve bookings (user/host)|
| GET    | `/api/bookings/<id>/`        | View booking details         |
| PATCH  | `/api/bookings/<id>/cancel/` | Cancel a booking             |

### ✅ Input Fields

| Field       | Type    | Required | Validation Rules               |
|-------------|---------|----------|--------------------------------|
| property_id | int     | ✅       | Must exist and be available    |
| start_date  | date    | ✅       | Cannot be in the past          |
| end_date    | date    | ✅       | Must be after start_date       |
| guests      | integer | ✅       | Must be within property limits |

### 🔁 Output (Success Response – Booking)

```json
{
  "id": 101,
  "property_id": 10,
  "user_id": 7,
  "start_date": "2025-07-10",
  "end_date": "2025-07-15",
  "status": "confirmed"
}
```

### 📊 Rules & Optimization
- Prevent double bookings (check overlapping dates)
- Status: `pending`, `confirmed`, `cancelled`
- Allow only guest or host to cancel
- Cache common queries (e.g., upcoming bookings)

---

## 🛠 Notes

- All endpoints follow REST principles and return standard HTTP status codes.
- Authentication is required for all POST, PUT, DELETE routes.
- Validation is handled using Django REST Framework serializers.
- File uploads (e.g., property images) are stored in cloud-compatible file storage (e.g., AWS S3).

---

> ✅ This document is part of the `alx-airbnb-project-documentation` repository.
