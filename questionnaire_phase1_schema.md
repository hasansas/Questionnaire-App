# Questionnaire App — Phase 1 Schema (Postgres, UUID IDs)

Below is a **Phase 1 Postgres schema** (all primary keys are **UUID**) covering: **Admin-managed Organizations**, **Org report viewer login**, **Questionnaires + scoring**, **Dynamic User Info Fields**, **Attempts/Answers/Results**, and **Exports-ready reporting**.

> Table view format: **Table name | Column | Field (type) | Description | Example value**

---

## organizations
| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| organizations | id | UUID (PK) | Organization ID | `3f6c2b9c-7c2e-4e72-8a7b-8c8a2a44d2d1` |
| organizations | code | TEXT (unique) | Short unique code/slug | `sma-1-jkt` |
| organizations | name | TEXT | Organization name | `SMA Negeri 1 Jakarta` |
| organizations | type | TEXT | Organization type | `school` |
| organizations | status | TEXT | `active / inactive` | `active` |
| organizations | timezone | TEXT | Default timezone (optional) | `Asia/Jakarta` |
| organizations | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |
| organizations | updated_at | TIMESTAMPTZ | Updated time | `2026-01-13T02:10:00Z` |

---

## organization_users
(Org portal accounts; **Admin creates** in Phase 1)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| organization_users | id | UUID (PK) | Org user ID | `9f88c6de-6c6a-49e5-8d18-0c3f3d4d4fb1` |
| organization_users | organization_id | UUID (FK) | Belongs to organization | `3f6c2b9c-7c2e-4e72-8a7b-8c8a2a44d2d1` |
| organization_users | email | TEXT (unique) | Login email | `admin@sma1.sch.id` |
| organization_users | password_hash | TEXT | Hashed password | `$2b$10$...` |
| organization_users | name | TEXT | Display name | `Budi Santoso` |
| organization_users | role | TEXT | `org_viewer` (Phase 1) | `org_viewer` |
| organization_users | status | TEXT | `active / disabled` | `active` |
| organization_users | last_login_at | TIMESTAMPTZ | Last login timestamp | `2026-01-13T02:20:00Z` |
| organization_users | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |
| organization_users | updated_at | TIMESTAMPTZ | Updated time | `2026-01-13T02:10:00Z` |

---

## questionnaires
| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaires | id | UUID (PK) | Questionnaire ID | `a1e6c2d8-1a7d-4e35-9f1e-0b2a3b4c5d6e` |
| questionnaires | code | TEXT (unique) | Unique code | `learning-style-v1` |
| questionnaires | title | TEXT | Display title | `Kuesioner Gaya Belajar` |
| questionnaires | description | TEXT | Description text | `Mengukur preferensi gaya belajar...` |
| questionnaires | language | TEXT | Content language | `id` |
| questionnaires | status | TEXT | `draft / published / archived` | `published` |
| questionnaires | version | INT | Version for snapshotting | `1` |
| questionnaires | scoring_type | TEXT | `multi_dimension / total_score` | `multi_dimension` |
| questionnaires | show_result_to_user | BOOLEAN | Show result after submit | `true` |
| questionnaires | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |
| questionnaires | updated_at | TIMESTAMPTZ | Updated time | `2026-01-13T02:10:00Z` |

---

## questionnaire_dimensions
(Only needed for `multi_dimension` scoring)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_dimensions | id | UUID (PK) | Dimension ID | `c8f1b3a2-0a1b-4d9a-9f11-2a4c5e6f7a8b` |
| questionnaire_dimensions | questionnaire_id | UUID (FK) | Parent questionnaire | `a1e6c2d8-1a7d-4e35-9f1e-0b2a3b4c5d6e` |
| questionnaire_dimensions | key | TEXT | Stable key | `visual` |
| questionnaire_dimensions | name | TEXT | Display name | `Visual` |
| questionnaire_dimensions | sort_order | INT | Ordering | `1` |
| questionnaire_dimensions | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |

---

