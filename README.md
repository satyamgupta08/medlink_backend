# MedLink: API & Frontend Routes Documentation

MedLink is a hospital appointment and bed booking web platform that allows users to search for nearby hospitals, check bed availability, and book appointments with doctors. It is built using React (frontend), Spring Boot (backend), and MySQL, developed by **Piyush Kumar, Sairam Sharan, Anadi Mehta, Satyam Gupta, Sweta Kumari, Vivek Tripathi, Sanchita, and Mayank**, students at **IIIT Lucknow**.

---

## Table of Contents

- [Backend API Endpoints](#backend-api-endpoints)
- [Frontend Routes](#frontend-routes)
- [Authentication Flow](#authentication-flow)
- [Data Models](#data-models)

---

## Backend API Endpoints

**Base URL:** `http://localhost:4000`

### Authentication

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| `POST` | `/register` | Register a new user account. Sends OTP email for verification. | `{ "firstName", "lastName", "email", "password" }` | `{ "statusCode": 200, "message": "..." }` |
| `PUT` | `/verify-account` | Verify account with OTP sent to email. | Query params: `email`, `otp` | `{ "token": "<JWT>", "statusCode": 200 }` |
| `POST` | `/login` | Login with credentials. Returns JWT token. | `{ "email", "password" }` | `{ "token": "<JWT>", "statusCode": 200 }` |

### Hospitals

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| `GET` | `/hospitals/{location}` | Get hospitals by city/location. | N/A | `[ { "id", "name", "address", "location", "contactInfo", "beds" } ]` |
| `POST` | `/hospitals/upload` | Bulk upload hospitals (admin utility). | `[ { "name", "address", "location", "contactInfo", "beds" } ]` | Saved hospitals list |
| `GET` | `/hospitals/id/{id}` | Get hospital by ID. | N/A | `{ "id", "name", "address", "location", "contactInfo", "beds" }` |

### Appointments

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| `POST` | `/appointment` | Create a new appointment. | `{ "patientId", "fullName", "email", "phone", "date", "time", "message" }` | Saved appointment object |
| `GET` | `/appointment/{id}` | Get all appointments for a user by user ID. | N/A | `[ { "id", "patientId", "fullName", "email", "phone", "date", "time", "message" } ]` |

### Contact

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| `POST` | `/contact` | Submit a contact form message. | `{ "fullName", "email", "message" }` | Saved contact object |

---

## Frontend Routes

**Dev URL:** `http://localhost:3000`

| Route | Page | Component | Auth Required | Description |
|-------|------|-----------|---------------|-------------|
| `/` | Home | `HomePage` | No | Landing page with emergency hotline, hero, stats, departments grid, doctor cards, trust features, and how-it-works |
| `/hospital` | Find Beds | `HospitalPage` | No | Hospital search with filters and live bed availability ticker |
| `/appointments` | Book Appointment | `AppointmentsPage` | Yes (recommended) | Appointment booking form with department, doctor, date, and time selection |
| `/patient` | My Records | `PatientPage` | Yes | Patient dashboard with upcoming/past appointments, health metrics, and lab reports |
| `/about` | About | `AboutPage` | No | About MedLink: mission, vision, team (IIIT Lucknow), and values |
| `/contact` | Contact | `ContactPage` | No | Contact form, FAQ, and emergency numbers |
| `/login` | Sign In | `LoginPage` | No | Email/password login form |
| `/signup` | Sign Up | `SignupPage` | No | Two-step signup: register then OTP verification |
| `/privacy` | Privacy Policy | `PrivacyPage` | No | Privacy policy with data handling, storage, and rights info |
| `/terms` | Terms of Service | `TermsPage` | No | Terms of service, acceptable use, and medical disclaimer |

---

## Authentication Flow

1. **Register** - User submits name, email, password to `POST /register` and the backend sends OTP to email
2. **Verify OTP** - User enters 6-digit OTP via `PUT /verify-account?email=...&otp=...` and receives a JWT token
3. **Login** - User submits email, password to `POST /login` and receives a JWT token
4. **JWT Token** - Stored in `localStorage` as `logintoken`. Decoded client-side to extract `sub` (email) and `id` (user ID).
5. **Logout** - Token removed from localStorage, user state cleared.

---

## Data Models

### UserModel
```json
{
  "id": "long",
  "email": "string",
  "password": "string (hashed)",
  "firstName": "string",
  "lastName": "string",
  "otp": "string",
  "active": "boolean",
  "otpGeneratedTime": "LocalDateTime"
}
```

### HospitalModel
```json
{
  "id": "long",
  "name": "string",
  "address": "string",
  "location": "string (city)",
  "contactInfo": "string",
  "beds": "int (total bed count)"
}
```

### PatientInfo (Appointment)
```json
{
  "id": "long",
  "patientId": "string (user ID)",
  "fullName": "string",
  "email": "string",
  "phone": "string",
  "date": "string (YYYY-MM-DD)",
  "time": "string (HH:MM)",
  "message": "string (Department: X | Doctor: Y | Symptoms: Z)"
}
```

### ContactModel
```json
{
  "id": "long",
  "email": "string",
  "fullName": "string",
  "message": "string"
}
```

---

## CORS Configuration

The backend allows all origins (`*`) with methods `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`.

---

## Running Locally

### Frontend (React)
```bash
cd MedLink
npm install
npm start        # Development at http://localhost:3000
npm run build    # Production build in build/
```

### Backend (Spring Boot)
```bash
cd medlink_backend/medlink
./mvnw spring-boot:run   # Starts at http://localhost:4000
```

> **Note:** The backend requires a MySQL database (configured via `application.properties`) and Gmail SMTP credentials for OTP emails.
