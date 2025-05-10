# Requirements Specification — Airbnb Clone Backend

This document outlines the **technical** and **functional** requirements for three core backend features of the Airbnb Clone project:

- User Authentication
- Property Management
- Booking System

## 1. User Authentication

### Functional Requirements

- Users should be able to register as either Guest or Host.
- Users can log in via email/password or through OAuth (Google/Facebook).
- Passwords must be securely stored (hashed).
- JWT tokens are used to manage sessions.

### API Endpoints

| Method | Endpoint | Description |
| ------ | -------- | ----------- |
| POST | `/api/auth/register` | Register new user |
| POST | `/api/auth/login` | Log in user |
| GET | `/api/auth/profile` | Get user profile |
| PUT | `/api/auth/profile` | Update profile |

### Input/Output Specification

#### `/api/auth/register`

**Input**

```json
{
  "email": "user@example.com",
  "password": "Password123!",
  "role": "guest"
}
```

**Output**

```json
{
  "message": "User registered successfully",
  "access_token": "<jwt_token>"
}
```

### Validation Rules

* Email must be valid format.
* Password minimum 8 characters, with at least 1 number and 1 uppercase letter.
* Role is either `guest` or `host`.

### Performance Criteria

* Registration/login response < 500ms under normal load.
* JWT tokens expire after 24 hours.

## 2. Property Management

### Functional Requirements

* Hosts can add, edit, or delete property listings.
* Guests can view/search properties.
* Each listing contains title, description, price, location, amenities, availability, and images.

### API Endpoints

| Method | Endpoint              | Description             |
| ------ | --------------------- | ----------------------- |
| POST   | `/api/properties`     | Create property listing |
| GET    | `/api/properties`     | Get list of properties  |
| GET    | `/api/properties/:id` | Get specific property   |
| PUT    | `/api/properties/:id` | Update property         |
| DELETE | `/api/properties/:id` | Delete property         |

### Input/Output Specification

#### `/api/properties`

**Input**

```json
{
  "title": "Cozy Apartment",
  "description": "2-bedroom apartment in downtown",
  "location": "Casablanca",
  "price_per_night": 80,
  "amenities": ["WiFi", "Pool"],
  "availability": {
    "start_date": "2025-06-01",
    "end_date": "2025-09-01"
  }
}
```

**Output**

```json
{
  "message": "Property created successfully",
  "property_id": "1234"
}
```

### Validation Rules

* Title is required (max 150 characters).
* Price must be a positive integer.
* Availability dates must be valid and `end_date > start_date`.

### Performance Criteria

* Search API returns results in < 1s for up to 1000 properties.
* Support pagination (default 10 per page).

## 3. Booking System

### Functional Requirements

* Guests can book properties by selecting available dates.
* Prevent double booking (validate overlapping dates).
* Bookings have statuses: pending, confirmed, cancelled, completed.

### API Endpoints

| Method | Endpoint                   | Description         |
| ------ | -------------------------- | ------------------- |
| POST   | `/api/bookings`            | Create new booking  |
| GET    | `/api/bookings`            | Get user's bookings |
| PUT    | `/api/bookings/:id/cancel` | Cancel booking      |

### Input/Output Specification

#### `/api/bookings`

**Input**

```json
{
  "property_id": "1234",
  "check_in": "2025-06-15",
  "check_out": "2025-06-20"
}
```

**Output**

```json
{
  "message": "Booking created successfully",
  "booking_id": "5678",
  "status": "pending"
}
```

### Validation Rules

* Check-in/check-out must be valid dates.
* Booking dates must not overlap existing bookings.
* Property must exist and be available.

### Performance Criteria

* Booking creation should validate and respond within < 700ms.
* Database constraints should enforce date conflict prevention.

## Notes

* All endpoints should return proper HTTP status codes: `200`, `201`, `400`, `401`, `404`, `409`.
