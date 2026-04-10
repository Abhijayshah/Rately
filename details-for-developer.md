# Project Documentation: Rately

**Version**: 1.0.0  
**Last Updated**: 2026-04-11  

---

## TABLE OF CONTENTS
1. [Project Overview](#1-project-overview)
2. [Tech Stack](#2-tech-stack)
3. [File Structure](#3-file-structure)
4. [Key Components](#4-key-components)
5. [Routing Structure](#5-routing-structure)
6. [API Endpoints](#6-api-endpoints)
7. [Styling System](#7-styling-system)
8. [Environment Variables](#8-environment-variables)
9. [Initial Database Setup](#9-initial-database-setup)
10. [Scripts and Commands](#10-scripts-and-commands)
11. [Dependencies](#11-dependencies)
12. [Deployment Notes](#12-deployment-notes)
13. [Future Sections](#13-future-sections)

---

## 1. PROJECT OVERVIEW
- **Project Name**: Rately
- **Description**: A comprehensive store rating platform designed for users to discover and rate local stores, and for store owners to manage their store's reputation and view analytics.
- **Main Purpose**: To provide a transparent and user-friendly system for store ratings and reviews.
- **Target Audience**: 
  - **Normal Users**: Looking to find and rate stores.
  - **Store Owners**: Looking to manage their store profile and view customer feedback.
  - **System Admins**: Responsible for managing users and stores across the platform.

---

## 2. TECH STACK
- **Frontend**: React (v18.2.0)
- **Styling**: Tailwind CSS (v3.3.6)
- **Backend**: Node.js / Express (v4.18.2)
- **Database**: MongoDB with Mongoose ORM
- **Authentication**: JWT (JSON Web Tokens) with `bcryptjs` for password hashing.
- **State Management**: React Context API (`AuthContext.js`)
- **Build Tools**: Create React App (`react-scripts`)
- **Package Manager**: npm
- **Deployment**: 
  - **Frontend**: Vercel
  - **Backend**: Render (as indicated by the production API URL)

---

## 3. FILE STRUCTURE

```text
Rately/
├── backend/                # Express backend API
│   ├── config/             # Database connection and initialization
│   ├── controllers/        # Business logic for routes
│   ├── middleware/         # Auth and validation middleware
│   ├── models/             # Mongoose schemas (User, Store, Rating)
│   ├── routes/             # API route definitions
│   ├── server.js           # Entry point for the backend
│   └── package.json        # Backend dependencies and scripts
├── frontend/               # React frontend
│   ├── public/             # Static assets and index.html
│   ├── src/                
│   │   ├── components/     # Reusable UI and layout components
│   │   │   ├── auth/       # Authentication-related components (ProtectedRoute)
│   │   │   ├── layout/     # Layout wrappers (Header, Sidebar, Footer)
│   │   │   └── ui/         # Generic UI elements (Button, Input, StarRating)
│   │   ├── contexts/       # React Context providers (AuthContext)
│   │   ├── pages/          # Page-level components organized by role
│   │   │   ├── admin/      # System Admin pages (Analytics, Stores, Users)
│   │   │   ├── auth/       # Login and Register pages
│   │   │   ├── storeOwner/ # Store Owner dashboard
│   │   │   └── user/       # Normal user pages (Ratings, Stores)
│   │   ├── services/       # API service layer (axios configuration)
│   │   ├── App.js          # Main application component with routing
│   │   └── index.js        # Entry point for the frontend
│   ├── tailwind.config.js  # Tailwind CSS configuration
│   └── package.json        # Frontend dependencies and scripts
└── README.md               # Root project overview
```

---

## 4. KEY COMPONENTS

### UI Components (`src/components/ui/`)
- **StarRating.js**: 
  - **Purpose**: Displays and allows users to set a rating using stars.
  - **Props**: `rating`, `onRatingChange`, `editable`.
- **Table.js**: 
  - **Purpose**: A generic table component for displaying lists of users, stores, or ratings.
  - **Props**: `headers`, `data`, `renderRow`.
- **Modal.js**: 
  - **Purpose**: A reusable modal for forms and confirmations.
  - **Props**: `isOpen`, `onClose`, `title`, `children`.

### Auth Components (`src/components/auth/`)
- **ProtectedRoute.js**: 
  - **Purpose**: Protects routes from unauthorized access based on authentication status and user role.
  - **Props**: `children`, `requiredRole`.

### Layout Components (`src/components/layout/`)
- **Layout.js**: 
  - **Purpose**: Provides a consistent structure with a Sidebar and Header for authenticated users.

---

## 5. ROUTING STRUCTURE

### Public Routes
- `/`: Landing page
- `/login`: Login page
- `/register`: Registration page
- `/unauthorized`: Error page for insufficient permissions

### Protected Routes (Requires Authentication)
- `/dashboard`: Main dashboard (content varies by user role)
- `/settings`: User profile and password management

### Role-Based Routes
- **System Admin**:
  - `/admin/users`: User management
  - `/admin/stores`: Store management
  - `/admin/analytics`: Platform-wide analytics
- **Normal User**:
  - `/stores`: Browse and rate stores
  - `/my-ratings`: View personal rating history
- **Store Owner**:
  - `/my-store`: Store-specific analytics and management

---

## 6. API ENDPOINTS

### Auth API (`/api/auth`)
- `POST /register`: Register a new user
- `POST /login`: Authenticate user and receive token
- `POST /logout`: Logout user
- `GET /profile`: Get current user's profile
- `PUT /profile`: Update user's profile information
- `PUT /password`: Update user's password

### Admin API (`/api/admin`)
- `GET /dashboard/stats`: Platform-wide statistics
- `POST /users`: Create a new user (admin only)
- `GET /users`: List all users with pagination/filters
- `GET /users/:id`: Get detailed user information
- `PUT /users/:id`: Update user details
- `DELETE /users/:id`: Remove a user
- `POST /stores`: Create a new store
- `GET /stores`: List all stores for admin

### Store API (`/api/stores`)
- `GET /`: List stores for users
- `GET /:id`: Get specific store details
- `POST /ratings`: Submit a rating for a store
- `GET /user/ratings`: Get current user's ratings
- `GET /owner/dashboard`: Get ratings and analytics for the owner's store

---

## 7. STYLING SYSTEM
- **Methodology**: Utility-first CSS using **Tailwind CSS**.
- **Global Styles**: Defined in `frontend/src/index.css`.
- **Theme**: Default Tailwind palette with custom configurations in `tailwind.config.js`.
- **Responsiveness**: Standard Tailwind breakpoints (`sm`, `md`, `lg`, `xl`).

---

## 8. ENVIRONMENT VARIABLES

### Frontend (`frontend/.env`)
- `REACT_APP_API_URL`: The base URL for the backend API (e.g., `http://localhost:5001/api`).

### Backend (`backend/.env`)
- `PORT`: Port number for the server (default: 5000).
- `MONGODB_URI`: Connection string for the MongoDB database.
- `JWT_SECRET`: Secret key for signing JSON Web Tokens.
- `CLIENT_URL`: URL of the frontend for CORS configuration.
- `NODE_ENV`: Current environment (`development`, `production`).

---

## 9. INITIAL DATABASE SETUP
The backend automatically initializes the database with default accounts if they don't exist. This is useful for testing.

| Role | Email | Password | Purpose |
|------|-------|----------|---------|
| **System Admin** | `admin@rately.com` | `Admin@123` | Platform management |
| **Normal User** | `user@rately.com` | `User@123` | Browsing and rating stores |
| **Store Owner** | `store@rately.com` | `Store@123` | Store analytics and dashboard |

---

## 10. SCRIPTS AND COMMANDS

### Frontend
- `npm start`: Runs the app in development mode at `http://localhost:3000`.
- `npm run build`: Builds the app for production in the `build/` folder.
- `npm test`: Launches the test runner.

### Backend
- `npm start`: Starts the production server.
- `npm run dev`: Starts the server with `nodemon` for auto-restarts on code changes.

---

## 11. DEPENDENCIES

### Major Frontend Dependencies
- `axios`: For making HTTP requests to the backend.
- `react-router-dom`: For client-side routing.
- `react-hook-form`: For form handling and validation.
- `react-toastify`: For displaying notifications.
- `lucide-react`: For iconography.

### Major Backend Dependencies
- `express`: Web framework for the API.
- `mongoose`: ODM for MongoDB.
- `jsonwebtoken`: For auth token handling.
- `bcryptjs`: For password security.
- `express-rate-limit` & `helmet`: For security hardening.

---

## 12. DEPLOYMENT NOTES
- **Build Process**: Frontend is built using `react-scripts build`. Backend is a standard Node.js application.
- **Frontend Hosting**: Vercel (configured via `vercel.json`).
- **Backend Hosting**: Render (as configured in the production environment).
- **Environment Setup**: Ensure all required `.env` variables are configured on the hosting platforms.

---

## 13. FUTURE SECTIONS
- [Add new sections as needed by developers]
- TODO: Implement comprehensive unit and integration tests.
- TODO: Add API documentation using Swagger/OpenAPI.
- TODO: Enhance store analytics with more detailed visualizations.
