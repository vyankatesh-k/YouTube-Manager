# Database Schema – AIM 2026

## Overview

The database is designed to support a clean separation between users, creator profiles, activities, and daily execution logs.

PostgreSQL is used for its relational integrity and structured querying capabilities.

---

## Tables

### users
Stores authentication-related information.

| Column | Type | Description |
|------|------|------------|
| id | UUID (PK) | Unique user identifier |
| name | VARCHAR | User name |
| email | VARCHAR (Unique) | Login email |
| created_at | TIMESTAMP | Account creation time |

---

### creator_profiles
Stores creator-specific metadata.

| Column | Type | Description |
|------|------|------------|
| id | UUID (PK) | Profile identifier |
| user_id | UUID (FK) | References users.id |
| channel_name | VARCHAR | YouTube channel name |
| niche | VARCHAR | Content niche |
| weekly_target | INTEGER | Target uploads per week |

---

### activities
Defines activity types.

| Column | Type | Description |
|------|------|------------|
| id | UUID (PK) | Activity identifier |
| name | VARCHAR | Activity name |
| is_default | BOOLEAN | System-defined or user-created |

---

### daily_logs
Stores daily execution data.

| Column | Type | Description |
|------|------|------------|
| id | UUID (PK) | Log identifier |
| user_id | UUID (FK) | References users.id |
| date | DATE | Log date |
| planned_activities | JSONB | Planned activities |
| completed_activities | JSONB | Completed activities |
| time_spent | INTEGER | Time spent in minutes |
| status | VARCHAR | DONE / PARTIAL / MISSED |
| reflection | TEXT | End-of-day reflection |
