# MediCareBook: Doctor Appointment Booking System

MediCareBook is a full-stack application for managing doctor appointments, featuring distinct roles for Users (Patients), Doctors, and Administrators. This project is built using the MERN stack (MongoDB, Express.js, React, Node.js).

## Features

*   **User Authentication:** Secure registration, login, and authentication using JWT.
*   **User (Patient) Management:** Standard users can browse approved doctors, book appointments, view their appointments, and apply to become a doctor.
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
    *   Run the server:
        ```bash
        node index.js
        # or if you add a start script to package.json: npm start
        ```
    *   You should see confirmation messages like "Connected to MongoDB" and "Server is running on port ...".

2.  **Start the Frontend:**
    *   In a **new** terminal window, navigate to the `frontend` directory.
    *   Run the development server:
        ```bash
        npm run dev
        ```
    *   Vite will provide a local development server URL (e.g., `http://localhost:5173`). Open this URL in your browser.

## Demonstration Example Flow (Sai Charan as Admin, Vinay as Doctor, Kumar as Patient)

This section walks through a typical flow demonstrating the different roles and key functionalities of the MediCareBook application.

1.  **Initial Admin Registration (by Sai Charan):**
    *   The system database is initially empty of admin users.
    *   **Sai Charan** visits the public `/register` page.
    *   Sai Charan fills in their details and **selects the "Admin" radio button**.
    *   The backend's registration logic detects that no admin user exists and allows Sai Charan to be registered as the very first administrator account (`type: 'admin'`).
    *   A success message confirms the registration.
    *   **Subsequent Admin Registration Attempts:** If anyone else (like Vinay or Kumar) later tries to register on the public `/register` page and selects "Admin", the backend will detect the existing admin (Sai Charan) and reject their attempt with an error message.

2.  **Standard User Registration (by Vinay and Kumar):**
    *   **Vinay** visits the public `/register` page, fills in his details, and **selects the "User" radio button**. This registers him as a standard user (`type: 'user'`, `isdoctor: false`).
    *   **Kumar** visits the public `/register` page, fills in his details, and **selects the "User" radio button**. This registers him as a standard user (`type: 'user'`, `isdoctor: false`).

3.  **Logging In as Admin (Sai Charan):**
    *   **Sai Charan** logs in using the `/login` page with their Admin credentials.
    *   Based on their `type: 'admin'`, they are redirected to the Admin Dashboard (`/adminhome`).
    *   From here, Sai Charan can view all users (including Vinay and Kumar), manage doctor applications, view all appointments, and even create *additional* admin accounts if necessary (this is a protected function within the admin panel, not available publicly).

4.  **Doctor Application (by Vinay):**
    *   **Vinay** logs in as a standard user.
    *   He is redirected to the User Dashboard (`/userhome`).
    *   He clicks the "Apply doctor" option in the sidebar.
    *   He fills out the doctor application form (specialization, experience, fees, timings, etc.) and submits it.
    *   The backend creates a pending doctor profile for Vinay and sends a notification to the admin (Sai Charan).

5.  **Admin Approves Doctor (Sai Charan):**
    *   **Sai Charan** logs in to the Admin Dashboard (`/adminhome`).
    *   He sees a notification about Vinay's doctor application.
    *   He navigates to the "Doctors" section in the Admin sidebar, finds Vinay's pending application, and clicks "Approve".
    *   The backend updates Vinay's doctor profile status to 'approved' and updates Vinay's user account (`isdoctor` flag becomes `true`). A notification is sent to Vinay.

6.  **Logging In as Doctor (Vinay):**
    *   **Vinay** logs out and logs back in.
    *   His user account now has `isdoctor: true`. He is redirected to the User Dashboard (`/userhome`), but the interface adapts to show doctor-specific options.
    *   The "Apply doctor" option is now hidden. The "Appointments" section becomes his primary tool for managing appointments booked *with him*.

7.  **Booking an Appointment (Kumar with Dr. Vinay):**
    *   **Kumar** logs in to his User Dashboard (`/userhome`).
    *   He navigates to the "Home" section which lists approved doctors. He sees **Dr. Vinay** in the list.
    *   Kumar clicks "Book Now" on Dr. Vinay's card.
    *   In the booking modal, Kumar selects a date/time and uploads a medical document.
    *   He submits the booking request.
    *   The backend creates a new appointment record (linking Kumar and Dr. Vinay), sets the status to 'pending', stores the document, and sends a notification to Dr. Vinay.

8.  **Doctor Manages Appointment (Dr. Vinay):**
    *   **Dr. Vinay** receives a notification about Kumar's new appointment request.
    *   He navigates to his "Appointments" section in the User Dashboard.
    *   He sees the pending appointment from Kumar, including the document link.
    *   He can download and review the document.
    *   He clicks "Approve" or "Reject". The backend updates the appointment status and sends a notification back to Kumar.

9.  **Patient Views Updated Appointment (Kumar):**
    *   **Kumar** receives a notification about his appointment status change.
    *   He navigates to his "Appointments" section.
    *   He sees the appointment status updated to 'approved' (or 'rejected').

10. **Admin Oversight (Sai Charan):**
    *   **Sai Charan**, the Admin, can log in at any time to their Admin Dashboard (`/adminhome`) to view all users (including Kumar and Dr. Vinay), all doctor profiles (including Vinay's approved status), and *all* appointments in the system, including the approved one between Kumar and Dr. Vinay.

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
