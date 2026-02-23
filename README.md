# 🏕️ DevCamper API

A comprehensive **REST API** for managing developer bootcamps, courses, reviews, and users — built with Node.js, Express, and MongoDB.

---

## 📖 Overview

DevCamper is a backend API that powers a bootcamp directory platform. It allows publishers to list and manage bootcamps and their associated courses, users to leave reviews, and admins to oversee all platform data. The API includes geo-location support, photo uploads, JWT authentication, and a full security middleware stack.

---

## ✨ Features

- **Bootcamp Management** — Create, read, update, and delete bootcamps with geo-coded locations
- **Course Management** — Associate courses with bootcamps; auto-calculate average cost per bootcamp
- **Reviews & Ratings** — Users can submit reviews; average ratings are auto-calculated
- **Authentication & Authorization** — JWT-based auth with cookie support and role-based access control
- **User Roles** — Three roles: `user`, `publisher`, and `admin` with different permissions
- **Photo Uploads** — Upload bootcamp cover photos stored to the filesystem
- **Geo-location** — Addresses are geocoded into GeoJSON coordinates via MapQuest
- **Advanced Querying** — Filter, select fields, sort, and paginate any resource
- **Password Reset** — Forgot/reset password flow via email token
- **Security** — Helmet headers, XSS protection, rate limiting, NoSQL injection sanitization, and HTTP param pollution prevention

---

## 🗂️ Project Structure

```
devcamper_project/
├── server.js                 # Entry point — Express app setup & middleware registration
├── seeder.js                 # CLI tool to import or destroy seed data
├── config/
│   ├── config.env            # Environment variables
│   └── db.js                 # MongoDB connection
├── controllers/
│   ├── auth.js               # Register, login, logout, password reset
│   ├── bootcamps.js          # Bootcamp CRUD + photo upload
│   ├── courses.js            # Course CRUD
│   ├── reviews.js            # Review CRUD
│   └── users.js              # Admin user management
├── models/
│   ├── Bootcamp.js           # Bootcamp schema with geocoding & slug hooks
│   ├── Course.js             # Course schema with average cost calculation
│   ├── Review.js             # Review schema with average rating calculation
│   └── User.js               # User schema with bcrypt & JWT methods
├── routes/
│   ├── auth.js               # /api/v1/auth
│   ├── bootcamps.js          # /api/v1/bootcamps
│   ├── courses.js            # /api/v1/courses
│   ├── reviews.js            # /api/v1/reviews
│   └── users.js              # /api/v1/users
├── middleware/
│   ├── auth.js               # protect (JWT verify) + authorize (role check)
│   ├── advancedResults.js    # Filtering, sorting, pagination middleware
│   ├── async.js              # Async/await error wrapper
│   ├── error.js              # Global error handler
│   └── logger.js             # Custom request logger
├── utils/
│   ├── errorResponse.js      # Custom ErrorResponse class
│   ├── geocoder.js           # node-geocoder setup
│   └── sendEmail.js          # Nodemailer email utility
├── _data/
│   ├── bootcamps.json        # Bootcamp seed data
│   ├── courses.json          # Course seed data
│   ├── reviews.json          # Review seed data
│   └── users.json            # User seed data
└── public/uploads/           # Uploaded bootcamp photos
```

---

## 🔌 API Endpoints

### Authentication — `/api/v1/auth`

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| `POST` | `/register` | Public | Register a new user |
| `POST` | `/login` | Public | Login and receive JWT |
| `GET` | `/logout` | Public | Clear auth cookie |
| `GET` | `/me` | Private | Get logged-in user profile |
| `PUT` | `/updateDetails` | Private | Update name and email |
| `PUT` | `/updatePassword` | Private | Update password |
| `POST` | `/forgotPassword` | Public | Send password reset email |
| `PUT` | `/resetpassword/:resettoken` | Public | Reset password via token |

### Bootcamps — `/api/v1/bootcamps`

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| `GET` | `/` | Public | Get all bootcamps (with filtering, sorting, pagination) |
| `GET` | `/:id` | Public | Get a single bootcamp |
| `POST` | `/` | Publisher / Admin | Create a new bootcamp |
| `PUT` | `/:id` | Publisher / Admin | Update a bootcamp |
| `DELETE` | `/:id` | Publisher / Admin | Delete a bootcamp |
| `PUT` | `/:id/photo` | Publisher / Admin | Upload bootcamp photo |

### Courses — `/api/v1/courses`

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| `GET` | `/` | Public | Get all courses |
| `GET` | `/:id` | Public | Get a single course |
| `GET` | `/bootcamps/:bootcampId/courses` | Public | Get courses for a bootcamp |
| `POST` | `/bootcamps/:bootcampId/courses` | Publisher / Admin | Add a course to a bootcamp |
| `PUT` | `/:id` | Publisher / Admin | Update a course |
| `DELETE` | `/:id` | Publisher / Admin | Delete a course |