## questionnaire_questions
| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_questions | id | UUID (PK) | Question ID | `d6c4b1a0-2c3d-4e5f-8a9b-0c1d2e3f4a5b` |
| questionnaire_questions | questionnaire_id | UUID (FK) | Parent questionnaire | `a1e6c2d8-1a7d-4e35-9f1e-0b2a3b4c5d6e` |
| questionnaire_questions | code | TEXT | Optional internal code | `Q01` |
| questionnaire_questions | text | TEXT | Question text | `Saya lebih mudah belajar dari gambar.` |
| questionnaire_questions | question_type | TEXT | `single_choice` (MVP) | `single_choice` |
| questionnaire_questions | is_required | BOOLEAN | Required question | `true` |
| questionnaire_questions | sort_order | INT | Ordering | `1` |
| questionnaire_questions | meta | JSONB | Extra config (optional) | `{"hint":"..."}` |
| questionnaire_questions | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |
| questionnaire_questions | updated_at | TIMESTAMPTZ | Updated time | `2026-01-13T02:10:00Z` |

---

## questionnaire_question_dimensions
(Map question → dimension(s); MVP: 1 dimension per question)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_question_dimensions | id | UUID (PK) | Mapping ID | `e1b2c3d4-5f6a-4b7c-8d9e-0a1b2c3d4e5f` |
| questionnaire_question_dimensions | question_id | UUID (FK) | Question | `d6c4b1a0-2c3d-4e5f-8a9b-0c1d2e3f4a5b` |
| questionnaire_question_dimensions | dimension_id | UUID (FK) | Dimension | `c8f1b3a2-0a1b-4d9a-9f11-2a4c5e6f7a8b` |
| questionnaire_question_dimensions | weight | NUMERIC(6,2) | Score multiplier | `1.00` |

---

## questionnaire_options
(Answer choices)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_options | id | UUID (PK) | Option ID | `f7a8b9c0-1d2e-4f3a-8b9c-0d1e2f3a4b5c` |
| questionnaire_options | question_id | UUID (FK) | Parent question | `d6c4b1a0-2c3d-4e5f-8a9b-0c1d2e3f4a5b` |
| questionnaire_options | label | TEXT | Label shown | `Setuju` |
| questionnaire_options | score_value | INT | Points granted | `2` |
| questionnaire_options | sort_order | INT | Ordering | `1` |
| questionnaire_options | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |

---

## questionnaire_score_bands
(Banding per dimension or total score)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_score_bands | id | UUID (PK) | Band ID | `aa11bb22-cc33-4dd4-8ee5-ff6677889900` |
| questionnaire_score_bands | questionnaire_id | UUID (FK) | Questionnaire | `a1e6c2d8-1a7d-4e35-9f1e-0b2a3b4c5d6e` |
| questionnaire_score_bands | scope | TEXT | `dimension / total` | `dimension` |
| questionnaire_score_bands | dimension_id | UUID (FK, nullable) | Required if scope=`dimension` | `c8f1b3a2-0a1b-4d9a-9f11-2a4c5e6f7a8b` |
| questionnaire_score_bands | label | TEXT | Band label | `High` |
| questionnaire_score_bands | min_score | INT | Inclusive min | `21` |
| questionnaire_score_bands | max_score | INT | Inclusive max | `30` |
| questionnaire_score_bands | sort_order | INT | Ordering | `3` |
| questionnaire_score_bands | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |

---

## questionnaire_meanings
(Rule-based meaning resolver)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_meanings | id | UUID (PK) | Meaning ID | `b0c1d2e3-f4a5-4b6c-8d9e-0f1a2b3c4d5e` |
| questionnaire_meanings | questionnaire_id | UUID (FK) | Questionnaire | `a1e6c2d8-1a7d-4e35-9f1e-0b2a3b4c5d6e` |
| questionnaire_meanings | rule_type | TEXT | `dominant_dimension / band_combo / fallback` | `band_combo` |
| questionnaire_meanings | rule_json | JSONB | Matching rule | `{"visual":"High","auditory":"High","kinesthetic":"Medium"}` |
| questionnaire_meanings | result_label | TEXT | Final label | `VISUAL AUDITORY` |
| questionnaire_meanings | description | TEXT | Meaning narrative | `Anda belajar efektif melalui...` |
| questionnaire_meanings | recommendations | JSONB | Optional bullets | `["Gunakan mind-map","Gunakan audio ulang"]` |
| questionnaire_meanings | priority | INT | Higher wins | `100` |
| questionnaire_meanings | is_active | BOOLEAN | Enable/disable | `true` |
| questionnaire_meanings | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |

