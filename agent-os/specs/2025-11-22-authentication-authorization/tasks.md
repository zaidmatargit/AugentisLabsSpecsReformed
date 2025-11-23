# Task Breakdown: Authentication & Authorization

## Overview
Total Tasks: 22
Estimated Effort: L (Large - critical security infrastructure with multiple auth flows)

## Task List

### Database Layer

#### Task Group 1: User and Role Data Models
**Dependencies:** None

- [ ] 1.0 Complete auth database layer
  - [ ] 1.1 Write 2-8 focused tests for auth models
    - Limit to 2-8 highly focused tests maximum
    - Test critical model behaviors (user creation, role assignment, tenant isolation)
    - Skip exhaustive coverage of all methods
  - [ ] 1.2 Create User model with Prisma
    - Fields: id, email, password_hash, email_verified, is_active, last_login, failed_login_attempts
    - Validations: email format, password not stored plaintext
    - Leverage Supabase Auth integration
  - [ ] 1.3 Create Role model
    - Fields: id, name, description, permissions_json
    - Roles: Admin, Member, Viewer
    - Support role hierarchy and permissions
  - [ ] 1.4 Create UserRole model (junction table)
    - Fields: id, user_id, role_id, org_id, assigned_at, assigned_by
    - Support per-organization role assignments
  - [ ] 1.5 Create Session model
    - Fields: id, user_id, token_hash, expires_at, remember_me, revoked_at
    - Track active sessions for revocation
  - [ ] 1.6 Create migrations for auth tables
    - Add indexes for: email (unique), org_id, role_id
    - Foreign keys: user_id → users, role_id → roles, org_id → organizations
    - Add RLS policies for multi-tenant isolation
  - [ ] 1.7 Set up model associations
    - User has_many UserRoles
    - User has_many Sessions
    - UserRole belongs_to User, Role, Organization
  - [ ] 1.8 Ensure database layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify migrations run successfully with RLS
    - Do NOT run the entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- User and role models pass validation tests
- Migrations run successfully with RLS policies
- Multi-tenant role assignment structure works

### API Layer - OAuth

#### Task Group 2: OAuth Integration
**Dependencies:** Task Group 1

- [ ] 2.0 Complete OAuth authentication
  - [ ] 2.1 Write 2-8 focused tests for OAuth flows
    - Limit to 2-8 highly focused tests maximum
    - Test critical OAuth operations (provider redirect, callback handling, token exchange)
    - Skip exhaustive testing of all edge cases
  - [ ] 2.2 Configure Supabase Auth providers
    - Enable Google, GitHub, Microsoft OAuth via Supabase dashboard
    - Configure OAuth app credentials (client ID, secret)
    - Set callback URLs for each provider
  - [ ] 2.3 Create OAuth callback controller
    - Route: GET /auth/callback/:provider
    - Handle authorization code exchange for tokens
    - Create or update user record in database
    - Reuse pattern from: api/deps.py for dependency injection
  - [ ] 2.4 Implement OAuth error handling
    - Handle denied permissions scenario
    - Handle cancelled flows (user clicks "cancel")
    - Handle token exchange failures
    - Return user-friendly error messages
  - [ ] 2.5 Create OAuth service layer
    - Method: handleOAuthCallback(provider, code)
    - Exchange code for tokens via Supabase
    - Create session and JWT
    - Assign default role (Member) to new users
  - [ ] 2.6 Ensure OAuth tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify OAuth flows work for each provider
    - Do NOT run the entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- OAuth works for Google, GitHub, Microsoft
- Callback handling successful
- User created/updated on OAuth login
- Errors handled gracefully

### API Layer - Email/Password

#### Task Group 3: Email/Password Authentication
**Dependencies:** Task Group 1

- [ ] 3.0 Complete email/password authentication
  - [ ] 3.1 Write 2-8 focused tests for email/password auth
    - Limit to 2-8 highly focused tests maximum
    - Test critical operations (registration, login, password reset, account lockout)
    - Skip exhaustive edge case testing
  - [ ] 3.2 Create registration endpoint
    - Route: POST /auth/register
    - Validate email format and password complexity (min 8 chars, uppercase, number, special char)
    - Hash password with bcrypt
    - Send email verification via Supabase
  - [ ] 3.3 Create login endpoint
    - Route: POST /auth/login
    - Validate credentials against password hash
    - Implement account lockout after 5 failed attempts
    - Generate JWT session token
  - [ ] 3.4 Create email verification endpoint
    - Route: GET /auth/verify-email/:token
    - Verify time-limited token
    - Update email_verified flag
  - [ ] 3.5 Create password reset workflow
    - Route: POST /auth/reset-password (request)
    - Route: POST /auth/reset-password/confirm (submit new password)
    - Generate time-limited reset tokens (1 hour expiry)
    - Send reset email via Supabase/SendGrid
  - [ ] 3.6 Implement account lockout logic
    - Increment failed_login_attempts on failed login
    - Lock account (is_active = false) after 5 failures
    - Send lockout notification email
    - Require manual unlock or password reset
  - [ ] 3.7 Ensure email/password auth tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify registration, login, reset flows work
    - Do NOT run the entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Registration with email verification works
