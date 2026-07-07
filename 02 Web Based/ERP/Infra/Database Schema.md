# Database Schema

The system uses a **multi-db catalog architecture** to support strict multi-tenancy. Core infrastructure configurations are held centrally, whereas college transactions, courses, and student records are isolated inside individual database schemas.

---

## Navigation
- [[Overview]] - Global system components and monorepo files.
- [[Backend Architecture]] - Go Clean Architecture layer.
- [[Frontend Architecture]] - SPA UI routing and components.
- [[../Registration/Registration Module|Registration Module]] - Gale-Shapley and OCR module.

---

## Database Partitioning

```
                     ┌──────────────────┐
                     │    MySQL Host    │
                     └────────┬─────────┘
                              │
             ┌────────────────┴────────────────┐
             ▼                                 ▼
   ┌───────────────────┐             ┌───────────────────┐
   │    Master DB      │             │    Tenant DBs     │
   │  (db: erp_master) │             │ (db: college_<id>)│
   └─────────┬─────────┘             └─────────┬─────────┘
             │                                 │
     ┌───────┴───────┐                 ┌───────┼───────┐
     ▼               ▼                 ▼       ▼       ▼
  colleges       billings            users  courses applications
```

### 1. Master Database (`erp_master`)
Stores global routing lookup coordinates, metadata, and SaaS billing configs.

#### `colleges`
Catalogs all colleges (tenants) signed up on the ERP.
- `id` (VARCHAR(36), PK): UUID.
- `name` (VARCHAR(255)): College name.
- `slug` (VARCHAR(100), Unique): Domain URL segment (e.g. `democollege`).
- `address` (TEXT), `phone` (VARCHAR(20)), `email` (VARCHAR(255)), `website` (VARCHAR(255)).
- `database_name` (VARCHAR(100)): Target tenant schema name (e.g. `college_demo`).
- `config` (JSON): Global rules (e.g. standard file size limit, active document types).
- `is_active` (BOOLEAN): Status toggle.
- `created_at`, `updated_at` (DATETIME).

#### `billings`
Manages billing cycles and subscription limits.
- `id` (VARCHAR(36), PK): UUID.
- `college_id` (VARCHAR(36), FK -> colleges.id): Associated tenant.
- `plan` (VARCHAR(50)): Subscription tier (e.g. Basic, Enterprise).
- `amount` (DECIMAL): Amount charged.
- `currency` (VARCHAR(10)): Standard billing currency (e.g. INR).
- `status` (VARCHAR(50)): Status (e.g. ACTIVE, IN arrears).
- `valid_from`, `valid_until` (DATETIME): Active billing coverage timeframe.

---

### 2. Tenant Databases (`college_<tenantID>`)
Each college has its own database containing students, academic staff, and curriculum schedules.

#### `users`
Accounts belonging to the tenant college.
- `id` (VARCHAR(36), PK): UUID.
- `email` (VARCHAR(255), Unique): Account email.
- `password` (VARCHAR(255)): Hashed password.
- `first_name` (VARCHAR(100)), `last_name` (VARCHAR(100)): Student or staff name.
- `role` (VARCHAR(50)): Security group (`student`, `teacher`, `staff`, `institute_admin`).
- `is_active` (BOOLEAN).
- `phone` (VARCHAR(20)).

#### `academic_years`
Calendar definitions for system operation scopes.
- `id` (VARCHAR(36), PK): UUID.
- `name` (VARCHAR(100)): Academic label (e.g. `2026-27`).
- `start_date`, `end_date` (DATETIME).
- `is_active` (BOOLEAN): Current active operational year.

#### `course_types`
Broader categorization streams.
- `id` (VARCHAR(36), PK): UUID.
- `name` (VARCHAR(100)): Label (e.g. `Undergraduate`).
- `code` (VARCHAR(50)): Code tag (e.g. `UG`).
- `duration_years` (INT): Default duration.

#### `courses`
Specific degrees offered by the college.
- `id` (VARCHAR(36), PK): UUID.
- `name` (VARCHAR(255)): Program title.
- `code` (VARCHAR(50)): Program code.
- `description` (TEXT).
- `duration` (INT): Duration in years.
- `total_seats` (INT): Capacity limit.
- `course_type_id` (VARCHAR(36), FK -> course_types.id).
- `academic_year_id` (VARCHAR(36), FK -> academic_years.id).
- `is_active` (BOOLEAN).

