# Questionnaire App — Phase 1 (MVP) Requirements & Access

## 1) Purpose & Scope (Phase 1)
Build a questionnaire app that allows **anonymous users** to fill a questionnaire, computes **configurable scoring**, generates **score meanings**, stores **reports**, and lets **organizations** (school/company/government/etc.) **log in to view reports only**.

**Phase 1 constraints**
- Organizations are **created by Admin only**
- Organization accounts are **view-only** (reports access)
- Admin manages everything else (questionnaires, scoring, meanings, fields)

---

## 2) Roles & Access (Phase 1)

### A. Admin (Super Admin)
**Capabilities**
- Create & manage Organizations
- Create & manage Questionnaires (questions, options, scoring, bands, meanings)
- Configure **User Info Fields** (dynamic identity form before questionnaire)
- View **all reports** across all organizations
- Export reports (CSV/Excel) *(recommended MVP)*
- Analytics overview *(optional MVP)*

**Restrictions**
- None (global access)

---

### B. Organization User (Org Viewer) — Phase 1
**Capabilities**
- Login
- View **reports only** belonging to their organization
- Filter/search reports
- View report detail
- Export reports *(recommended MVP)*

**Restrictions**
- No questionnaire/scoring management
- No organization/user management
- No cross-organization access

---

### C. Anonymous / Non-Registered User (Public)
**Capabilities**
- Start questionnaire without login
- Fill **User Info Form** (admin-configured fields)
- Fill questionnaire
- Submit answers
- View result page (configurable per questionnaire)

**Restrictions**
- No dashboard/history (Phase 1)
- No login

---

## 3) Core MVP Features (Phase 1)

### 3.1 Questionnaire Taking (Public)
**Flow**
1) Select questionnaire (or shared link)
2) **User Info Step** (dynamic fields)
3) Answer questionnaire
4) Submit

**UI/UX Requirements**
- Progress indicator (e.g., 12/45)
- Back/Next navigation (or paginated list)
- Required validation before submit
- Mobile-friendly

**Question Types (MVP)**
- Single choice (radio)

---

### 3.2 User Info Fields (Admin-configurable) — Before Questionnaire
Admin config controls what anonymous users must fill.

**Field Types (MVP)**
- text, number, select, date, email, phone, textarea

**Per-field Controls**
- required/optional
- display order
- validation rules (min/max length, regex, allowed values)
- scope: global (all questionnaires) or per questionnaire

**Organization Field**
- recommended **required**
- dropdown with search (if long list)
- values come from Admin-created organizations

**Important Rule**
- User info saved as **snapshot** at submission time.

---

### 3.3 Scoring Engine (Configurable per Questionnaire)
Each questionnaire can define its own scoring rules.

**Supported scoring models in Phase 1**
1) **Weighted Multi-Dimension (default)**
- Example: Visual/Auditory/Kinesthetic
- Question → dimension mapping (MVP: 1 dimension per question)
- Option score values are questionnaire-specific

2) **Simple Total Score (optional but recommended)**
- Sum all answers into total score

**Outputs**
- scores (per dimension and/or total)
- bands (per dimension and/or total)
- final result label (dominant/combination/fallback)

---

### 3.4 Score Bands (Low/Medium/High etc.)
Per questionnaire define score ranges → band labels.

**Must support**
- per-dimension bands (multi-dimension)
- global bands (total score)

---

### 3.5 Score Meaning (Interpretation Layer)
Generate a narrative meaning based on scores/bands.

**Must support**
- meaning by dominant dimension
- meaning by combination
- fallback/ambiguous meaning (e.g., “Konsultasi / Tes ulang”)

**Meaning output**
- result label
- narrative description
- optional recommendations list

---

### 3.6 Report Storage & Viewing
On submit:
- answers saved
- scoring computed
- banding applied
- meaning resolved
- report stored and linked to organization

**Organization portal**
- Reports list table
- Report detail page

---

## 4) Phase 1 End-to-End Flows

### A) Anonymous User Flow
1. Choose questionnaire
2. Fill User Info (name, organization/school, class/etc.)
3. Fill questionnaire
4. Submit
5. Result shown (optional) + report stored for org/admin

### B) Organization User Flow (View-only)
1. Login
2. Reports dashboard (only own organization)
3. Filter/search (date, class, questionnaire, result label, bands)
4. Open report detail
5. Export (optional)

### C) Admin Flow
1. Create organizations
2. Create questionnaires
3. Add questions + options
4. Configure scoring + bands + meanings
5. Configure user info fields
6. View/export all reports

---

## 5) Permissions Summary (Phase 1)
- **Admin**: full access
- **Organization**: read-only reports (own org only)
- **Anonymous**: submit only

---

## 6) Phase 1 Acceptance Criteria
- Admin can create orgs and configure questionnaires, scoring, bands, meanings, and user-info fields.
- Anonymous user can complete and submit successfully.
- System returns correct scores/bands/meaning.
- Organization can view only its own reports and open report detail.
- Admin can view/export all reports.

---

# 7) SaaS Roadmap Requirements (Later)

## Phase 2 — Organization Self-Registration & Tenant Setup
**Org onboarding**
- Organization can register (signup) with:
  - org name, org type, region
  - admin contact email
- Email verification
- First user becomes **Org Admin**

**Tenant setup**
- Organization profile:
  - logo, address, domain/slug (optional)
  - default timezone
- Invite team members:
  - roles: Org Admin, Org Viewer, (later) Org Editor
- Basic tenant isolation across all data access

---

## Phase 3 — Billing & Plans (Subscribe / One-time Purchase)
**Billing models**
- Subscription plans:
  - monthly/yearly
  - seat-based or usage-based
- One-time purchase:
  - report packs (e.g., 500 submissions)
  - per questionnaire pack
  - limited-time access (e.g., 30 days)

**Plan gating / entitlements**
- Max submissions / month
- Max active questionnaires
- Export access (CSV/Excel/PDF)
- Advanced analytics access
- Custom branding access
- API access (later)

**Payment & invoicing**
- Payment provider integration (Stripe/Midtrans/etc.)
- Invoice history
- Renewal reminders
- Grace period + downgrade behavior

---

## Phase 4 — Organization-Level Customization (True SaaS)
**Branding**
- Org-branded result pages
- Custom logo/colors on reports
- Optional custom domain

**Questionnaire distribution**
- Share links per org
- QR code generation per questionnaire
- Form prefill fields (org locked)

**Data operations**
- Data retention settings (e.g., 1 year, 3 years)
- Export all data
- Delete requests / GDPR-style controls (if global)

---

## Phase 5 — Advanced Features & Enterprise
**Rosters & identity**
- Import respondents list (CSV)
- Unique respondent IDs (studentId/employeeId)
- Prevent duplicates / enforce one submission per user per period

**Analytics**
- Cohort analysis by class/department
- Trend over time
- Heatmaps by score bands/dimensions
- Benchmarking (org vs global anonymized benchmarks)

**Automation**
- Scheduled report emails to org admins
- Webhooks / integrations (Slack, Google Sheets, LMS)

**Audit & compliance**
- Audit logs (who viewed/exported)
- Role-based access controls (RBAC) expanded
- SSO (SAML/OAuth) for enterprise
