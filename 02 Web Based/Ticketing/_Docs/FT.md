# Login & Register Pages - Features Added

## Overview
Created fully functional login and register pages with college selection support for the Ticketing System.

## Changes Made

### 1. **Updated User Model** (`domain/models/User.go`)
- Added `College` field to the User struct
- Type: `string`, optional field for storing user's college/institution

### 2. **Updated DTOs** (`application/dto/auth.go`)
- Updated `RegisterDTO` to include `College` field (required)
- Updated `UserDTO` to include `College` field (optional)

### 3. **Updated Auth Handler** (`application/handlers/auth/auth_handler.go`)
- Modified `Register` method to handle college field
- Updated response DTOs to include college information in both Register and Login responses
- Updated GetProfile method to include college in response

### 4. **Created Login Page Template** (`application/templates/login.templ`)
Features:
- Clean, modern design with Tailwind CSS
- Email and password input fields
- Error message display
- Client-side form validation
- Automatic token storage in localStorage
- Redirect to tickets page after successful login
- Link to register page
- Responsive design

### 5. **Created Register Page Template** (`application/templates/register.templ`)
Features:
- Full name input field
- Email input field with validation
- **College selection dropdown** with options:
  - College of Engineering
  - College of Science
  - College of Arts
  - College of Commerce
  - College of Medicine
  - College of Law
  - College of Business
  - College of Technology
- Password field with 6-character minimum requirement
- Client-side validation
- Error message display
- Automatic token storage in localStorage
- Redirect to tickets page after successful registration
- Link to login page
- Responsive design

### 6. **Created Page Handler** (`application/handlers/auth/page_handler.go`)
- `ShowLoginPage()` - Renders the login page template
- `ShowRegisterPage()` - Renders the register page template

### 7. **Updated Auth Routes** (`application/routes/auth_routes.go`)
Added page routes:
- `GET /login` - Serves the login page
- `GET /register` - Serves the register page

Existing API routes remain unchanged:
- `POST /auth/register` - Registration API endpoint
- `POST /auth/login` - Login API endpoint
- `POST /auth/refresh` - Token refresh endpoint
- `GET /auth/profile` - Get user profile (protected)

## How to Use

### For Users:
1. Navigate to `/register` to create a new account
   - Enter full name
   - Enter email
   - Select your college
   - Enter password (minimum 6 characters)
   - Click "Create Account"
   
2. Navigate to `/login` to sign in
   - Enter email
   - Enter password
   - Click "Sign In"

### For Developers:
The API endpoints support JSON requests:

**Register:**
```bash
POST /auth/register
Content-Type: application/json

{
  "full_name": "John Doe",
  "email": "john@example.com",
  "college": "Engineering",
  "password": "securepassword123"
}
```

**Login:**
```bash
POST /auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "securepassword123"
}
```

## Styling
- **Framework**: Tailwind CSS (via CDN)
- **Font**: Inter from Google Fonts
- **Color Scheme**: Indigo/Blue gradient theme
- **Responsive**: Mobile-first design with full responsiveness

## Client-Side Features
- Form validation with visual feedback
- Error message handling and display
- JWT token storage in localStorage
- Automatic redirection to tickets page after authentication
- Real-time form validation for password length and email format

## Database
When the database migrations run, the new `College` column will be added to the `users` table automatically (ensure your migrations are set up to handle this).

## Notes
- College field is required for registration
- All form validations are performed both client-side and server-side
- Tokens are stored in localStorage and sent in API requests automatically
- The design is consistent with the existing Ticketing System styling