#### `batches`
Year/Class division slots.
- `id` (VARCHAR(36), PK): UUID.
- `course_id` (VARCHAR(36), FK -> courses.id).
- `academic_year_id` (VARCHAR(36), FK -> academic_years.id).
- `name` (VARCHAR(100)): Label (e.g. `Section A`).

#### `admission_categories`
Intake quotas categories.
- `id` (VARCHAR(36), PK): UUID.
- `name` (VARCHAR(100)): (e.g. `Merit Pool`).
- `code` (VARCHAR(50)): (e.g. `GEN`).
- `percentage` (DECIMAL): Seat allocation weight (e.g. `50.00`).

#### `admission_quotas`
Intake reservation quotas.
- `id` (VARCHAR(36), PK): UUID.
- `name` (VARCHAR(100)): (e.g. `Sports Quota`).
- `code` (VARCHAR(50)): (e.g. `SPORTS`).
- `description` (TEXT).

#### `seat_buckets`
Junction tables specifying seats available per Course * Category * Quota.
- `id` (VARCHAR(36), PK): UUID.
- `course_id` (VARCHAR(36), FK -> courses.id).
- `batch_id` (VARCHAR(36), Nullable, FK -> batches.id).
- `academic_year_id` (VARCHAR(36), FK -> academic_years.id).
- `admission_category_id` (VARCHAR(36), FK -> admission_categories.id).
- `admission_quota_id` (VARCHAR(36), FK -> admission_quotas.id).
- `total_seats` (INT): Designated seats.
- `filled_seats` (INT): Occupied seats.

#### `applications`
Student registration details.
- `id` (VARCHAR(36), PK): UUID.
- `student_id` (VARCHAR(36), FK -> users.id): Applicant user ID.
- `academic_year_id` (VARCHAR(36), FK -> academic_years.id).
- `register_number` (VARCHAR(50), Unique): Sequence generated value.
- `status` (VARCHAR(50)): (`DRAFT`, `SUBMITTED`, `UNDER_REVIEW`, `APPROVED`, `REJECTED`, `WAITLISTED`, `ALLOTTED`, `CONFIRMED`, `WITHDRAWN`).
- `qualifiers` (JSON): Dynamically loaded score parameters based on qualifier configs.
- `category` (VARCHAR(50)): Applicant reservation category pool.
- `remarks` (TEXT): Audit/review feedback comments.
- `submitted_at` (DATETIME).

#### `course_preferences`
Requested course options submitted by the student.
- `id` (VARCHAR(36), PK): UUID.
- `application_id` (VARCHAR(36), FK -> applications.id).
- `course_id` (VARCHAR(36), FK -> courses.id).
- `preference_order` (INT): Choice index order (e.g. 1st, 2nd, 3rd choice).

#### `documents`
Uploaded grade transcripts and certifications.
- `id` (VARCHAR(36), PK): UUID.
- `application_id` (VARCHAR(36), FK -> applications.id).
- `type` (VARCHAR(100)): Grade Card, ID Proof.
- `file_name` (VARCHAR(255)): Original name.
- `storage_path` (VARCHAR(500)): MinIO bucket path.
- `mime_type` (VARCHAR(100)), `size` (BIGINT).
- `status` (VARCHAR(50)): (`PENDING`, `VERIFIED`, `REJECTED`).
- `ocr_text` (LONGTEXT): Extracted characters.
- `verified_by` (VARCHAR(36)): Verifying admin user ID.
- `verified_at` (DATETIME).
- `rejection_note` (TEXT).

#### `rank_entries`
Resulting placement records produced by the ranking enforcer.
- `id` (VARCHAR(36), PK): UUID.
- `application_id` (VARCHAR(36), FK -> applications.id).
- `course_id` (VARCHAR(36), FK -> courses.id).
- `category` (VARCHAR(50)): Allocation pool.
- `score` (DOUBLE): Compiled score rank weight.
- `rank` (INT): Computed rank index.
- `allocation_status` (VARCHAR(50)): (`PENDING`, `ALLOTTED`, `CONFIRMED`, `REJECTED`, `WAITLISTED`).
- `published_at` (DATETIME).
