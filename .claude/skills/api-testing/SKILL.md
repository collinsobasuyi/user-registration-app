# Skill: API Testing

Use this skill to create and run manual API test scenarios for AuthBridge.

## Test Scope

Endpoints:

- GET /
- POST /api/register
- GET /api/users
- GET /api/users/{id}
- PUT /api/users/{id}
- PATCH /api/users/{id}
- DELETE /api/users/{id}

## Required Test Scenarios

- Health check returns 200
- Valid registration returns 201
- Duplicate email returns 400
- Invalid email returns validation error
- Missing password returns validation error
- Get existing user returns 200
- Get missing user returns 404
- Update user with duplicate email returns 400
- Delete existing user returns 204
- Delete missing user returns 404

## Expected Output

| Test ID | Endpoint | Scenario | Test Data | Expected Result | Status |
|---|---|---|---|---|---|
