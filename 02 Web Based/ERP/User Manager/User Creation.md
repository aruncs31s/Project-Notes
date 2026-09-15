Here is a step-by-step guide on how to use the new **SuperAdmin Users Manager** page to provision administrative and faculty accounts and configure their screen permissions:

---

### Step 1: Navigate to the Users Manager
1. Log in to the ERP application with a `super_admin` account.
2. Click the **Users Manager** link in the sidebar navigation menu under the *Super Admin* section.

---

### Step 2: Select the College Tenant
1. Use the **Institution** dropdown at the top of the panel to select the target college.
2. The page will automatically query that college's database schema and display the directory of active moderators, teachers, and staff members.

---

### Step 3: Open the Provisioning Modal
1. Click the **Provision New User** button on the top right of the panel to open the setup form.

---

### Step 4: Fill User Credentials & Select Preset
1. Complete the core profile details: **First Name**, **Last Name**, **Email / Username**, **Temporary Password**, and **Phone**.
2. Select the **User Type / RolePreset**:
   * **Moderator / Admin** (`institute_admin`)
   * **Teacher of Institution** (`teacher`)
   * **Staff / Clerk** (`staff`)
   *(Note: There is no option to create students here, as student admission is handled directly by the college's staff/teachers).*

---

### Step 5: Configure Screen Access (Permissions Matrix)
1. Selecting a User Type automatically seeds the default screen checkmarks in the **Module Screen Access & Permissions** checklist below.
2. You can custom-toggle any individual checkmark in the grid to grant or restrict access to specific screens and actions (`read`, `write`, `delete`, `manage`):
   * **Student Registration**: Admission pipeline & seat claims.
   * **Course Directory**: Curriculum management.
   * **Document & OCR Verification**: Reviewing uploaded transcripts.
   * **Support Desk**: Handling student issues/tickets.
   * **Staff & User Registry**: Internal user management.
   * **College Billing Plans**: Subscriptions.
   * **System Configuration**: Local settings.

---

### Step 6: Save and Provision
1. Click **Provision User & Permissions**.
2. Behind the scenes:
   * The application registers the user inside the selected college database schema.
   * The checked permissions are compiled into Casbin policy rules (`subject: userID, resource: module, action: privilege`) and written to the database.
   * The modal will close, a success toast will appear, and the user list will refresh to display the new account.