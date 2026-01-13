# Questionnaire App — API Endpoints (Grouped by Module)

> Global versioned endpoints: `/v1/*`  
> Permissions are enforced via **guards/middleware** (admin/org/public).  
> Table format: **Endpoint | Method | Purpose | Request Details | Table(s) Used**

---

## Module: auth
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/auth/login` | POST | Login for **admin** or **organization user** (single endpoint) | **Body:** `{ email, password, role: "admin" \| "org" }` | `admin_users`, `organization_users`, `organizations` |
| `/v1/auth/refresh` | POST | Refresh access token | **Body:** `{ refreshToken }` | *(token storage if any)* |
| `/v1/auth/logout` | POST | Logout (invalidate refresh token if stored) | **Body:** `{ refreshToken }` | *(token storage if any)* |
| `/v1/auth/me` | GET | Get current user profile (admin/org) | **Auth:** Bearer token | `admin_users` or `organization_users`, `organizations` |

---

## Module: organizations
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/organizations` | GET | List organizations (admin), or list allowed orgs (public for dropdown) | **Query:** `search`, `status`, `page`, `perPage` | `organizations` |
| `/v1/organizations` | POST | Create organization (**admin only**) | **Body:** `{ code, name, type, timezone?, status? }` | `organizations` |
| `/v1/organizations/:id` | GET | Get organization detail | **Path:** `id` (UUID) | `organizations` |
| `/v1/organizations/:id` | PATCH | Update organization (**admin only**) | **Body:** partial org fields | `organizations` |
| `/v1/organizations/:id/users` | POST | Create organization portal user (**admin only**) | **Body:** `{ email, name, password, role?: "org_viewer" }` | `organization_users` |
| `/v1/organizations/:id/users` | GET | List organization users (**admin only**) | **Query:** `status`, `page`, `perPage` | `organization_users` |
| `/v1/organizations/:id/users/:userId` | PATCH | Update/disable org user (**admin only**) | **Body:** `{ name?, status?, password? }` | `organization_users` |

---

## Module: questionnaires
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/questionnaires` | GET | List questionnaires (published for public, all for admin) | **Query:** `status?`, `search?`, `lang?`, `page?`, `perPage?` | `questionnaires` |
| `/v1/questionnaires` | POST | Create questionnaire (draft) (**admin**) | **Body:** `{ code, title, description?, language, scoringType, showResultToUser? }` | `questionnaires` |
| `/v1/questionnaires/:id` | GET | Get questionnaire detail (admin sees draft; public sees published only) | **Path:** `id` (UUID) | `questionnaires` |
| `/v1/questionnaires/:id` | PATCH | Update questionnaire metadata (**admin**) | **Body:** partial fields | `questionnaires` |
| `/v1/questionnaires/:id/publish` | POST | Publish questionnaire (**admin**) | **Body:** `{}` | `questionnaires` |
| `/v1/questionnaires/:id/new-version` | POST | Create new questionnaire version (**admin**) | **Body:** `{}` | `questionnaires` (+ copy related tables) |

---

## Module: questionnaire-structure (questions, options, dimensions, mappings)
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/questionnaires/:id/dimensions` | GET | List dimensions | **Path:** `id` (questionnaire UUID) | `questionnaire_dimensions` |
| `/v1/questionnaires/:id/dimensions` | POST | Create dimension (**admin**) | **Body:** `{ key, name, sortOrder }` | `questionnaire_dimensions` |
| `/v1/questionnaires/:id/questions` | GET | List questions (optionally include options) | **Query:** `include=options` | `questionnaire_questions`, `questionnaire_options` |
| `/v1/questionnaires/:id/questions` | POST | Create question (**admin**) | **Body:** `{ code?, text, sortOrder, isRequired, questionType:"single_choice", meta? }` | `questionnaire_questions` |
| `/v1/questions/:id` | PATCH | Update question (**admin**) | **Body:** `{ text?, sortOrder?, isRequired?, meta? }` | `questionnaire_questions` |
| `/v1/questions/:id/options` | POST | Create option for a question (**admin**) | **Body:** `{ label, scoreValue, sortOrder }` | `questionnaire_options` |
| `/v1/options/:id` | PATCH | Update option (**admin**) | **Body:** `{ label?, scoreValue?, sortOrder? }` | `questionnaire_options` |
| `/v1/questions/:id/dimensions` | POST | Map question → dimension with weight (**admin**) | **Body:** `{ dimensionId, weight }` | `questionnaire_question_dimensions` |
| `/v1/questions/:id/dimensions/:mapId` | PATCH | Update mapping weight (**admin**) | **Body:** `{ weight }` | `questionnaire_question_dimensions` |

---

