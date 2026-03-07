# ESDC LMS Backend Documentation

Welcome to the backend documentation for the **ESDC LMS (Learning Management System)**. This project is built using Go (Golang) and provides a robust API for managing courses, users, assignments, chat, and more.

## Table of Contents

- [Overview](#overview)
- [Architecture & Tech Stack](./architecture.md)
- [Setup & Installation](./setup.md)
- [API Documentation](./api.md)

## Overview

The ESDC LMS Backend serves as the core engine for an e-learning platform. It handles all business logic, database operations, and third-party integrations required for the system.

### Key Features

- **User Management & Authentication**: Secure registration and login using JWT. Profile management and role-based access control (Admin, Teacher, Student).
- **Course Management**: Complete CRUD operations for courses and modules. Supports enrollments, likes, reviews, and tracking completion.
- **Assignments & Submissions**: Teachers can create standard and coding assignments. Students can submit, and teachers can grade them.
- **Code Execution Sandbox**: A secure environment for running and testing coding assignments via an integrated code runner.
- **Real-Time Communication**: Integrated WebSocket-based chat system for courses and direct messaging.
- **Certificate Generation**: Automated PDF certificate generation for course completions, utilizing templates and orchestrators.
- **Notifications**: System-wide notifications for users with read/unread tracking.
- **Media Uploads**: Handlers for uploading and serving images, videos, and attachments.

Please refer to the detailed sections linked in the Table of Contents for deeper dives into specific areas.