### Reviews — `/api/v1/reviews`

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| `GET` | `/` | Public | Get all reviews |
| `GET` | `/:id` | Public | Get a single review |
| `GET` | `/bootcamps/:bootcampId/reviews` | Public | Get reviews for a bootcamp |
| `POST` | `/bootcamps/:bootcampId/reviews` | User / Admin | Add a review |
| `PUT` | `/:id` | User / Admin | Update a review |
| `DELETE` | `/:id` | User / Admin | Delete a review |

### Users — `/api/v1/users` *(Admin only)*

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| `GET` | `/` | Admin | Get all users |
| `GET` | `/:id` | Admin | Get a single user |
| `POST` | `/` | Admin | Create a user |
| `PUT` | `/:id` | Admin | Update a user |
| `DELETE` | `/:id` | Admin | Delete a user |

---

## 🔐 User Roles & Permissions

| Role | Permissions |
|------|-------------|
| `user` | Browse bootcamps & courses, submit reviews |
| `publisher` | All user permissions + create/manage own bootcamps & courses |
| `admin` | Full access to all resources and users |

---

## 🔍 Advanced Query Parameters

All `GET` list endpoints support the following query parameters:

| Parameter | Example | Description |
|-----------|---------|-------------|
| Field filter | `?averageCost[lte]=10000` | Filter using `gt`, `gte`, `lt`, `lte`, `in` |
| Field select | `?select=name,description` | Return only specific fields |
| Sort | `?sort=name` or `?sort=-createdAt` | Sort ascending or descending |
| Pagination | `?page=2&limit=10` | Page through results |

---

## ⚙️ Tech Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| Node.js | — | Runtime environment |
| Express | ^4.18.1 | Web framework |
| MongoDB + Mongoose | ^6.5.2 | Database & ODM |
| JSON Web Token | ^8.5.1 | Authentication tokens |
| bcryptjs | ^2.4.3 | Password hashing |
| node-geocoder | ^4.2.0 | Address geocoding (MapQuest) |
| Nodemailer | ^6.7.8 | Email delivery |
| express-fileupload | ^1.4.0 | Photo uploads |
| Helmet | ^6.0.0 | Security HTTP headers |
| express-rate-limit | ^6.6.0 | Rate limiting (100 req / 10 min) |
| xss-clean | ^0.1.1 | XSS attack prevention |
| express-mongo-sanitize | ^2.2.0 | NoSQL injection sanitization |
| hpp | ^0.2.3 | HTTP parameter pollution prevention |
| Morgan | ^1.10.0 | HTTP request logger |
| dotenv | ^16.0.1 | Environment variable management |

---

## 🚀 Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v14 or higher
- [MongoDB](https://www.mongodb.com/) running locally or a MongoDB Atlas URI
- A [MapQuest API key](https://developer.mapquest.com/) for geocoding

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/devcamper_project.git
   cd devcamper_project
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**

   Edit `config/config.env` with your own values:
   ```env
   NODE_ENV=development
   PORT=5000

   MONGO_URL=mongodb://localhost:27017/devcamper

   GEOCODER_PROVIDER=mapquest
   GEOCODER_API_KEY=your_mapquest_api_key

   FILE_UPLOAD_PATH=./public/uploads
   MAX_FILE_UPLOAD=1000000

   JWT_SECRET=your_jwt_secret
   JWT_EXPIRE=30d
   JWT_COOKIE_PARSER=30

   SMTP_HOST=smtp.mailtrap.io
   SMTP_PORT=25
   SMTP_EMAIL=your_email
   SMTP_PASSWORD=your_password
   FROM_EMAIL=noreply@devcamper.io
   FROM_NAME=DevCamper
   ```

4. **Seed the database** *(optional)*
   ```bash
   # Import seed data
   node seeder -i

   # Destroy all data
   node seeder -d
   ```

5. **Start the server**
   ```bash
   # Development (with nodemon)
   npm run dev

   # Production
   npm start
   ```

   The API will be available at `http://localhost:5000`

---

## 📜 Available Scripts

| Script | Description |
|--------|-------------|
| `npm run dev` | Start development server with Nodemon (auto-restart) |
| `npm start` | Start production server |
| `node seeder -i` | Import seed data into the database |
| `node seeder -d` | Destroy all data from the database |

---

## 🛡️ Security Measures

- **JWT Authentication** — Tokens sent via Authorization header (`Bearer <token>`) or HTTP-only cookie
- **Password Hashing** — bcrypt with salt rounds of 10
- **Rate Limiting** — Max 100 requests per 10 minutes per IP
- **Helmet** — Sets secure HTTP response headers
- **XSS-Clean** — Sanitizes user input to prevent Cross-Site Scripting
- **Mongo Sanitize** — Strips `$` operators to prevent NoSQL injection
- **HPP** — Prevents HTTP parameter pollution attacks
- **CORS** — Enabled for cross-origin requests

---

## 📄 License

This project is open source and available under the [ISC License](LICENSE).
