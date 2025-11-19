# Backend TODO - STMIK Tazkia API

## Status: NOT STARTED (Deferred to Phase 2)

The backend will be implemented after the marketing site (frontend Phase 3) is complete.

**Target Platform:** VPS with Node.js + PostgreSQL
**Framework:** Express.js
**Cost:** $5-10/month

---

## Phase 2: Backend Development

### 1. Project Setup
- [ ] Initialize Express.js project
  - [ ] `npm init`
  - [ ] Install dependencies (express, pg, bcrypt, jsonwebtoken, etc.)
  - [ ] Configure TypeScript (optional but recommended)
  - [ ] Create `src/` directory structure
- [ ] Set up environment configuration
  - [ ] Create `.env.example`
  - [ ] Document required variables (DATABASE_URL, JWT_SECRET, etc.)
  - [ ] Install dotenv
- [ ] Create entry point (`src/index.js` or `src/server.js`)
  - [ ] Express server setup
  - [ ] Middleware configuration (helmet, cors, body-parser)
  - [ ] Error handling middleware
  - [ ] Health check endpoint

### 2. Database Setup
- [ ] Design database schema
  - [ ] `users` table (id, email, name, password_hash, provider, role, etc.)
  - [ ] `applications` table (id, user_id, program, status, submitted_at, etc.)
  - [ ] `sessions` table (optional - if using Option B session management)
  - [ ] Add indexes for performance
- [ ] Set up migration system
  - [ ] Install migration tool (node-pg-migrate or knex)
  - [ ] Create initial migration files
  - [ ] Create seed data for testing
- [ ] Create database connection module
  - [ ] Connection pooling configuration
  - [ ] Query helper functions
  - [ ] Transaction support

### 3. Authentication System
- [ ] JWT utilities (`src/utils/jwt.js`)
  - [ ] `generateToken(payload)` - create JWT
  - [ ] `verifyToken(token)` - verify and decode JWT
  - [ ] Configure JWT_SECRET and expiration (7 days)
- [ ] Password utilities (`src/utils/password.js`)
  - [ ] `hashPassword(password)` - bcrypt hashing
  - [ ] `comparePassword(password, hash)` - verify password
  - [ ] Use 10 salt rounds
- [ ] Authentication middleware (`src/middleware/auth.js`)
  - [ ] `authenticateToken` - verify JWT from Authorization header
  - [ ] `requireRole(['staff'])` - role-based access control
  - [ ] Attach user to `req.user`

### 4. Authentication Routes (`src/routes/auth.js`)
- [ ] `POST /auth/register`
  - [ ] Validate input (email, password, name)
  - [ ] Check if email exists
  - [ ] Hash password
  - [ ] Create user in database
  - [ ] Generate JWT
  - [ ] Return token + user data (no password)
- [ ] `POST /auth/login`
  - [ ] Validate input (email, password)
  - [ ] Find user by email
  - [ ] Verify password
  - [ ] Generate JWT
  - [ ] Return token + user data
- [ ] `POST /auth/google`
  - [ ] Verify Google ID token from BFF
  - [ ] Extract user info (email, name, provider_id)
  - [ ] Find or create user
  - [ ] Check domain for staff role (`@youruni.edu`)
  - [ ] Generate JWT
  - [ ] Return token + user data
- [ ] `POST /auth/logout` (optional if using sessions)
  - [ ] Invalidate session in database
  - [ ] Return success

### 5. User Routes (`src/routes/users.js`)
- [ ] `GET /users/me`
  - [ ] Require authentication
  - [ ] Return current user info (from JWT)
  - [ ] Exclude password_hash
- [ ] `PATCH /users/me`
  - [ ] Require authentication
  - [ ] Update user profile (name, etc.)
  - [ ] Validate input
  - [ ] Return updated user
- [ ] `GET /users` (admin only)
  - [ ] Require staff role
  - [ ] List all users
  - [ ] Pagination support
  - [ ] Filter by role
- [ ] `PATCH /users/:id/role` (admin only)
  - [ ] Require staff role
  - [ ] Update user role
  - [ ] Validate role value