## Module: scoring-config (bands + meanings)
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/questionnaires/:id/score-bands` | GET | List score bands | **Path:** `id` (questionnaire UUID) | `questionnaire_score_bands` |
| `/v1/questionnaires/:id/score-bands` | POST | Create score band (**admin**) | **Body:** `{ scope:"dimension"\|"total", dimensionId?, label, minScore, maxScore, sortOrder }` | `questionnaire_score_bands` |
| `/v1/score-bands/:id` | PATCH | Update score band (**admin**) | **Body:** partial fields | `questionnaire_score_bands` |
| `/v1/questionnaires/:id/meanings` | GET | List meaning rules | **Path:** `id` (questionnaire UUID) | `questionnaire_meanings` |
| `/v1/questionnaires/:id/meanings` | POST | Create meaning rule (**admin**) | **Body:** `{ ruleType, ruleJson, resultLabel, description, recommendations?, priority, isActive }` | `questionnaire_meanings` |
| `/v1/meanings/:id` | PATCH | Update meaning rule (**admin**) | **Body:** partial fields | `questionnaire_meanings` |

---

## Module: user-fields (dynamic user info form)
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/user-fields` | GET | List global user fields library | **Query:** `active?`, `search?` | `questionnaire_user_fields` |
| `/v1/user-fields` | POST | Create user field definition (**admin**) | **Body:** `{ key, label, fieldType, validationJson?, optionsJson?, isActive }` | `questionnaire_user_fields` |
| `/v1/user-fields/:id` | PATCH | Update user field definition (**admin**) | **Body:** partial fields | `questionnaire_user_fields` |
| `/v1/questionnaires/:id/user-fields` | GET | List user field configs for questionnaire | **Path:** `id` (questionnaire UUID) | `questionnaire_user_field_configs`, `questionnaire_user_fields` |
| `/v1/questionnaires/:id/user-fields` | POST | Attach field to questionnaire (**admin**) | **Body:** `{ userFieldId, isRequired, sortOrder, overridesJson? }` | `questionnaire_user_field_configs` |
| `/v1/user-field-configs/:id` | PATCH | Update questionnaire field config (**admin**) | **Body:** `{ isRequired?, sortOrder?, overridesJson? }` | `questionnaire_user_field_configs` |

---

## Module: attempts (taking questionnaire)
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/questionnaires/:id/attempts` | POST | Start attempt (public) | **Body:** `{}` | `questionnaire_attempts` |
| `/v1/attempts/:attemptId` | GET | Get attempt status | **Path:** `attemptId` (UUID) | `questionnaire_attempts` |
| `/v1/attempts/:attemptId/user-info` | POST | Save user info snapshot | **Body:** `{ fields: [{ key, value }] }` | `questionnaire_attempt_user_info`, `questionnaire_user_field_configs`, `questionnaire_user_fields`, `organizations`, `questionnaire_attempts` |
| `/v1/attempts/:attemptId/answers` | POST | Save answers batch | **Body:** `{ answers: [{ questionId, optionId }] }` | `questionnaire_answers`, `questionnaire_questions`, `questionnaire_options` |
| `/v1/attempts/:attemptId/submit` | POST | Final submit + compute scoring + meaning | **Body:** `{}` | `questionnaire_attempts`, `questionnaire_answers`, `questionnaire_questions`, `questionnaire_options`, `questionnaire_question_dimensions`, `questionnaire_dimensions`, `questionnaire_score_bands`, `questionnaire_meanings`, `questionnaire_results`, `questionnaire_attempt_user_info` |
| `/v1/attempts/:attemptId/result` | GET | Get computed result for attempt | **Path:** `attemptId` (UUID) | `questionnaire_results`, `questionnaire_attempts`, `questionnaires` |

---

## Module: reports (list + detail + export)
| Endpoint | Method | Purpose | Request Details | Table(s) Used |
|---|---|---|---|---|
| `/v1/reports` | GET | List reports (admin: all, org: own only) | **Query:** `organizationId?` *(admin only)*, `questionnaireId?`, `dateFrom?`, `dateTo?`, `resultLabel?`, `search?`, `page?`, `perPage?` | `questionnaire_attempts`, `questionnaire_results`, `questionnaire_attempt_user_info`, `questionnaires`, `organizations` |
| `/v1/reports/:attemptId` | GET | Report detail (admin all; org own only) | **Path:** `attemptId` (UUID) | `questionnaire_attempts`, `questionnaire_results`, `questionnaire_attempt_user_info`, `questionnaire_answers`, `questionnaire_questions`, `questionnaire_options`, `questionnaires`, `organizations` |
| `/v1/reports/export` | POST | Export reports (CSV/XLSX) | **Body:** `{ filters..., format:"csv"\|"xlsx" }` | `questionnaire_attempts`, `questionnaire_results`, `questionnaire_attempt_user_info`, `questionnaires`, `organizations` |

---

# Guard / Permission Notes (No role-based URL prefixes)

## Public (anonymous)
- `GET /v1/questionnaires` *(published only)*
- `GET /v1/questionnaires/:id` *(published only)*
- `POST /v1/questionnaires/:id/attempts`
- `POST /v1/attempts/:attemptId/user-info`
- `POST /v1/attempts/:attemptId/answers`
- `POST /v1/attempts/:attemptId/submit`
- `GET /v1/attempts/:attemptId/result` *(only if `questionnaires.show_result_to_user=true`)*

## Organization user (org_viewer)
- `GET /v1/reports`
- `GET /v1/reports/:attemptId`
- `POST /v1/reports/export`

## Admin
- Full access to all endpoints.
