# Peer Tutor Management System

A comprehensive JavaFX-based peer tutoring management application built for an Object-Oriented Programming (OOP) university project. This system enables students to connect as tutors and tutees, manage tutoring sessions, and handle bookings efficiently.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Building and Running](#building-and-running)
- [Usage Guide](#usage-guide)
- [Project Structure](#project-structure)
- [User Roles](#user-roles)
- [Design Patterns](#design-patterns)
- [Technologies Used](#technologies-used)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

PeerTutorMGMT is a full-stack peer tutoring management system designed to streamline the process of connecting tutees (students seeking help) with tutors (students providing expertise). The application features a graphical user interface (GUI) built with JavaFX and uses PostgreSQL for persistent data storage.

This project demonstrates core OOP principles including inheritance, polymorphism, abstraction, encapsulation, and design patterns such as the DAO (Data Access Object) pattern.

## ✨ Features

- **Multi-role Authentication**: Support for three user types (Tutee, Tutor, Admin)
- **User Management**: 
  - Secure login and registration system
  - User profile management with expertise tracking
  - Hour capacity management for tutors
- **Tutoring Session Management**:
  - Tutors can create and manage tutoring sessions
  - Sessions include subject, date/time, and maximum student capacity
  - Real-time session status tracking (Active, Completed, Cancelled)
- **Booking System**:
  - Tutees can request bookings for available sessions
  - Booking status tracking (Pending, Approved, Rejected, Completed)
  - Request timestamp tracking
- **Dashboard Interface**:
  - Personalized dashboard for each user type
  - Real-time data viewing and management
- **CRUD Operations**: Complete Create, Read, Update, Delete functionality for all entities
- **Persistent Data Storage**: PostgreSQL database for reliable data persistence

## 🏗️ Architecture

### Architecture Pattern: Layered Architecture (Model-View-Controller inspired)

The application is organized into distinct layers:

```
┌─────────────────────────────────────────┐
│         GUI Layer (JavaFX)              │
│  - LoginGUI, SignupGUI                  │
│  - MainDashboardGUI                     │
│  - CRUDSessionGUI, CRUDBookingGUI       │
├─────────────────────────────────────────┤
│         Model Layer                     │
│  - User, Tutor, Tutee, Admin            │
│  - TutoringSession, Booking             │
│  - Enums: Role, SessionStatus,          │
│    BookingStatus                        │
├─────────────────────────────────────────┤
│  Database Access Layer (DAO Pattern)    │
│  - DBConnection                         │
│  - DBSelect, DBSelectAll, DBInsert,     │
│    DBUpdate, DBDelete                   │
│  - DBOperations (Interface)             │
└─────────────────────────────────────────┘
```

## 📋 Prerequisites

- **Java Development Kit (JDK)**: Version 11 or higher
- **PostgreSQL**: Version 10 or higher
- **Apache Ant**: For building the project (comes with NetBeans)
- **Apache NetBeans**: IDE (recommended but not required)
- **PostgreSQL JDBC Driver**: Included in project dependencies

## 🚀 Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/OOP-School-Projects/PeerTutorMGMT.git
cd PeerTutorMGMT
```

### Step 2: Set Up the PostgreSQL Database

1. Install and start PostgreSQL service
2. Create a new database for the project:

```sql
CREATE DATABASE peer_tutor_mgmt;
```

3. Connect to the database and create the required tables:

```sql
-- Users table
CREATE TABLE users (
    student_id VARCHAR(20) PRIMARY KEY,
    student_name VARCHAR(100) NOT NULL,
    student_email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(20) NOT NULL, -- 'TUTEE', 'TUTOR', 'ADMIN'
    year_of_study INT NOT NULL,
    expertise VARCHAR(255),
    max_hours_per_week INT
);

-- Tutoring Sessions table
CREATE TABLE tutoring_sessions (
    session_id SERIAL PRIMARY KEY,
    subject VARCHAR(100) NOT NULL,
    tutor_id VARCHAR(20) NOT NULL,
    datetime TIMESTAMP NOT NULL,
    max_students INT NOT NULL,
    status VARCHAR(20) NOT NULL, -- 'ACTIVE', 'COMPLETED', 'CANCELLED'
    FOREIGN KEY (tutor_id) REFERENCES users(student_id)
);

-- Bookings table
CREATE TABLE bookings (
    booking_id SERIAL PRIMARY KEY,
    session_id INT NOT NULL,
    tutee_id VARCHAR(20) NOT NULL,
    status VARCHAR(20) NOT NULL, -- 'PENDING', 'APPROVED', 'REJECTED', 'COMPLETED'
    requested_at TIMESTAMP NOT NULL,
    FOREIGN KEY (session_id) REFERENCES tutoring_sessions(session_id),
    FOREIGN KEY (tutee_id) REFERENCES users(student_id)
);
```

## ⚙️ Configuration

### Database Configuration

1. Open or create `src/database/Config.java`:

```java
package database;

public class Config {
    public static final String DB_Url = "jdbc:postgresql://localhost:5432/peer_tutor_mgmt";
    public static final String DB_Username = "your_postgres_username";
    public static final String DB_Password = "your_postgres_password";
}
```

2. Replace `your_postgres_username` and `your_postgres_password` with your actual PostgreSQL credentials.

**Note**: Currently, database credentials are hardcoded in the Config.java file. For production use, consider implementing a secure configuration system using environment variables or property files.

## 🏗️ Building and Running

### Using Apache Ant (NetBeans)

```bash
# Build the project
ant build

# Run the application
ant run

# Clean build artifacts
ant clean
```

### Using Command Line

```bash
# Compile Java files
javac -d build src/**/*.java

# Run the main application
java -cp build:lib/postgresql-JDBC.jar app.Main
```

## 📖 Usage Guide

### 1. Starting the Application

Launch the application to access the **Login Screen**. Choose one of the following:

#### For New Users - Sign Up
1. Click the "Sign Up" button
2. Enter your details:
   - Student ID
   - Full Name
   - Email Address
   - Password
   - Year of Study
   - Select Role (Tutee/Tutor)
   - Expertise (for Tutors)
   - Maximum hours per week (for Tutors)
3. Click "Register" to create your account

#### For Existing Users - Login
1. Enter your Student ID
2. Enter your Password
3. Click "Login"

### 2. Dashboard Overview

After login, you'll be directed to the **Main Dashboard** with options based on your role.

#### Tutee Dashboard
- **View Available Sessions**: Browse all active tutoring sessions
- **Request Booking**: Book a seat in a session
- **Manage Bookings**: View and track your booking requests
- **My Profile**: Update your information

#### Tutor Dashboard
- **Create Session**: Set up a new tutoring session
- **Manage Sessions**: Edit, view, or cancel your sessions
- **View Bookings**: See student requests for your sessions
- **My Profile**: Update expertise and availability

#### Admin Dashboard
- **User Management**: View all users, manage accounts
- **Session Overview**: Monitor all tutoring sessions
- **Booking Overview**: Track all bookings in the system

### 3. Managing Tutoring Sessions

#### As a Tutor - Creating a Session
1. Navigate to "Create Session"
2. Enter session details:
   - Subject
   - Date and Time
   - Maximum number of students
3. Click "Create"

#### As a Tutor - Managing Sessions
1. View your active sessions
2. Options available:
   - Edit session details
   - Cancel session
   - View enrolled students

### 4. Managing Bookings

#### As a Tutee - Requesting a Booking
1. Browse available sessions
2. Click "Book Session"
3. Your request enters "Pending" status
4. Wait for tutor approval

#### As a Tutor - Managing Booking Requests
1. View pending booking requests
2. Options:
   - **Approve**: Accept the tutee's request
   - **Reject**: Decline the booking request

## 📂 Project Structure

```
PeerTutorMGMT/
├── src/
│   ├── app/
│   │   └── Main.java                 # Application entry point
│   ├── models/
│   │   ├── User.java                 # Abstract base user class
│   │   ├── Tutee.java                # Tutee model (extends User)
│   │   ├── Tutor.java                # Tutor model (extends User)
│   │   ├── Admin.java                # Admin model (extends User)
│   │   ├── TutoringSession.java       # Session data model
│   │   ├── Booking.java              # Booking data model
│   │   ├── Role.java                 # Enum: User roles
│   │   ├── SessionStatus.java        # Enum: Session statuses
│   │   └── BookingStatus.java        # Enum: Booking statuses
│   ├── gui/
│   │   ├── LoginGUI.java             # Login screen
│   │   ├── SignupGUI.java            # User registration screen
│   │   ├── MainDashboardGUI.java     # Main user dashboard
│   │   ├── CRUDSessionGUI.java       # Session management interface
│   │   └── CRUDBookingGUI.java       # Booking management interface
│   └── database/
│       ├── Config.java               # Database configuration
│       ├── DBConnection.java         # Connection handler
│       ├── DBOperations.java         # Interface for DB operations
│       ├── DBOperationsImpl.java      # Implementation of interface
│       ├── DBSelect.java             # SELECT queries
│       ├── DBSelectAll.java          # SELECT all queries
│       ├── DBInsert.java             # INSERT queries
│       ├── DBUpdate.java             # UPDATE queries
│       └── DBDelete.java             # DELETE queries
├── test/                             # Unit tests
├── build.xml                         # Ant build configuration
├── manifest.mf                       # JAR manifest
├── nbproject/                        # NetBeans project configuration
└── README.md                         # This file
```

## 👥 User Roles

### Tutee
- **Definition**: A student seeking tutoring help
- **Capabilities**:
  - View available tutoring sessions
  - Request bookings for sessions
  - Track booking status
  - Manage their profile
  - View tutor expertise and availability

### Tutor
- **Definition**: A student offering tutoring services
- **Capabilities**:
  - Create tutoring sessions with subject and schedule
  - Set session capacity (max students)
  - View and approve/reject booking requests
  - Manage their expertise areas
  - Track teaching hours and commitments

### Admin
- **Definition**: Administrator with elevated privileges
- **Capabilities**:
  - View all users in the system
  - View all tutoring sessions
  - View all bookings
  - Manage user accounts (future enhancement)
  - Generate reports (future enhancement)

## 🎨 Design Patterns

### 1. **DAO (Data Access Object) Pattern**
Located in `database/` package, this pattern abstracts database operations:
- `DBOperations.java` - Interface defining database operations
- `DBOperationsImpl.java` - Factory for creating specific DB operations
- `DBSelect.java`, `DBUpdate.java`, etc. - Specific operation implementations

Benefits:
- Decouples business logic from database access
- Enables easy testing and database switching
- Centralizes database operation logic

### 2. **Inheritance & Polymorphism**
- `User.java` - Abstract base class
- `Tutee.java`, `Tutor.java`, `Admin.java` - Concrete implementations
- Demonstrates IS-A relationships and code reusability

### 3. **Singleton Pattern (Potential)**
- `DBConnection.java` - Could be implemented as singleton for database connection pooling

### 4. **MVC-Inspired Architecture**
- **Models**: User classes and data models
- **Views**: GUI classes (LoginGUI, MainDashboardGUI, etc.)
- **Control Logic**: Database operations and business logic

## 🛠️ Technologies Used

| Component | Technology | Version |
|-----------|-----------|---------|
| **Language** | Java | 11+ |
| **GUI Framework** | JavaFX | - |
| **Database** | PostgreSQL | 10+ |
| **JDBC Driver** | PostgreSQL JDBC | Latest |
| **Build Tool** | Apache Ant | - |
| **IDE** | NetBeans | Recommended |

## 🔧 Troubleshooting

### Issue: "Connection Failed!" at startup

**Cause**: PostgreSQL connection error

**Solutions**:
1. Verify PostgreSQL is running:
   ```bash
   # On Linux/Mac
   sudo systemctl status postgresql
   
   # On Windows
   # Check Services or PostgreSQL Application
   ```
2. Check database credentials in `Config.java`
3. Ensure database exists:
   ```sql
   \l  -- List all databases (in psql)
   ```
4. Verify JDBC driver is in classpath

### Issue: "Driver loading failed"

**Cause**: PostgreSQL JDBC driver not in classpath

**Solutions**:
1. Add PostgreSQL JDBC jar to project libraries in NetBeans
2. Or compile with explicit classpath:
   ```bash
   javac -cp lib/postgresql-JDBC.jar src/**/*.java
   ```

### Issue: "Access denied for user"

**Cause**: Database credentials are incorrect

**Solutions**:
1. Verify username and password in `Config.java`
2. Reset PostgreSQL password if forgotten
3. Create a new PostgreSQL user with proper permissions:
   ```sql
   CREATE USER tutor_app WITH PASSWORD 'secure_password';
   ALTER USER tutor_app WITH CREATEDB;
   ```

### Issue: GUI doesn't display properly

**Cause**: JavaFX not properly installed or configured

**Solutions**:
1. Download JavaFX SDK from [javafx.io](https://gluonhq.com/products/javafx/)
2. Add JavaFX to VM options in NetBeans:
   - Project Properties → Run → VM Options
   - Add: `--module-path /path/to/javafx-sdk/lib --add-modules javafx.controls,javafx.fxml`

## 🤝 Contributing

This is an educational project. Contributions for learning purposes are welcome! To contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/YourFeature`)
3. Commit changes (`git commit -m 'Add YourFeature'`)
4. Push to the branch (`git push origin feature/YourFeature`)
5. Open a Pull Request

### Areas for Enhancement
- [ ] Implement environment variable-based configuration
- [ ] Add unit tests with JUnit
- [ ] Implement connection pooling
- [ ] Add session review/rating system
- [ ] Implement tutor availability calendar
- [ ] Add email notifications
- [ ] Create admin report generation
- [ ] Add session rescheduling functionality

## 📄 License

This project is provided as-is for educational purposes. Refer to the LICENSE file for detailed terms.
