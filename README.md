# MediCareBook: Doctor Appointment Booking System

MediCareBook is a full-stack application for managing doctor appointments, featuring distinct roles for Users, Doctors, and Administrators. This project is built using the MERN stack (MongoDB, Express.js, React, Node.js).

## Features

*   **User Authentication:** Secure registration, login, and authentication using JWT.
*   **User Management:** Standard users can browse approved doctors, book appointments, view their appointments, and apply to become a doctor.
*   **Doctor Management:** Doctors (after admin approval) can view appointments booked with them, approve or reject appointment requests, and download patient documents.
*   **Admin Panel:** Administrators have a dedicated dashboard to:
    *   View all registered users.
    *   View and manage doctor applications (approve/reject).
    *   View all appointments across the system.
    *   Create new administrator accounts (restricted function, accessible only by logged-in admins).
*   **Notifications:** Users and Doctors receive notifications for relevant events (e.g., doctor application status, new appointment requests, appointment status updates).
*   **File Uploads:** Patients can upload documents (like medical history) when booking an appointment.

## Technologies Used

**Backend (Node.js/Express):**

*   **Express.js:** Web application framework.
*   **Mongoose:** MongoDB object modeling for Node.js.
*   **MongoDB Atlas:** Cloud-hosted NoSQL database.
*   **JWT (jsonwebtoken):** For creating and verifying authentication tokens.
*   **Bcryptjs:** For secure password hashing.
*   **Multer:** Middleware for handling file uploads.
*   **CORS:** Middleware to enable Cross-Origin Resource Sharing.
*   **dotenv:** For managing environment variables.

**Frontend (React/Vite):**

*   **React:** JavaScript library for building user interfaces.
*   **React Router DOM (v6):** For routing and navigation.
*   **Vite:** Fast development build tool.
*   **Axios:** Promise-based HTTP client for API requests.
*   **React Bootstrap:** Frontend UI library.
*   **Ant Design:** UI library used for specific components (like Tabs, Badge, TimePicker, message).
*   **@mui/icons-material:** Material Design icons.
*   **dayjs:** For date/time handling (used with Ant Design Picker).

## Getting Started

Follow these steps to set up and run the project locally.

### Prerequisites

*   Node.js (v18 or higher recommended)
*   npm or yarn package manager
*   A MongoDB database (MongoDB Atlas is used in the configuration)

### Setup

1.  **Clone the repository:**
    ```bash
    git clone <repository_url>
    cd <project_directory>
    ```

2.  **Backend Setup:**
    *   Navigate to the `backend` directory: `cd backend`
    *   Install dependencies: `npm install` (or `yarn install`)
    *   Create a `.env` file in the `backend` directory.
    *   Add your MongoDB Atlas connection string and a JWT secret key to the `.env` file:
        ```env
        MONGO_DB=YOUR_MONGODB_ATLAS_CONNECTION_STRING_HERE
        JWT_KEY=your_super_secret_jwt_key_replace_this_with_a_long_random_string
        PORT=5000 # Default backend port
        ```
        *   **MongoDB Atlas Connection String:** Get this from your Atlas dashboard. It will look something like `mongodb+srv://<username>:<password>@cluster0.abcde.mongodb.net/mydatabase?retryWrites=true&w=majority`. Replace `<username>` and `<password>` with your database user's credentials.
        *   **MongoDB Atlas Network Access:** Go to your Atlas cluster's Security -> Network Access settings and **add your current IP address** or **Allow Access from Anywhere (0.0.0.0/0 - Use only for testing/development)**. This is **crucial** for your backend to connect.

    *   **Create the `uploads` directory:** Manually create an empty folder named `uploads` in the `backend` directory. This is where Multer will store uploaded files.

3.  **Frontend Setup:**
    *   Navigate to the `frontend` directory: `cd ../frontend`
    *   Install dependencies: `npm install` (or `yarn install`)
    *   Create a `.env` file in the `frontend` directory.
    *   Add the backend API URL to the `.env` file:
        ```env
        VITE_API_URL=http://localhost:5000/api
        ```
        *Ensure the port `5000` matches the `PORT` set in your backend `.env` file.*

### Running the Application

1.  **Start the Backend:**
    *   In your terminal, navigate to the `backend` directory.
    *   Run: `node index.js` (or `npm start`)
    *   Keep this terminal window open.

2.  **Start the Frontend:**
    *   In a **new** terminal window, navigate to the `frontend` directory.
    *   Run: `npm run dev`
    *   Vite will provide a local development server URL (e.g., `http://localhost:5173`). Open this URL in your browser.