---

## questionnaire_user_fields
(Admin-configurable “User Info Form” fields library)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_user_fields | id | UUID (PK) | Field ID | `c111d222-e333-4f44-8a55-b666c777d888` |
| questionnaire_user_fields | key | TEXT (unique) | Stable key | `class` |
| questionnaire_user_fields | label | TEXT | Display label | `Class` |
| questionnaire_user_fields | field_type | TEXT | `text/number/select/date/email/phone/textarea` | `select` |
| questionnaire_user_fields | validation_json | JSONB | Rules | `{"required":true,"maxLen":50}` |
| questionnaire_user_fields | options_json | JSONB | Select options | `["X-A","X-B","XI-A"]` |
| questionnaire_user_fields | is_active | BOOLEAN | Enable/disable globally | `true` |
| questionnaire_user_fields | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |

---

## questionnaire_user_field_configs
(Attach user fields to a questionnaire, ordering + required flags)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_user_field_configs | id | UUID (PK) | Config ID | `d999e888-f777-4a66-8b55-c444b333a222` |
| questionnaire_user_field_configs | questionnaire_id | UUID (FK) | Questionnaire | `a1e6c2d8-1a7d-4e35-9f1e-0b2a3b4c5d6e` |
| questionnaire_user_field_configs | user_field_id | UUID (FK) | Field definition | `c111d222-e333-4f44-8a55-b666c777d888` |
| questionnaire_user_field_configs | is_required | BOOLEAN | Required for this questionnaire | `true` |
| questionnaire_user_field_configs | sort_order | INT | Ordering in form | `2` |
| questionnaire_user_field_configs | overrides_json | JSONB | Per-questionnaire overrides | `{"label":"Kelas","options":["10-A","10-B"]}` |

---

## questionnaire_attempts
(Anonymous submissions are allowed; optional user account later)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_attempts | id | UUID (PK) | Attempt ID | `e2f3a4b5-c6d7-4e8f-9a0b-1c2d3e4f5a6b` |
| questionnaire_attempts | questionnaire_id | UUID (FK) | Questionnaire | `a1e6c2d8-1a7d-4e35-9f1e-0b2a3b4c5d6e` |
| questionnaire_attempts | questionnaire_version | INT | Version snapshot | `1` |
| questionnaire_attempts | organization_id | UUID (FK, nullable) | Selected organization | `3f6c2b9c-7c2e-4e72-8a7b-8c8a2a44d2d1` |
| questionnaire_attempts | status | TEXT | `in_progress / submitted` | `submitted` |
| questionnaire_attempts | started_at | TIMESTAMPTZ | Start time | `2026-01-13T02:12:00Z` |
| questionnaire_attempts | submitted_at | TIMESTAMPTZ | Submit time | `2026-01-13T02:16:00Z` |
| questionnaire_attempts | ip_address | INET | Optional anti-abuse logging | `203.0.113.10` |
| questionnaire_attempts | user_agent | TEXT | Optional | `Mozilla/5.0 ...` |

---

