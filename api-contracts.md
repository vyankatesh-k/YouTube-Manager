# API Contracts – AIM 2026

## Authentication

### POST /auth/login
Authenticates a user.

Request:
{
  "email": "user@example.com",
  "password": "password"
}

Response:
{
  "access_token": "jwt-token",
  "user_id": "uuid"
}

---

## Creator Profile

### GET /profile
Fetch creator profile.

### POST /profile
Create creator profile.

### PUT /profile
Update creator profile.

---

## Activities

### GET /activities
Returns default and user-defined activities.

### POST /activities
Request:
{
  "name": "Thumbnail Design"
}

---

## Daily Logs

### POST /daily-log
Creates or updates a daily log.

Request:
{
  "date": "2026-01-10",
  "planned_activities": ["Script Writing", "Editing"],
  "completed_activities": ["Script Writing"],
  "time_spent": 120,
  "status": "PARTIAL",
  "reflection": "Editing skipped due to fatigue"
}

---

### GET /daily-log?date=YYYY-MM-DD
Fetch daily log for a specific date.

---

## Weekly Summary

### GET /weekly-summary
Returns last 7 days of execution data with status indicators.
