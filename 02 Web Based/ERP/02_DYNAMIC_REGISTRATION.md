# Dynamic & Configurable Registration Engine Specification

## 1. Overview
The Registration Engine allows institute administrators to dynamically define, configure, and order application screens for their college. Each screen is a step in the student application wizard.

## 2. Screen Configuration Schema
Each screen definition (`registration_screens` table in the tenant database) contains:
- `id`: UUID
- `tenant_id`: College identifier
- `name`: Human-readable screen title (e.g. "Personal Details", "Academic Qualifications", "Fee Payment", "Course Allotment Preferences")
- `slug`: Unique screen key
- `order_index`: Execution sequence in the registration wizard
- `is_active`: Toggle switch to enable/disable the screen
- **1. Visibility & Permissions ("Who can see")**:
  - `allowed_roles`: List of roles permitted to view this screen (e.g. `["student", "staff", "institute_admin"]`)
- **2. Action Permissions ("Who can perform which action")**:
  - `action_permissions`: JSON map defining fine-grained capabilities:
    - `can_fill`: Roles allowed to input data
    - `can_edit`: Roles allowed to edit existing data
    - `can_approve`: Roles allowed to verify/approve step data
- **3. Fee Configuration ("Is there a fee?")**:
  - `has_fee`: Boolean flag
  - `fee_config`: JSON blob detailing:
    - `fee_type`: Application fee, processing fee, quota fee, etc.
    - `amount`: Base amount
    - `category_amounts`: Category-specific fee overrides (e.g., GEN: 1000, SC/ST: 500)
    - `payment_timing`: `before_submission`, `after_verification`, `on_allotment`
    - `payment_gateways`: Supported payment methods/gateways
- **4. Screen Details & Form Fields**:
  - `fields`: Array of field definitions (linking to `FieldIdentifier` / custom field rules):
    - `field_key`: Identifier key
    - `label`: Display title
    - `field_type`: `text`, `number`, `select`, `file`, `date`, `radio`, `checkbox`
    - `is_required`: Boolean
    - `validation_regex`: Optional regex
    - `options`: Dropdown/radio choice items
- **5. Allotment Configuration**:
  - `enable_allotment`: Boolean flag specifying whether this screen participates in or triggers course allotment preference calculations.

## 3. Workflow Execution
1. Student initiates registration.
2. Backend queries active screens ordered by `order_index` for the tenant.
3. For each screen, permission checks evaluate visibility and action capabilities against the student's role and state.
4. Screen renders dynamically on the frontend following the modular SCSS & DIP architecture.
5. If `has_fee` is true and `payment_timing` condition is met, fee collection is enforced before advancing to the next step.
6. If `enable_allotment` is enabled, course preference selection and allotment engine bindings are activated.
