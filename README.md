<div align="center">

# Voting System

### A modern, secure JavaFX desktop platform for electronic election management, voter participation, and administrative oversight.

[![Java](https://img.shields.io/badge/Java-17-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://openjdk.org/)
[![JavaFX](https://img.shields.io/badge/JavaFX-17.0.6-0078D7?style=for-the-badge&logo=java&logoColor=white)](https://openjfx.io/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0+-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![BCrypt](https://img.shields.io/badge/Security-BCrypt-5B5EA6?style=for-the-badge&logo=auth0&logoColor=white)](https://www.mindrot.org/)
[![Maven](https://img.shields.io/badge/Maven-3.8+-C71A36?style=for-the-badge&logo=apache-maven&logoColor=white)](https://maven.apache.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-22C55E?style=for-the-badge)](LICENSE)
[![Build](https://img.shields.io/badge/Build-Passing-22C55E?style=for-the-badge)]()
[![Status](https://img.shields.io/badge/Status-Active-22C55E?style=for-the-badge)]()

Voting System delivers a tamper-resistant, role-based electronic ballot platform featuring election scheduling, candidate management, real-time analytics, automated OTP password resets, and duplicate-vote prevention.

</div>

---

## Table of Contents

- [Overview](#overview)
- [Highlights](#highlights)
- [Features](#features)
  - [Admin Features](#admin-features)
  - [Voter / User Features](#voter--user-features)
- [System Architecture](#system-architecture)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Database Schema](#database-schema)
  - [Core Tables](#core-tables)
  - [Entity Relationship Diagram](#entity-relationship-diagram)
- [Security & Integrity](#security--integrity)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [1. Clone Repository](#1-clone-the-repository)
  - [2. Database Setup](#2-database-setup)
  - [3. Install Dependencies](#3-install-dependencies)
- [Configuration](#configuration)
  - [Database Connection](#database-connection)
  - [Email & SMTP Configuration](#email--smtp-configuration)
  - [Runtime Application Settings](#runtime-application-settings)
- [Running the Application](#running-the-application)
- [Default User Credentials](#default-user-credentials)
- [Application Workflows](#application-workflows)
  - [Voter Workflow](#voter-workflow)
  - [Administrator Workflow](#administrator-workflow)
- [Future Improvements](#future-improvements)
- [Learning Outcomes](#learning-outcomes)
- [Contributing](#contributing)
- [Author](#author)
- [Support](#support)
- [License](#license)
- [Acknowledgements](#acknowledgements)

---

## Overview

**Voting System** is a full-featured desktop application engineered in JavaFX and backed by MySQL. Designed for institutional elections, student unions, corporate boards, and community organizations, it ensures high ballot integrity, transparent outcome reporting, and strict role-based access control.

The system features two synchronized user environments:

| Interface | Target Audience | Primary Responsibilities |
| --- | --- | --- |
| **Admin Panel** | Election Commissioners & Admins | Election lifecycle management, candidate registration, voter account audits, real-time vote aggregation, analytics reporting, and system settings. |
| **Voter Portal** | Registered Voters & Members | Viewing active and scheduled ballots, inspecting candidate profiles and manifestos, casting single verified votes, tracking personal voting history, and profile management. |

Both interfaces communicate through a unified data access layer, enforcing cryptographic validation, transactional ballot recording, and unique voting constraints.

---

## Highlights

- **Tamper-Resistant Ballot Engine**: SQL-level unique constraints (`UNIQUE(election_id, user_id)`) guarantee that each eligible voter can cast at most one vote per election.
- **Role-Based Access Control (RBAC)**: Distinct views and navigation pipelines for `admin` and `user` roles.
- **End-to-End Election Lifecycle**: Automated and manual status transitions (`pending` -> `active` -> `completed` -> `cancelled`) with start and end datetime boundaries.
- **Candidate & Manifesto Showcase**: Candidate profiles with positions, biographical profiles, and photo URLs.
- **Live Visual Analytics & Reports**: Visual reports with turnout statistics, percentage breakdowns, and winner declarations.
- **Two-Way Password Recovery via OTP**: Secure one-time password generation sent to registered emails with expiration timestamps.
- **Configurable Application Parameters**: Dynamic settings table for runtime adjustments to SMTP servers, theme preferences, IP blocking, and registration toggles.
- **BCrypt Cryptographic Security**: Industry-standard salted password hashing for all user accounts.

---

## Features

### Admin Features

| Functional Area | Capabilities |
| --- | --- |
| **Admin Dashboard** | Live KPI summary cards (Total Users, Active Elections, Completed Elections, Total Votes Cast) and quick navigation hubs. |
| **User Management** | Create, edit, activate, deactivate, or delete user accounts; update voter details; assign or revoke administrator privileges; filter user records. |
| **Election Management** | Schedule new elections with precise start/end datetime pickers, titles, descriptions, and operational status controls (`pending`, `active`, `completed`, `cancelled`). |
| **Candidate Management** | Add and link candidates to specific elections; configure candidate names, campaign positions, biographies, and image URLs; manage candidate rosters. |
| **Reports & Analytics** | Detailed analytical reports; visualize voting turnout percentages; examine candidate vote tallies; export and print outcome summaries. |
| **Election Results** | Real-time vote count aggregation; automatic winner identification; percentage calculations per candidate. |
| **System Settings** | Manage database connection strings, toggle new user registrations, enable email notifications, configure IP blocking, update SMTP server credentials, and change UI themes. |

### Voter / User Features

| Functional Area | Capabilities |
| --- | --- |
| **Secure Authentication** | User sign-up with unique email and username verification; secure login; session initialization; automatic role dispatching. |
| **Password Recovery** | 6-digit OTP verification delivered via SMTP; dedicated password reset screen with real-time validation. |
| **Voter Dashboard** | Personal voting dashboard highlighting active elections, upcoming ballots, voter status, and recent activity logs. |
| **Election Catalog** | Browse open, upcoming, and past elections; search elections by keywords; filter by status. |
| **Ballot Casting** | Inspect candidate positions and campaign promises; cast a confidential vote; receive instant confirmation while enforcing single-vote rules. |
| **Results Viewer** | View completed election results with winner announcements and transparent vote count summaries once voting closes. |
| **Profile Management** | Update full name and contact information; review personal voting history; change account passwords securely. |

---

## System Architecture

Voting System follows a clean, modular Model-View-Controller (MVC) design pattern:

```text
+-------------------------------------------------------------------------+
|                               Presentation Layer                         |
|             JavaFX 17 FXML Views, CSS Layouts, Dialog Windows           |
|  (login-view, admin-dashboard, user-dashboard, reports-view, vote-view)  |
+------------------------------------+------------------------------------+
                                     |
                                     v
+-------------------------------------------------------------------------+
|                               Controller Layer                          |
|         Event Handling, Form Validation, UI Routing, State Binding      |
|    (AdminDashboardController, ManageElectionsController, VoteController) |
+--------------------+-------------------------------+--------------------+
                     |                               |
                     v                               v
+-----------------------------------+  +----------------------------------+
|            Model Layer            |  |          Utility & Security      |
|    (User, Election, Candidate)    |  |  (DatabaseConnection, PasswordHash|
|    Domain Data & Properties       |  |   EmailService with JavaMail)    |
+--------------------+--------------+  +-----------------+----------------+
                     |                                   |
                     +-----------------+-----------------+
                                       |
                                       v
+-------------------------------------------------------------------------+
|                                Data Layer                               |
|                  JDBC PreparedStatements & Transaction Engine           |
+--------------------------------------+----------------------------------+
                                       |
                                       v
+-------------------------------------------------------------------------+
|                               MySQL Database                            |
|          users, elections, candidates, votes, otp, application_settings |
+-------------------------------------------------------------------------+
```

---

## Technology Stack

| Technology | Version | Purpose |
| --- | --- | --- |
| **Java** | 17 LTS | Core application language |
| **JavaFX Controls** | 17.0.6 | Desktop UI components and layouts |
| **JavaFX FXML** | 17.0.6 | Declarative XML-based UI definitions |
| **MySQL Server** | 8.0+ | Relational database management system |
| **MySQL Connector/J** | 8.0.28 | Type 4 JDBC driver for MySQL connectivity |
| **jBCrypt** | 0.4 | Password hashing and salt verification |
| **JavaMail (javax.mail)** | 1.6.2 | SMTP email delivery for OTP verification |
| **Apache Maven** | 3.8+ | Dependency management and build automation |
| **JavaFX Maven Plugin** | 0.0.8 | Maven execution plugin for JavaFX runtimes |
| **JUnit Jupiter** | 5.10.2 | Unit and integration testing framework |

---

## Project Structure

```text
voting-system/
|-- pom.xml                               # Maven project configuration and dependencies
|-- database.sql                          # Core schema definition and seed data
|-- vote.sql                              # Extended database schema and demo records
|-- application.properties                # Externalized runtime connection properties
|-- mvnw / mvnw.cmd                       # Maven wrapper scripts
`-- src/
    `-- main/
        |-- java/com/example/vote/
        |   |-- VoteApplication.java       # Main JavaFX application entry point
        |   |-- controller/               # MVC Controllers
        |   |   |-- AdminDashboardController.java
        |   |   |-- CandidateFormController.java
        |   |   |-- ElectionFormController.java
        |   |   |-- ElectionResultsController.java
        |   |   |-- ForgotPasswordController.java
        |   |   |-- LoginController.java
        |   |   |-- ManageCandidatesController.java
        |   |   |-- ManageElectionsController.java
        |   |   |-- ManageUsersController.java
        |   |   |-- ReportsController.java
        |   |   |-- ResetPasswordController.java
        |   |   |-- SettingsController.java
        |   |   |-- SignupController.java
        |   |   |-- UserDashboardController.java
        |   |   |-- UserElectionsController.java
        |   |   |-- UserFormController.java
        |   |   |-- UserProfileController.java
        |   |   `-- VoteController.java
        |   |-- model/                    # Domain Entity Models
        |   |   |-- Candidate.java
        |   |   |-- Election.java
        |   |   `-- User.java
        |   `-- util/                     # Utilities & Service Helpers
        |       |-- DatabaseConnection.java
        |       |-- EmailService.java
        |       `-- PasswordHash.java
        `-- resources/com/example/vote/   # FXML View Layouts
            |-- admin-dashboard.fxml
            |-- candidate-form-view.fxml
            |-- election-form-view.fxml
            |-- election-results-view.fxml
            |-- forgot-password-view.fxml
            |-- login-view.fxml
            |-- manage-candidates-view.fxml
            |-- manage-elections-view.fxml
            |-- manage-users-view.fxml
            |-- reports-view.fxml
            |-- reset-password-view.fxml
            |-- settings-view.fxml
            |-- signup-view.fxml
            |-- user-dashboard.fxml
            |-- user-elections-view.fxml
            |-- user-form-view.fxml
            |-- user-profile-view.fxml
            `-- vote-view.fxml
```

---

## Database Schema

The database backend is structured to maintain strict relational integrity, prevent double voting, and audit election timelines.

### Core Tables

| Table | Primary Key | Foreign Keys / Constraints | Description |
| --- | --- | --- | --- |
| `users` | `id` | `UNIQUE(username)`, `UNIQUE(email)` | Stores registered voters and administrators with BCrypt-hashed credentials. |
| `elections` | `id` | `fk_election_creator -> users(id)` | Manages election events, schedules, descriptions, and lifecycle states. |
| `candidates` | `id` | `fk_candidate_election -> elections(id)` | Contains candidate details, target election, campaign position, and vote counters. |
| `votes` | `id` | `UNIQUE(election_id, user_id)`, `fk_vote_election`, `fk_vote_user`, `fk_vote_candidate` | Records cast ballots; composite unique constraint strictly blocks duplicate votes. |
| `otp` | `id` | `fk_email -> users(email)` | Tracks 6-digit one-time passwords for secure password recovery. |
| `application_settings` | `setting_key` | Primary Key on `setting_key` | Persists dynamic application configuration, themes, and SMTP settings. |

### Entity Relationship Diagram

```text
       +------------------+
       |      users       |
       +------------------+
       | id (PK)          |<-------------------+
       | username (UQ)    |                    |
       | password         |                    |
       | role             |                    |
       | email (UQ)       |----+               |
       | fullname         |    |               |
       +------------------+    |               |
         | 1                   | 1             | 1
         |                     |               |
         | creates             | generates     | casts
         |                     |               |
         v 0..*                v 0..*          v 0..*
+------------------+     +------------------+ +------------------+
|    elections     |     |       otp        | |      votes       |
+------------------+     +------------------+ +------------------+
| id (PK)          |     | id (PK)          | | id (PK)          |
| title            |     | email (FK)       | | election_id (FK) |
| start_date       |     | otp_code         | | user_id (FK)     |
| end_date         |     | status           | | candidate_id (FK)|
| status           |     | created_at       | | voted_at         |
+------------------+     +------------------+ | UQ(elect, user)  |
         | 1                                  +------------------+
         | contains                                    ^
         v 0..*                                        | receives
+------------------+                                   |
|    candidates    |-----------------------------------+
+------------------+
| id (PK)          |
| election_id (FK) |
| name             |
| position         |
| votes            |
+------------------+
```

---

## Security & Integrity

- **Duplicate Vote Prevention**: Guaranteed at both the controller layer and database level through a composite unique key constraint `UNIQUE (election_id, user_id)` on the `votes` table.
- **BCrypt Password Hashing**: Passwords are never stored in plaintext. They are salted and hashed using `jBCrypt` (`BCrypt.hashpw` / `BCrypt.checkpw`).
- **SQL Injection Defense**: Every database interaction utilizes `PreparedStatement` with parameterized placeholders.
- **Secure Password Reset**: Passwords can only be reset following validation of a time-limited 6-digit OTP dispatched to the registered user email.
- **Privilege Separation**: Strict role validation prevents standard users from accessing administrative FXML views or modifying elections/candidates.

---

## Getting Started

### Prerequisites

| Requirement | Minimum Version | Note |
| --- | --- | --- |
| **JDK** | 17 LTS | Oracle OpenJDK / Eclipse Temurin |
| **MySQL Server** | 8.0+ | Running on port `3306` |
| **Apache Maven** | 3.8+ | Or use the included `./mvnw` wrapper |
| **Git** | Any recent version | For version control |
| **IDE** | IntelliJ IDEA / Eclipse | Recommended for JavaFX tooling |

### 1. Clone the Repository

```bash
git clone https://github.com/GautamPrasid/voteing-system.git
cd voteing-system
```

### 2. Database Setup

1. Start your local MySQL service.
2. Connect to MySQL via CLI or workbench tool:
   ```bash
   mysql -u root -p
   ```
3. Execute the setup script:
   ```sql
   source database.sql;
   ```
   *Alternatively, import `database.sql` directly into your MySQL client.*

### 3. Install Dependencies

```bash
mvn clean install -DskipTests
```

---

## Configuration

### Database Connection

Verify or update your MySQL connection credentials in [`src/main/java/com/example/vote/util/DatabaseConnection.java`](src/main/java/com/example/vote/util/DatabaseConnection.java):

```java
public class DatabaseConnection {
    private static final String URL = "jdbc:mysql://localhost:3306/vote";
    private static final String USER = "root";
    private static final String PASSWORD = ""; // Set your MySQL password here
    // ...
}
```

### Email & SMTP Configuration

For password reset OTP emails to dispatch properly, configure SMTP details in [`src/main/java/com/example/vote/util/EmailService.java`](src/main/java/com/example/vote/util/EmailService.java) or via **Admin Panel -> Settings**:

```java
private static final String SMTP_HOST = "smtp.gmail.com";
private static final int SMTP_PORT = 587;
private static final String SMTP_USERNAME = "your-email@gmail.com";
private static final String SMTP_PASSWORD = "your-app-specific-password";
```

> [!TIP]
> **For Gmail users**: Generate an **App Password** from your Google Account settings (*Security > 2-Step Verification > App passwords*).

### Runtime Application Settings

The system supports real-time settings loaded dynamically from the `application_settings` database table:

| Setting Key | Default Value | Description |
| --- | --- | --- |
| `application_title` | `Voting System` | Window title displayed throughout views |
| `application_theme` | `Default` | Visual theme (`Default`, `Light`, `Dark`) |
| `enable_user_registration` | `true` | Allows or restricts new public voter signups |
| `enable_email_notifications` | `false` | Enables system-wide email event alerts |
| `enable_ip_blocking` | `true` | Protects login endpoints against brute-force |
| `date_format` | `MM/dd/yyyy` | Display format for election and audit timestamps |

---

## Running the Application

### Using Maven

To launch the JavaFX application directly from the terminal:

```bash
mvn javafx:run
```

### Packaging into Executable JAR

```bash
mvn clean package
java -jar target/vote-1.0-SNAPSHOT.jar
```

### Using an IDE (IntelliJ IDEA / Eclipse)

1. Open the project root directory in your IDE.
2. Ensure Maven syncs all dependencies.
3. Run the main class: [`src/main/java/com/example/vote/VoteApplication.java`](src/main/java/com/example/vote/VoteApplication.java).

---

## Default User Credentials

Initial seed records created by `database.sql`:

| Account Type | Username | Email | Default Password | Role |
| --- | --- | --- | --- | --- |
| **Administrator** | `admin` | `admin@example.com` | `admin123` | `admin` |
| **Regular Voter** | `user` | `user@example.com` | `user123` | `user` |

> [!IMPORTANT]
> Change the default passwords immediately upon initial installation for security compliance.

---

## Application Workflows

### Voter Workflow

```text
[Launch App] 
     |
     v
[Login / Sign Up] -----(Forgot Password?)-----> [Receive 6-Digit OTP] -> [Reset Password]
     |
     v
[Voter Dashboard]
     |
     +---> [Browse Elections] ---> [Select Active Election]
     |                                      |
     |                                      v
     |                             [Inspect Candidates & Manifesto]
     |                                      |
     |                                      v
     |                             [Cast Confidential Ballot]
     |                                      |
     |                                      v
     |                             [Instant Receipt & Verification]
     |
     +---> [View Voting History & Completed Election Results]
     |
     +---> [Manage Profile & Account Security]
```

### Administrator Workflow

```text
[Admin Login]
     |
     v
[Admin Dashboard & KPI Overview]
     |
     +---> [Manage Users] -----------> (Create, Edit, Deactivate, Delete Voters)
     |
     +---> [Manage Elections] -------> (Schedule, Activate, Conclude, Cancel)
     |                                           |
     |                                           v
     |                                 [Manage Candidates] -> (Link to Election)
     |
     +---> [Reports & Visuals] ------> (Turnout Statistics, Candidate Tallies, Winner Determination)
     |
     +---> [System Settings] --------> (Theme, Registration Toggle, SMTP & DB Config)
```

---

## Future Improvements

| Enhancement | Rationale |
| --- | --- |
| **Biometric / 2FA Login** | Add multi-factor authentication or WebAuthn/FIDO2 hardware keys for high-stakes ballots. |
| **Cryptographic Ballot Receipts** | Provide voters with zero-knowledge cryptographic hashes (e.g. SHA-256 verifiable receipts) to verify ballot inclusion without revealing choice. |
| **PDF & Excel Report Export** | Integrate Apache POI / iText to export election tally summaries and auditor certificates. |
| **HikariCP Connection Pool** | Replace raw JDBC connections with a high-performance connection pool. |
| **Modern Flat / Glassmorphic CSS Theme** | Upgrade styling with modern JavaFX CSS themes, smooth animations, and dark/light switching. |
| **Automated Election Cron Scheduler** | Add automated state transitions (`active` -> `completed`) based on system datetime triggers. |
| **Auditor Role** | Introduce a dedicated read-only `auditor` role for independent election observers. |

---

## Learning Outcomes

This project demonstrates:

- Developing modular desktop GUI applications using **JavaFX**, **FXML**, and **CSS**.
- Structuring desktop software following the **Model-View-Controller (MVC)** architectural pattern.
- Enforcing relational data integrity and duplicate-vote prevention using **MySQL Constraints** and **JDBC Transactions**.
- Implementing enterprise security best practices with **BCrypt salted password hashing** and **OTP recovery**.
- Managing asynchronous external communications with **JavaMail SMTP**.
- Organizing clean Maven dependency management and packaging portable desktop artifacts.

---

## Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository.
2. **Create a Feature Branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Commit your changes**:
   ```bash
   git commit -m "feat: add election export to PDF"
   ```
4. **Push to the branch**:
   ```bash
   git push origin feature/your-feature-name
   ```
5. **Open a Pull Request** with detailed notes on your modifications.

### Code Standards

- Maintain clean MVC separation (no UI rendering inside Model or DAO classes).
- Use `PreparedStatement` and `try-with-resources` for all SQL operations.
- Ensure all passwords and credentials use `PasswordHash` (BCrypt).
- Follow standard Java naming conventions and clean documentation.

---

## Author

| Name | Role | Institution |
| --- | --- | --- |
| **Prasid Gautam** | Full-Stack JavaFX Developer | Bachelor of Computer Application (BCA) |

---

## Support

If you found this project helpful, please give it a **Star** on GitHub! For issues, bugs, or feature suggestions, feel free to open an issue or pull request in the repository.

---

## License

This project is licensed under the **MIT License**. See the [`LICENSE`](LICENSE) file for details.

```text
MIT License - Copyright (c) 2026 Prasid Gautam
```

---

## Acknowledgements

- [OpenJFX Project](https://openjfx.io/) for the JavaFX platform.
- [MySQL Connector/J](https://dev.mysql.com/doc/connector-j/en/) for MySQL integration.
- [jBCrypt](https://www.mindrot.org/) for cryptographic hashing.
- [JavaMail](https://javaee.github.io/javamail/) for email protocols.
- [Apache Maven](https://maven.apache.org/) for build management.
- [Shields.io](https://shields.io/) for dynamic repository badges.

---

<div align="center">

**Built with Java 17, JavaFX, MySQL, and Maven.**

Voting System enables secure, transparent, and accessible democratic elections.

</div>