### 6. Application Routes (`src/routes/applications.js`)
- [ ] `POST /applications`
  - [ ] Require authentication
  - [ ] Validate application data
  - [ ] Store in database
  - [ ] Set status = 'pending'
  - [ ] Return created application
- [ ] `GET /applications`
  - [ ] Require authentication
  - [ ] If registrant: return only user's applications
  - [ ] If staff: return all applications (with filters)
  - [ ] Support pagination
  - [ ] Support filtering by status/program
- [ ] `GET /applications/:id`
  - [ ] Require authentication
  - [ ] Check ownership (registrant can only view their own)
  - [ ] Staff can view all
  - [ ] Return application details
- [ ] `PATCH /applications/:id`
  - [ ] Require authentication
  - [ ] Only allow updates to 'pending' applications
  - [ ] User can only update their own
  - [ ] Validate input
  - [ ] Return updated application
- [ ] `PATCH /applications/:id/status` (admin only)
  - [ ] Require staff role
  - [ ] Update status (approve/reject)
  - [ ] Add admin notes
  - [ ] Return updated application
- [ ] `DELETE /applications/:id`
  - [ ] Require authentication
  - [ ] Only allow deletion of 'pending' drafts
  - [ ] User can only delete their own
  - [ ] Soft delete or hard delete

### 7. File Upload System
- [ ] Choose storage solution
  - [ ] Option A: VPS local storage
  - [ ] Option B: Cloudflare R2
  - [ ] Document pros/cons
- [ ] Set up file upload handler
  - [ ] Install multer or similar
  - [ ] Configure upload directory
  - [ ] File size limits (e.g., 5MB per file)
  - [ ] Allowed file types (PDF, JPG, PNG)
- [ ] Create file routes (`src/routes/files.js`)
  - [ ] `POST /files/upload`
    - [ ] Require authentication
    - [ ] Validate file type/size
    - [ ] Generate unique filename
    - [ ] Store file
    - [ ] Return file URL/ID
  - [ ] `GET /files/:id`
    - [ ] Require authentication
    - [ ] Check ownership/permissions
    - [ ] Stream file to client
  - [ ] `DELETE /files/:id`
    - [ ] Require authentication
    - [ ] Check ownership
    - [ ] Delete file from storage

### 8. Validation & Security
- [ ] Install express-validator
- [ ] Create validation schemas
  - [ ] User registration validation
  - [ ] Login validation
  - [ ] Application submission validation
  - [ ] File upload validation
- [ ] Security middleware
  - [ ] Install helmet (security headers)
  - [ ] Configure CORS (allow frontend domain only)
  - [ ] Install express-rate-limit
  - [ ] Rate limit auth endpoints (5 attempts per 15 min)
  - [ ] Rate limit API endpoints (100 requests per 15 min)
- [ ] Input sanitization
  - [ ] Sanitize all user inputs
  - [ ] Prevent SQL injection (use parameterized queries)
  - [ ] Prevent XSS attacks

### 9. Error Handling
- [ ] Create error classes (`src/utils/errors.js`)
  - [ ] `ValidationError`
  - [ ] `AuthenticationError`
  - [ ] `AuthorizationError`
  - [ ] `NotFoundError`
- [ ] Global error handler middleware
  - [ ] Log errors
  - [ ] Return appropriate HTTP status codes
  - [ ] Hide sensitive error details in production
  - [ ] Return consistent error format

### 10. Testing
- [ ] Set up testing framework (Jest or Mocha)
- [ ] Write unit tests
  - [ ] JWT utilities
  - [ ] Password utilities
  - [ ] Validation functions
- [ ] Write integration tests
  - [ ] Auth endpoints
  - [ ] User endpoints
  - [ ] Application endpoints
  - [ ] File upload endpoints
- [ ] Test coverage goals
  - [ ] Critical paths: 100%
  - [ ] Overall: 80%+

### 11. Documentation
- [ ] API documentation
  - [ ] Document all endpoints
  - [ ] Request/response examples
  - [ ] Authentication requirements
  - [ ] Error responses
  - [ ] Use Swagger/OpenAPI (optional)
- [ ] Database documentation
  - [ ] ER diagram
  - [ ] Table descriptions
  - [ ] Index strategy
