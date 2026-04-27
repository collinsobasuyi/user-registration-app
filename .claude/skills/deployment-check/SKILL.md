# Skill: Deployment Check

Use this skill before deploying AuthBridge.

## Review Scope

### Frontend on Vercel

Check:

- Root directory is `frontend`
- Build command works
- `NEXT_PUBLIC_API_URL` is set
- No local-only backend URL in production
- Registration form points to deployed backend

### Backend on Railway

Check:

- Start command is correct
- `DATABASE_URL` is set
- Backend can connect to Neon
- CORS includes deployed Vercel URL
- Swagger docs load
- Register endpoint works

### Database on Neon

Check:

- Connection string includes `sslmode=require`
- Users table is created
- Email unique constraint exists
- Passwords are hashed

## Expected Output

| Deployment Area | Check | Pass/Fail | Comments | Fix |
|---|---|---|---|---|
| Vercel |  |  |  |  |
| Railway |  |  |  |  |
| Neon |  |  |  |  |
| Environment Variables |  |  |  |  |