The application should now be running and accessible in your browser.

## User Flows & Roles

### General Access (Public)

*   **Home (`/`):** Landing page.
*   **Register (`/register`):**
    *   Create a new account. The form shows both "User" and "Admin" options.
    *   **Initial Admin Registration:** The **very first user** to register can successfully select "Admin" and create the administrator account.
    *   **Subsequent Admin Registration Attempts:** If an admin account already exists in the database, any subsequent attempt by a user to register and select "Admin" will be **blocked by the backend**, and an error message will be displayed on the frontend indicating that an administrator already exists.
    *   **Standard User Registration:** Registering as "User" is always permitted via this form (assuming the email is unique).
    *   Successful registration (either user or the first admin) redirects to the Login page.
*   **Login (`/login`):** Log in with existing credentials (User, Doctor, or Admin). Redirects to `/userhome` or `/adminhome` based on user type.

### User Role (`type: 'user'`)

*   Login with a standard user account.
*   Redirected to `/userhome`.
*   **Home:** Browse and book appointments with approved doctors. Upload medical documents during booking.
*   **Appointments:** View a list of your booked appointments and their status.
*   **Apply doctor:** If not already a doctor (`isdoctor: false`), submit an application to become a doctor. Admin notification is sent.
*   **Notifications:** View and manage notifications (unread/seen) from the header icon.
*   **Logout:** Log out from the sidebar.

### Doctor Role (`isdoctor: true`)

*   First, register as a User and apply via the "Apply doctor" form. An Admin must then approve your application. Your user account's `isdoctor` flag will be set to `true`.
*   Login with your user account.
*   Redirected to `/userhome`. The User Home interface adapts slightly for doctors.
*   **Appointments:** View a list of appointments booked *with you* by patients. See patient details, appointment date, status, and attached documents.
*   **Manage Appointments:** For pending appointments, approve or reject the request. Patient receives a notification.
*   **Download Documents:** Download documents uploaded by patients for their appointments.
*   **Apply doctor:** This option is hidden if `isdoctor` is `true`.
*   **Notifications:** View and manage notifications (e.g., new appointment requests, admin messages) from the header icon.
*   **Logout:** Log out from the sidebar.

### Admin Role (`type: 'admin'`)

*   **The first admin account is created via the public registration page by selecting "Admin".**
*   Login with an admin account.
*   Redirected to `/adminhome`.
*   **Appointments:** View a list of *all* appointments in the system.
*   **Doctors:** View a list of all doctor applications and their status. Approve or reject pending applications. Approving a doctor updates their status and sets the linked user's `isdoctor` flag to `true`.
*   **Users:** View a list of all user accounts (standard users, doctors, admins).
*   **Create Admin:** Use the form to create a *new* user account with the `type` automatically set to `admin`. This route (`/api/admin/registeradmin`) is protected and requires the logged-in user to be an admin.
*   **Notifications:** View and manage notifications (e.g., new doctor applications) from the header icon.
*   **Logout:** Log out from the sidebar.

## File Structure Overview

*   `backend/`: Contains the server-side code.
    *   `controllers/`: Handles request logic and interacts with models.
    *   `routes/`: Defines API endpoints and maps them to controllers and middleware.
    *   `schemas/`: Mongoose models defining MongoDB collection structures.
    *   `middlewares/`: Middleware functions (like authentication).
    *   `config/`: Database connection setup.
    *   `index.js`: Entry point, server setup, middleware, and route mounting.
    *   `uploads/`: Directory for storing uploaded files (ensure this exists).
*   `frontend/`: Contains the client-side React application.
    *   `src/components/`: React components organized by area (`admin`, `common`, `user`).
    *   `src/utils/`: Utility functions (like `axiosConfig` for API calls with token).
    *   `src/App.jsx`: Main React component setting up routing.
    *   `src/main.jsx`: React application entry point.
    *   `public/`: Static assets.

## Potential Improvements

*   Implement password reset functionality.
*   Add email notifications for key events.
*   Improve UI/UX and responsiveness.
*   Add doctor scheduling and availability management features (beyond just timings).
*   Implement an actual booking process within the doctor's available slots.
*   Add user/doctor/admin profile pages for viewing/editing details (with appropriate restrictions).
*   Implement more robust input validation (frontend and backend).
*   Containerize the application using Docker.
*   Add comprehensive testing.
*   Write deployment instructions for platforms like Heroku, Render, or Vercel/AWS.

---