- [ ] Environment setup guide
  - [ ] Required environment variables
  - [ ] Database setup instructions
  - [ ] Migration instructions

---

## Phase 4: BFF Layer (Cloudflare Workers)

**Note:** BFF layer will be implemented in `frontend/functions/` directory

### Required BFF Functions
- [ ] `functions/auth/login.ts` - proxy to backend, set HttpOnly cookie
- [ ] `functions/auth/register.ts` - proxy to backend, set HttpOnly cookie
- [ ] `functions/auth/google/login.ts` - initiate Google OAuth
- [ ] `functions/auth/google/callback.ts` - handle OAuth callback, verify token, proxy to backend
- [ ] `functions/auth/logout.ts` - clear cookie
- [ ] `functions/applications/submit.ts` - extract JWT from cookie, proxy to backend
- [ ] `functions/applications/list.ts` - extract JWT, proxy to backend
- [ ] `functions/users/me.ts` - extract JWT, proxy to backend
- [ ] `functions/files/upload.ts` - handle file upload, proxy to backend

See `frontend/TODO.md` for BFF layer details.

---

## Phase 6: Deployment

### VPS Setup
- [ ] Provision VPS (2GB RAM minimum)
- [ ] Install Node.js 20+
- [ ] Install PostgreSQL 14+
- [ ] Install pm2 for process management
- [ ] Install nginx as reverse proxy
- [ ] Configure firewall (UFW)
  - [ ] Allow SSH (port 22)
  - [ ] Allow HTTP (port 80)
  - [ ] Allow HTTPS (port 443)
- [ ] Set up SSL with Let's Encrypt
  - [ ] Install certbot
  - [ ] Generate SSL certificate
  - [ ] Auto-renewal configuration

### Backend Deployment
- [ ] Clone repository to VPS
- [ ] Install production dependencies
- [ ] Create `.env` file with production values
- [ ] Run database migrations
- [ ] Start application with pm2
  - [ ] `pm2 start src/index.js --name campus-backend`
  - [ ] `pm2 save`
  - [ ] `pm2 startup` (auto-restart on reboot)
- [ ] Configure nginx reverse proxy
  - [ ] Proxy `/api/*` to backend
  - [ ] SSL termination
  - [ ] Request logging

### Monitoring
- [ ] Set up logging
  - [ ] Application logs (pm2 logs)
  - [ ] Nginx access/error logs
  - [ ] Database logs
- [ ] Set up monitoring
  - [ ] pm2 monit for resource usage
  - [ ] Database connection monitoring
  - [ ] Error tracking (optional: Sentry)
- [ ] Set up backups
  - [ ] Daily database backups
  - [ ] File storage backups
  - [ ] Backup retention policy (7 days)

---

## Dependencies

### Core
```json
{
  "express": "^4.18.0",
  "pg": "^8.11.0",
  "bcrypt": "^5.1.0",
  "jsonwebtoken": "^9.0.0",
  "dotenv": "^16.0.0"
}
```

### Security
```json
{
  "helmet": "^7.0.0",
  "cors": "^2.8.5",
  "express-rate-limit": "^7.0.0",
  "express-validator": "^7.0.0"
}
```

### File Upload
```json
{
  "multer": "^1.4.5"
}
```

### Database Migrations
```json
{
  "node-pg-migrate": "^6.2.0"
}
```

### Testing
```json
{
  "jest": "^29.0.0",
  "supertest": "^6.3.0"
}
```

---

## Timeline Estimate

- **Setup & Database:** 2-3 days
- **Authentication System:** 2-3 days
- **API Routes:** 3-4 days
- **File Upload:** 1-2 days
- **Security & Validation:** 1-2 days
- **Testing:** 2-3 days
- **Documentation:** 1 day

**Total: 12-18 days (~2.5-4 weeks)**

---

## Success Criteria

- [ ] All endpoints functional and tested
- [ ] Authentication working (both email/password and Google OIDC)
- [ ] File uploads working
- [ ] Database properly indexed and optimized
- [ ] Security measures in place (rate limiting, CORS, input validation)
- [ ] Error handling comprehensive
- [ ] API documentation complete
- [ ] Deployed to VPS and accessible
- [ ] SSL certificate active
- [ ] Backups configured