## questionnaire_attempt_user_info
(Snapshot values filled in “User Info Step”)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_attempt_user_info | id | UUID (PK) | Row ID | `f1e2d3c4-b5a6-4c7d-8e9f-0a1b2c3d4e5f` |
| questionnaire_attempt_user_info | attempt_id | UUID (FK) | Attempt | `e2f3a4b5-c6d7-4e8f-9a0b-1c2d3e4f5a6b` |
| questionnaire_attempt_user_info | user_field_key | TEXT | Field key snapshot | `name` |
| questionnaire_attempt_user_info | label_snapshot | TEXT | Label at time of submit | `Full Name` |
| questionnaire_attempt_user_info | value_text | TEXT | Stored value (string) | `Rina Putri` |
| questionnaire_attempt_user_info | value_json | JSONB | For complex values (optional) | `null` |
| questionnaire_attempt_user_info | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:16:00Z` |

---

## questionnaire_answers
(Stores chosen option + score snapshot)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_answers | id | UUID (PK) | Answer ID | `aabbccdd-1122-4333-8444-556677889900` |
| questionnaire_answers | attempt_id | UUID (FK) | Attempt | `e2f3a4b5-c6d7-4e8f-9a0b-1c2d3e4f5a6b` |
| questionnaire_answers | question_id | UUID (FK) | Question | `d6c4b1a0-2c3d-4e5f-8a9b-0c1d2e3f4a5b` |
| questionnaire_answers | option_id | UUID (FK) | Selected option | `f7a8b9c0-1d2e-4f3a-8b9c-0d1e2f3a4b5c` |
| questionnaire_answers | score_value_snapshot | INT | Snapshot of option score | `2` |
| questionnaire_answers | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:16:00Z` |

---

## questionnaire_results
(Computed scores + meaning snapshot)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| questionnaire_results | id | UUID (PK) | Result ID | `0a9b8c7d-6e5f-4d3c-2b1a-0f9e8d7c6b5a` |
| questionnaire_results | attempt_id | UUID (FK, unique) | One result per attempt | `e2f3a4b5-c6d7-4e8f-9a0b-1c2d3e4f5a6b` |
| questionnaire_results | scoring_type_snapshot | TEXT | Snapshot | `multi_dimension` |
| questionnaire_results | scores_json | JSONB | Scores output | `{"visual":18,"auditory":24,"kinesthetic":9}` |
| questionnaire_results | bands_json | JSONB | Bands output | `{"visual":"Medium","auditory":"High","kinesthetic":"Low"}` |
| questionnaire_results | result_label | TEXT | Final label | `AUDITORY` |
| questionnaire_results | meaning_id | UUID (FK, nullable) | Meaning matched | `b0c1d2e3-f4a5-4b6c-8d9e-0f1a2b3c4d5e` |
| questionnaire_results | meaning_snapshot | TEXT | Frozen meaning text | `Anda lebih mudah belajar lewat suara...` |
| questionnaire_results | recommendations_snapshot | JSONB | Frozen recommendations | `["Rekam ulang materi","Diskusi kelompok"]` |
| questionnaire_results | computed_at | TIMESTAMPTZ | Compute time | `2026-01-13T02:16:01Z` |

---

## admin_users
(If you need Admin login inside this app)

| Table name | Column | Field (type) | Description | Example value |
|---|---|---|---|---|
| admin_users | id | UUID (PK) | Admin user ID | `11112222-3333-4444-5555-666677778888` |
| admin_users | email | TEXT (unique) | Admin email | `admin@superbazi.com` |
| admin_users | password_hash | TEXT | Hashed password | `$2b$10$...` |
| admin_users | name | TEXT | Display name | `Super Admin` |
| admin_users | role | TEXT | `super_admin` | `super_admin` |
| admin_users | status | TEXT | `active/disabled` | `active` |
| admin_users | created_at | TIMESTAMPTZ | Created time | `2026-01-13T02:10:00Z` |
| admin_users | updated_at | TIMESTAMPTZ | Updated time | `2026-01-13T02:10:00Z` |

---

# Recommended Indexes (Phase 1)
- `organization_users(email)` unique
- `questionnaires(code)` unique
- `questionnaire_questions(questionnaire_id, sort_order)`
- `questionnaire_options(question_id, sort_order)`
- `questionnaire_attempts(organization_id, submitted_at)`
- `questionnaire_attempts(questionnaire_id, submitted_at)`
- `questionnaire_results(result_label)`
- `questionnaire_answers(attempt_id)`
- `questionnaire_attempt_user_info(attempt_id)`