- Login with password validation successful
- Password reset workflow functional
- Account lockout after 5 failed attempts enforced

### API Layer - RBAC

#### Task Group 4: Role-Based Access Control
**Dependencies:** Task Groups 1, 2, 3

- [ ] 4.0 Complete RBAC system
  - [ ] 4.1 Write 2-8 focused tests for RBAC
    - Limit to 2-8 highly focused tests maximum
    - Test critical RBAC operations (role assignment, permission checks, route protection)
    - Skip exhaustive permission combination testing
  - [ ] 4.2 Create role assignment endpoints
    - Route: POST /roles/assign (assign role to user)
    - Route: DELETE /roles/revoke (revoke role from user)
    - Require Admin role to assign roles
    - Support org-specific role assignments
  - [ ] 4.3 Create role middleware for route protection
    - Decorator: @RequiresRole('Admin') or @RequiresAnyRole(['Admin', 'Member'])
    - Extract user role from JWT claims
    - Return 403 Forbidden if insufficient permissions
    - Reuse pattern from: api/deps.py dependency injection
  - [ ] 4.4 Implement permission checking service
    - Method: checkPermission(user, permission, resource)
    - Validate user has required role
    - Validate user belongs to resource's organization (tenant isolation)
  - [ ] 4.5 Define role permissions
    - Admin: full CRUD on all resources within org
    - Member: CRUD on ventures they created, read on org ventures
    - Viewer: read-only access to org ventures
  - [ ] 4.6 Ensure RBAC tests pass
    - Run ONLY the 2-8 tests written in 4.1
    - Verify role assignment and permission checks work
    - Do NOT run the entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 4.1 pass
- Role assignment endpoints functional
- Route protection middleware works
- Permission checks enforce role restrictions
- Org-specific role assignments supported

### API Layer - Session Management

#### Task Group 5: Session Management
**Dependencies:** Task Groups 2, 3

- [ ] 5.0 Complete session management
  - [ ] 5.1 Write 2-8 focused tests for session management
    - Limit to 2-8 highly focused tests maximum
    - Test critical session operations (token generation, refresh, revocation)
    - Skip exhaustive session edge case testing
  - [ ] 5.2 Implement JWT token generation
    - Use Supabase JWT with custom claims (user_id, org_id, roles)
    - Set expiration: 1 hour (default), 30 days (remember me)
    - Sign tokens with secret key from Supabase config
  - [ ] 5.3 Implement token refresh endpoint
    - Route: POST /auth/refresh
    - Accept refresh token, issue new access token
    - Extend session expiration
  - [ ] 5.4 Implement logout endpoint
    - Route: POST /auth/logout
    - Revoke current session (set revoked_at timestamp)
    - Clear client-side cookies/tokens
  - [ ] 5.5 Implement session revocation
    - Route: POST /auth/revoke-all-sessions (admin only)
    - Revoke all sessions for a user
    - Force re-authentication
  - [ ] 5.6 Add "remember me" functionality
    - Checkbox in login form
    - Extend JWT expiration to 30 days if checked
    - Use longer-lived refresh tokens
  - [ ] 5.7 Ensure session management tests pass
    - Run ONLY the 2-8 tests written in 5.1
    - Verify token generation, refresh, revocation work
    - Do NOT run the entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 5.1 pass
- JWT token generation works with custom claims
- Token refresh extends sessions correctly
- Logout revokes active sessions
- "Remember me" extends session duration

### Multi-Tenant Authorization

#### Task Group 6: Multi-Tenant Security
**Dependencies:** Task Groups 1, 4

