# Airbnb Clone Backend Requirements

## Detailed Requirement Specifications for Key Features

### 1. User Authentication

#### **API Endpoints**

- **POST /api/auth/register**
  - **Description**: Registers a new user (guest or host).
  - **Input**:
    - `email` (string, required)
    - `password` (string, required, minimum 8 characters)
    - `userType` (string, required, values: "guest" or "host")
  - **Output**:
    - `userId` (UUID)
    - `message` (string, success or error message)
  - **Validation Rules**:
    - `email` must be a valid email address and unique.
    - `password` must be at least 8 characters and include alphanumeric characters.

- **POST /api/auth/login**
  - **Description**: Logs in a user.
  - **Input**:
    - `email` (string, required)
    - `password` (string, required)
  - **Output**:
    - `token` (JWT)
    - `message` (string, success or error message)
  - **Validation Rules**:
    - `email` must match a registered email.
    - `password` must match the stored password for the given email.

- **GET /api/auth/logout**
  - **Description**: Logs out the authenticated user.
  - **Input**:
    - `Authorization` header (JWT token, required)
  - **Output**:
    - `message` (string, success or error message)

#### **Performance Criteria**
- User registration and login processes should complete within 1-2 seconds.
- Ensure high availability by caching commonly requested resources.

---

### 2. Property Management

#### **API Endpoints**

- **POST /api/properties**
  - **Description**: Creates a new property listing.
  - **Input**:
    - `title` (string, required)
    - `description` (string, required)
    - `location` (string, required)
    - `pricePerNight` (float, required)
    - `amenities` (array of strings, optional)
    - `images` (array of file uploads, required)
  - **Output**:
    - `propertyId` (UUID)
    - `message` (string, success or error message)
  - **Validation Rules**:
    - `title`, `description`, and `location` must not be empty.
    - `pricePerNight` must be a positive float value.
    - `images` should have a minimum of 1 image.

- **PUT /api/properties/{propertyId}**
  - **Description**: Updates an existing property listing.
  - **Input**:
    - `title` (string, optional)
    - `description` (string, optional)
    - `location` (string, optional)
    - `pricePerNight` (float, optional)
    - `amenities` (array of strings, optional)
    - `images` (array of file uploads, optional)
  - **Output**:
    - `message` (string, success or error message)
  - **Validation Rules**:
    - At least one property detail must be provided for an update.
    - `pricePerNight` must be a positive value if provided.

- **DELETE /api/properties/{propertyId}**
  - **Description**: Deletes a property listing.
  - **Input**: None
  - **Output**:
    - `message` (string, success or error message)
  - **Validation Rules**:
    - `propertyId` must be valid and belong to the authenticated host.

#### **Performance Criteria**
- Listing creation should take no more than 3-4 seconds, including image uploads.
- Property retrieval should be optimized using indexes and caching to reduce response time to less than 1 second.

---

### 3. Booking System

#### **API Endpoints**

- **POST /api/bookings**
  - **Description**: Creates a new booking for a property.
  - **Input**:
    - `propertyId` (UUID, required)
    - `startDate` (date, required)
    - `endDate` (date, required)
    - `numberOfGuests` (integer, required)
  - **Output**:
    - `bookingId` (UUID)
    - `message` (string, success or error message)
  - **Validation Rules**:
    - `propertyId` must refer to an available property.
    - `startDate` must be before `endDate`.
    - `numberOfGuests` must be a positive integer and should not exceed property capacity.

- **PUT /api/bookings/{bookingId}/cancel**
  - **Description**: Cancels an existing booking.
  - **Input**: None
  - **Output**:
    - `message` (string, success or error message)
  - **Validation Rules**:
    - Booking cancellation must adhere to the host's cancellation policy.
    - `bookingId` must be valid and belong to the authenticated user.

- **GET /api/bookings/{bookingId}**
  - **Description**: Retrieves booking details for a specific booking.
  - **Input**: None
  - **Output**:
    - `propertyId` (UUID)
    - `startDate` (date)
    - `endDate` (date)
    - `numberOfGuests` (integer)
    - `status` (string, e.g., "pending", "confirmed", "cancelled")
  - **Validation Rules**:
    - `bookingId` must be valid and belong to the authenticated user or host.

#### **Performance Criteria**
- Booking creation should complete within 2-3 seconds, including availability checks.
- Use database transactions to ensure data consistency in booking and property availability.
- Bookings should be confirmed or denied based on real-time availability to avoid double bookings.