- [ ] 6.0 Complete multi-tenant authorization
  - [ ] 6.1 Write 2-8 focused tests for multi-tenant security
    - Limit to 2-8 highly focused tests maximum
    - Test critical tenant isolation scenarios (cross-tenant access prevention, RLS enforcement)
    - Skip exhaustive multi-tenant edge cases
  - [ ] 6.2 Implement tenant context extraction
    - Extract org_id from JWT claims
    - Validate tenant context in all API requests
    - Reject requests without valid tenant context
  - [ ] 6.3 Create RLS policies for all tables
    - Policy: users can only access data from their org_id
    - Apply to: projects, assessments, competitors, all venture data
    - Test RLS policies with different tenant users
  - [ ] 6.4 Implement cross-tenant access prevention
    - Validate org_id matches JWT claim in all queries
    - Return 403 Forbidden on cross-tenant access attempts
    - Log cross-tenant access attempts for security monitoring
  - [ ] 6.5 Add org_id to all data models
    - Update existing models to include org_id
    - Create migration to add org_id to all tenant-scoped tables
    - Set org_id as required field
  - [ ] 6.6 Ensure multi-tenant security tests pass
    - Run ONLY the 2-8 tests written in 6.1
    - Verify cross-tenant access is blocked
    - Confirm RLS policies enforce isolation
    - Do NOT run the entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 6.1 pass
- Tenant context extracted from JWT successfully
- RLS policies prevent cross-tenant data access
- All API requests validate tenant context
- Cross-tenant access attempts blocked and logged

### Frontend Components

#### Task Group 7: Authentication UI
**Dependencies:** Task Groups 2, 3

- [ ] 7.0 Complete authentication UI
  - [ ] 7.1 Write 2-8 focused tests for auth UI components
    - Limit to 2-8 highly focused tests maximum
    - Test critical UI behaviors (login form, OAuth buttons, password reset)
    - Skip exhaustive UI component testing
  - [ ] 7.2 Create login/signup form component
    - Email and password fields
    - Social login buttons (Google, GitHub, Microsoft)
    - "Remember me" checkbox
    - Link to password reset
  - [ ] 7.3 Create OAuth button components
    - Styled buttons for each provider (branded colors/icons)
    - Handle OAuth redirect to Supabase
    - Display loading state during authentication
  - [ ] 7.4 Create password strength indicator
    - Visual indicator during registration (weak/medium/strong)
    - Show password requirements (8+ chars, uppercase, number, special char)
    - Real-time validation feedback
  - [ ] 7.5 Create email verification screens
    - Success screen after registration
    - Email sent confirmation
    - Resend verification email option
  - [ ] 7.6 Create password reset form
    - Request reset email form
    - New password submission form (from reset link)
    - Success confirmation
  - [ ] 7.7 Apply responsive design
    - Mobile: 320px - 768px (stacked forms)
    - Tablet: 768px - 1024px (centered layout)
    - Desktop: 1024px+ (split screen with branding)
  - [ ] 7.8 Ensure UI tests pass
    - Run ONLY the 2-8 tests written in 7.1
    - Verify critical auth UI flows work
    - Do NOT run the entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 7.1 pass
- Login/signup forms functional
- OAuth buttons redirect correctly
- Password strength indicator works
- Email verification and reset flows complete

### Testing

#### Task Group 8: Test Review & Gap Analysis
**Dependencies:** Task Groups 1-7

- [ ] 8.0 Review existing tests and fill critical gaps only
  - [ ] 8.1 Review tests from Task Groups 1-7
    - Review tests from database, OAuth, email/password, RBAC, session, multi-tenant, UI engineers
    - Total existing tests: approximately 14-56 tests
  - [ ] 8.2 Analyze test coverage gaps for Authentication feature only
    - Identify critical user workflows lacking coverage
    - Focus on end-to-end auth workflows (register → verify → login → access protected resource)
    - Prioritize security testing (cross-tenant, role enforcement, session security)
  - [ ] 8.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill critical gaps
    - Focus on: complete auth workflows, multi-tenant isolation, RBAC enforcement
    - Do NOT write comprehensive coverage
  - [ ] 8.4 Run feature-specific tests only
    - Run ONLY tests related to Authentication feature
    - Expected total: approximately 24-66 tests maximum
    - Verify critical auth workflows pass

**Acceptance Criteria:**
- All Authentication-specific tests pass (approximately 24-66 tests total)
- Critical auth workflows covered end-to-end
- No more than 10 additional tests added
- Testing focused exclusively on Authentication requirements

## Execution Order

Recommended implementation sequence:
1. Database Layer (Task Group 1) - User and role models
2. API Layer - OAuth (Task Group 2) - OAuth integration
3. API Layer - Email/Password (Task Group 3) - Email/password auth
4. API Layer - RBAC (Task Group 4) - Role-based access control
5. API Layer - Session Management (Task Group 5) - Session handling
6. Multi-Tenant Authorization (Task Group 6) - Multi-tenant security
7. Frontend Components (Task Group 7) - Authentication UI
8. Testing (Task Group 8) - Test review and gap analysis
