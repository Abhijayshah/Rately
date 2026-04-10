# QA & Software Testing Interview Guide 
## Project: Rately 
## Role: Software Testing / QA Engineer 
## Prepared by: Abhijay (Senior Full-Stack Engineer)
## Last Updated: 2026-04-11 
> This guide is written specifically for 
> interview preparation based on real project experience. 
> Every example is from actual development work. 

---

## TABLE OF CONTENTS 

1. [Introduction to Software Testing](#1-introduction-to-software-testing)
2. [Types of Testing](#2-types-of-testing)
3. [Testing Levels](#3-testing-levels)
4. [Test Case Design Techniques](#4-test-case-design-techniques)
5. [Bug Life Cycle and Bug Reports](#5-bug-life-cycle-and-bug-reports)
6. [Testing Methodologies](#6-testing-methodologies)
7. [API Testing](#7-api-testing)
8. [Database Testing](#8-database-testing)
9. [UI and Frontend Testing](#9-ui-and-frontend-testing)
10. [Performance Testing Concepts](#10-performance-testing-concepts)
11. [Security Testing Concepts](#11-security-testing-concepts)
12. [Test Documentation Templates](#12-test-documentation-templates)
13. [Testing Tools Overview](#13-testing-tools-overview)
14. [Rately - Complete Manual Test Cases](#14-rately---complete-manual-test-cases)
15. [Rately - API Test Cases](#15-rately---api-test-cases)
16. [Rately - End to End Test Scenarios](#16-rately---end-to-end-test-scenarios)
17. [Rately - Real Bug Reports from Development](#17-rately---real-bug-reports-from-development)
18. [Rately - Complete Test Plan](#18-rately---complete-test-plan)
19. [Rately - Interview Q&A (60 questions)](#19-rately---interview-qa-60-questions)
20. [Quick Fire Round (35 questions)](#20-quick-fire-round-35-questions)
21. [Top 10 Things to Say That Impress Interviewers](#21-top-10-things-to-say-that-impress-interviewers)
22. [Common Mistakes Junior QA Engineers Make](#22-common-mistakes-junior-qa-engineers-make)

---

## 1. INTRODUCTION TO SOFTWARE TESTING

Software testing is the process of evaluating and verifying that a software application or system does what it is supposed to do. It aims to identify defects, ensure quality, and reduce the risk of failure in production.

### Why it Matters
In **Rately**, testing is crucial because it handles sensitive user data (passwords, emails) and maintains the integrity of store ratings. A bug in the rating logic could allow a user to spam a store with multiple reviews, destroying the platform's credibility.

### 7 Principles of Software Testing
1. **Testing shows the presence of defects**: We found that users could register with short names initially; testing revealed this.
2. **Exhaustive testing is impossible**: We can't test every possible string for a store name, so we focus on boundary values (20-60 characters).
3. **Early testing**: We start testing API endpoints before the frontend is even built.
4. **Defect clustering**: Most bugs in Rately were found in the role-based redirection logic.
5. **Pesticide Paradox**: Running the same login tests repeatedly won't find new bugs in the analytics module.
6. **Testing is context-dependent**: Testing the Admin Dashboard is different from testing the User Store List.
7. **Absence-of-errors fallacy**: Just because the login works doesn't mean the app is useful if the ratings don't update.

### Verification vs Validation
| Concept | Definition | Rately Example |
|---------|------------|----------------|
| **Verification** | Are we building the product right? (Reviews, walkthroughs) | Reviewing the `User.js` model to ensure the `email` field is marked as `unique`. |
| **Validation** | Are we building the right product? (Execution) | Running the app and trying to register two users with the same email to see if it fails. |

### Testing Types
- **Static Testing**: Reviewing code or documentation without execution. Example: Checking `auth.js` middleware for logical errors during a code review.
- **Dynamic Testing**: Testing by executing the code. Example: Clicking the "Submit Rating" button and checking the database update.
- **White Box**: Testing the internal logic/code. Example: Writing a unit test for the `calculateAverageRating` static method in `Rating.js`.
- **Black Box**: Testing based on requirements without knowing internal code. Example: Testing the Login form as a user would.
- **Grey Box**: A mix; testing with partial knowledge of the internals. Example: Testing the API `/api/stores` while knowing it uses a MongoDB query with regex.

---

## 2. TYPES OF TESTING

### Functional Testing
- **Unit Testing**: Testing individual components. Example: Testing a utility function that formats the "Average Rating" to one decimal place.
- **Integration Testing**: Testing how modules work together. Example: Verifying that `authController.js` correctly interacts with the `User` model to save data.
- **System Testing**: Testing the complete integrated system. Example: Testing the full flow from User Registration -> Login -> Rating a Store -> Seeing updated rating.
- **Acceptance Testing**: Verifying if the system meets business requirements. Example: Ensuring the Store Owner can see their specific analytics as requested in the PRD.
- **Regression Testing**: Testing to ensure new changes didn't break existing features. Example: After adding the "Search" feature in the Store list, re-testing the "Submit Rating" functionality.
- **Smoke Testing**: Quick test of major functionalities. Example: Checking if the Login page loads and a user can log in.
- **Sanity Testing**: Testing a specific bug fix or change. Example: Verifying that the password validation error message is now correctly displayed after a fix.

### Non-Functional Testing
- **Performance Testing**: Checking speed and stability. Example: Checking how long the Admin Dashboard takes to load when there are 1,000+ stores.
- **Load Testing**: Testing under expected user load. Example: Simulating 50 users rating stores simultaneously.
- **Stress Testing**: Testing beyond normal capacity. Example: Bombarding the API with 5,000 requests per minute to see where it breaks.
- **Security Testing**: Checking for vulnerabilities. Example: Trying to access `/api/admin/users` as a `normal_user`.
- **Usability Testing**: Checking user experience. Example: Is the "Star Rating" component intuitive to use on mobile?
- **Compatibility Testing**: Testing on different browsers/devices. Example: Checking if the Tailwind layout breaks on Safari or mobile Chrome.

---

## 3. TESTING LEVELS

| Level | What gets tested | Who tests it | Tools Used | Rately Example |
|-------|------------------|--------------|------------|----------------|
| **Unit** | Individual functions/classes | Developers | Jest | Testing `calculateAverageRating` in `Rating.js`. |
| **Integration** | Interaction between modules | Developers/QA | Supertest, Postman | Testing the `/api/auth/register` route's flow to the DB. |
| **System** | End-to-end functionality | QA | Cypress, Manual | Testing the entire Admin workflow to create a store. |
| **Acceptance** | Business requirements | Product/Users | Manual | Verifying if the "Analytics" charts meet business needs. |

---

## 4. TEST CASE DESIGN TECHNIQUES

### Equivalence Partitioning (EP)
Divide input into groups that are expected to be treated the same.
- **Example**: Name field (20-60 characters).
  - Group 1: < 20 characters (Invalid)
  - Group 2: 20-60 characters (Valid)
  - Group 3: > 60 characters (Invalid)

### Boundary Value Analysis (BVA)
Test the edges of the EP groups.
- **Example**: Password field (8-16 characters).
  - Test cases: 7 chars (Invalid), 8 chars (Valid), 15 chars (Valid), 16 chars (Valid), 17 chars (Invalid).

### Decision Table Testing
Testing complex logic with multiple inputs.
- **Example**: Login logic.
  - Email Valid? (Y/N)
  - Password Valid? (Y/N)
  - Outcome: Login Success, Error 1, Error 2.

### State Transition Testing
Testing how the system moves between states.
- **Example**: User Role update.
  - State: `normal_user` -> Admin promotes to `store_owner` -> User now has access to "My Store" dashboard.

### Error Guessing
Based on experience, guessing where bugs might hide.
- **Example**: Entering emojis in the "Address" field or rapid-clicking the "Submit" button to trigger race conditions.

---

## 5. BUG LIFE CYCLE AND BUG REPORTS

### Bug Life Cycle Flowchart
```text
New -> Open -> Assigned -> In-Progress -> Fixed -> Pending Retest -> Verified -> Closed
      |                                                                       ^
      |-> Rejected/Duplicate/Deferred -----------------------------------------|
```

### Severity vs Priority
- **Severity**: Impact on the system (Technical).
- **Priority**: Urgency to fix (Business).

| Level | Severity Example (Rately) | Priority Example (Rately) |
|-------|--------------------------|---------------------------|
| **Critical/P1** | Database crash when rating a store. | Login page showing 404 on production. |
| **High/P2** | Users can't update their password. | "Submit Rating" button is missing on mobile. |
| **Medium/P3** | Store name is truncated in the list. | Typo in the "About Us" section of the landing page. |
| **Low/P4** | Logo is slightly off-center. | Changing the hover color of a secondary button. |

---

## 6. TESTING METHODOLOGIES

### Methodologies Overview
- **Waterfall**: Sequential steps (Plan -> Design -> Build -> Test). Not used in Rately.
- **Agile**: Iterative development. We used this, building Auth first, then Stores, then Ratings.
- **V-Model**: Testing phases mapped to development phases.
- **TDD (Test Driven Development)**: Writing tests before code.
- **BDD (Behavior Driven Development)**: Writing tests in natural language (Gherkin).

### BDD Scenarios (Gherkin)
1. **Scenario**: Successful Rating Submission
   - **Given** I am logged in as a "normal_user"
   - **And** I am on the "Browse Stores" page
   - **When** I select a store and give it a 5-star rating
   - **Then** the store's average rating should update correctly

2. **Scenario**: Admin access control
   - **Given** I am logged in as a "normal_user"
   - **When** I attempt to navigate to `/admin/users`
   - **Then** I should be redirected to the "Unauthorized" page

3. **Scenario**: Password validation
   - **Given** I am on the registration page
   - **When** I enter a password "12345"
   - **Then** I should see an error "Password must be between 8 and 16 characters"

---

## 7. API TESTING

### HTTP Status Codes
| Code | Meaning | Rately Example |
|------|---------|----------------|
| **200 OK** | Success | Fetching store list. |
| **201 Created** | Resource created | Successful registration. |
| **400 Bad Request** | Validation failed | Name too short in registration. |
| **401 Unauthorized** | Missing/Invalid token | Accessing profile without logging in. |
| **403 Forbidden** | Insufficient permissions | User trying to delete another user. |
| **404 Not Found** | Resource missing | Accessing a store ID that doesn't exist. |
| **500 Internal Error** | Server crash | Database connection lost. |

### Postman Test Collection
```javascript
// Test: Successful Login
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Token is present", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.token).to.be.a('string');
});

// Test: Role Check
pm.test("User role is normal_user", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.user.role).to.eql("normal_user");
});
```

---

## 8. DATABASE TESTING

### What to Verify
1. **Data Integrity**: Does deleting a user also handle their ratings? (Orphaned data).
2. **Schema Validation**: Does the `Store` model enforce the 20-character minimum?
3. **Uniqueness**: Can two stores have the same email? (Should be blocked).

### Specific Queries
```javascript
// Check for stores with missing owners
db.stores.find({ owner: { $exists: false } });

// Validate average rating calculation
db.ratings.aggregate([
  { $match: { store: ObjectId("STORE_ID") } },
  { $group: { _id: "$store", avg: { $avg: "$rating" } } }
]);
```

---

## 9. UI AND FRONTEND TESTING

### Form Test Cases
- **Registration Form**: 
  - Test with valid data.
  - Test with existing email.
  - Test password strength (missing uppercase, missing special char).
  - Test address length (> 400 chars).
- **Rating Modal**:
  - Test clicking stars 1 through 5.
  - Test submitting without selecting a star.
  - Test closing modal without saving.

### Navigation & Routing
- Verify sidebar links change based on user role.
- Verify "Logout" clears `localStorage` and redirects to `/login`.
- Verify back-button behavior after logout (should not return to dashboard).

---

## 10. PERFORMANCE TESTING CONCEPTS

### Metrics
- **Response Time**: Time for the API to return the store list (Target: < 200ms).
- **Throughput**: Requests per second (Target: 50 req/sec).
- **Error Rate**: Percentage of failed requests (Target: < 1%).

### k6 Script Example
```javascript
import http from 'k6/http';
import { sleep } from 'k6';

export default function () {
  http.get('http://localhost:5001/api/stores');
  sleep(1);
}
```

---

## 11. SECURITY TESTING CONCEPTS

### Key Security Tests
1. **Broken Access Control**: Try accessing `/api/admin/dashboard/stats` with a `normal_user` token.
2. **Injection**: Enter `' OR '1'='1` in the login email field.
3. **JWT Security**: Try modifying the payload of the JWT token to change your role to `system_admin`.
4. **Rate Limiting**: Attempt to call the login API 100 times in a row to see if the IP is blocked.

---

## 12. TEST DOCUMENTATION TEMPLATES

### Test Plan Template (Rately)
- **Scope**: Auth, Store Browsing, Rating, Admin Dashboard.
- **Out of Scope**: Third-party email notifications (not implemented).
- **Approach**: Manual testing for UI, Postman for API, Jest for unit tests.
- **Risks**: High concurrency on rating submission could cause race conditions.

### Bug Report Template
- **Title**: [Auth] User can register with name less than 20 characters.
- **Steps**:
  1. Go to /register.
  2. Enter name "Bob".
  3. Enter valid email/password.
  4. Click Register.
- **Expected**: Error message "Name must be between 20 and 60 characters".
- **Actual**: User is registered successfully.

---

## 13. TESTING TOOLS OVERVIEW

| Category | Tool | Purpose in Rately |
|----------|------|-------------------|
| **API** | Postman / Axios | Testing endpoints and request interceptors. |
| **Automation** | Jest / Supertest | Running backend integration tests. |
| **UI** | Browser DevTools | Inspecting network requests and local storage. |
| **Performance** | Lighthouse | Checking frontend load times and SEO. |
| **Database** | MongoDB Compass | Verifying data persistence and indexes. |

---

## 14. RATELY - COMPLETE MANUAL TEST CASES

| ID | Module | Title | Preconditions | Steps | Expected Result | Priority | Type |
|----|--------|-------|---------------|-------|-----------------|----------|------|
| TC01 | Auth | Successful Register | None | 1. Enter valid 20+ char name<br>2. Valid email<br>3. Strong password | User created and redirected to dashboard | High | Positive |
| TC02 | Auth | Register - Short Name | None | 1. Enter name "Short" | Error: Name must be 20-60 chars | Medium | Negative |
| TC03 | Auth | Register - Duplicate Email | User exists | 1. Register with existing email | Error: User with this email exists | High | Negative |
| TC04 | Auth | Successful Login | User exists | 1. Enter valid credentials | JWT stored in localStorage, redirect | High | Positive |
| TC05 | Auth | Login - Invalid Password | User exists | 1. Correct email, wrong password | Error: Invalid credentials | High | Negative |
| TC06 | Stores | List Stores | Logged in | 1. Navigate to /stores | Stores displayed in cards/table | High | Positive |
| TC07 | Ratings | Submit 5-Star Rating | Logged in | 1. Click store<br>2. Select 5 stars<br>3. Submit | Rating saved, average updated | High | Positive |
| TC08 | Ratings | Duplicate Rating | Rated store | 1. Attempt to rate same store again | Error or block UI from re-rating | Medium | Negative |
| TC09 | Admin | Create User | Admin role | 1. Navigate to Admin Users<br>2. Fill form | New user appears in list | High | Positive |
| TC10 | Admin | Delete User | Admin role | 1. Click delete on user | User removed from system | Medium | Positive |
| TC11 | Admin | Stats Check | Admin role | 1. View dashboard | Total Users/Stores/Ratings match DB | Medium | Positive |
| TC12 | Owner | View Dashboard | Owner role | 1. Login as owner<br>2. View dashboard | Shows ratings for THEIR store only | High | Positive |
| TC13 | Security | Unauthorized Admin | Normal user | 1. Manual URL entry to /admin/users | Redirect to /unauthorized | High | Negative |
| TC14 | Security | Expired Token | Token > 24h | 1. Use app with old token | Redirect to /login automatically | High | Negative |
| TC15 | UI | Loading State | Slow network | 1. Click login | Spinner/Loading bar appears | Low | Positive |
| TC16 | Stores | Search Stores | Logged in | 1. Enter "Tech" in search bar | Only stores with "Tech" in name appear | Medium | Positive |
| TC17 | Stores | Empty Search | Logged in | 1. Enter "XYZABC" (no match) | "No stores found" message displayed | Medium | Negative |
| TC18 | Profile | Update Profile Name | Logged in | 1. Go to Settings<br>2. Change name to valid | Name updated in UI and DB | Medium | Positive |
| TC19 | Profile | Update Name - Too Long | Logged in | 1. Enter name > 60 chars | Error: Name must be 20-60 chars | Low | Negative |
| TC20 | Password | Update Password - Valid | Logged in | 1. Enter correct current<br>2. Enter valid new | Password updated successfully | High | Positive |
| TC21 | Password | Update Password - Wrong Old | Logged in | 1. Enter wrong current password | Error: Current password incorrect | High | Negative |
| TC22 | Admin | Filter Users by Role | Admin role | 1. Select "Store Owner" in filter | Only store owners displayed | Medium | Positive |
| TC23 | Admin | Create Store | Admin role | 1. Fill store form with valid data | New store appears in system | High | Positive |
| TC24 | Owner | Analytics Accuracy | Owner role | 1. Add new rating to store<br>2. Check owner dashboard | Total ratings count increases by 1 | High | Positive |
| TC25 | UI | Responsive Sidebar | None | 1. Shrink browser to mobile width | Sidebar hides and hamburger menu appears | Low | Positive |
| TC26 | UI | Modal Close - Escape Key | Any modal open | 1. Press ESC key | Modal closes automatically | Low | Positive |
| TC27 | Routing | Redirect from Login | Already logged in | 1. Go to /login while authenticated | Automatically redirected to /dashboard | Medium | Positive |
| TC28 | Routing | Access Protected - No Login | Not logged in | 1. Go to /dashboard | Automatically redirected to /login | High | Positive |
| TC29 | Validation | Email Format | Any form | 1. Enter "invalid-email" | Error: Provide valid email address | High | Negative |
| TC30 | Validation | Password Uppercase | Register | 1. Enter "password123!" (no upper) | Error: Password must contain uppercase | Medium | Negative |
| TC31 | Admin | Pagination Test | 11+ users | 1. Go to page 2 | Displays users 11-20 correctly | Medium | Positive |
| TC32 | Database | Soft Delete Check | Admin role | 1. Delete user<br>2. Check if ratings remain | Ratings should persist but user reference handles gracefully | Medium | Positive |
| TC33 | API | Token Header Missing | Any API | 1. Call GET /api/stores without Bearer | 401 Unauthorized returned | High | Negative |
| TC34 | API | Rate Limit Test | Any IP | 1. Call API 1000+ times | 429 Too Many Requests returned | Low | Negative |
| TC35 | Edge Case | Emojis in Address | Register | 1. Enter "123 🏠 Street" | User registered successfully (Unicode supported) | Low | Positive |

---

## 15. RATELY - API TEST CASES

1. **POST /api/auth/register**:
   - Valid body -> 201 Created.
   - Missing address -> 400 Bad Request.
   - Password no uppercase -> 400 Bad Request.
2. **GET /api/stores**:
   - Authenticated user -> 200 OK + Array.
   - No token -> 401 Unauthorized.
3. **POST /api/stores/ratings**:
   - Rating 6 -> 400 Bad Request (Max 5).
   - Valid rating -> 201 Created + Updated Store Object.
4. **GET /api/admin/dashboard/stats**:
   - Admin user -> 200 OK + Stats Object.
   - Normal user -> 403 Forbidden.
5. **PUT /api/auth/profile**:
   - Update name -> 200 OK + Updated User.
   - Empty name -> 400 Bad Request.

---

## 16. END TO END TEST SCENARIOS

1. **Scenario**: New Store Owner Onboarding
   - **Actor**: System Admin
   - **Steps**: Login -> Create User with role `store_owner` -> Create Store and assign to that owner.
   - **Outcome**: Owner can login and see their new store.
2. **Scenario**: User Rating Journey
   - **Actor**: Normal User
   - **Steps**: Register -> Login -> Browse Stores -> Search for "Tech" -> Rate 4 stars.
   - **Outcome**: My Ratings page shows the new entry.
3. **Scenario**: Password Reset Flow
   - **Actor**: Any User
   - **Steps**: Login -> Go to Settings -> Change Password -> Logout -> Login with NEW password.
   - **Outcome**: Successfully logged in with new credentials.
4. **Scenario**: Admin User Cleanup
   - **Actor**: System Admin
   - **Steps**: Login -> Navigate to Users -> Find inactive user -> Delete.
   - **Outcome**: User disappears from list and cannot log in.
5. **Scenario**: Store Owner Analytics Check
   - **Actor**: Store Owner
   - **Steps**: Login -> View Dashboard -> Observe average rating.
   - **Outcome**: Matches the manual calculation of all ratings.
6. **Scenario**: Cross-Role Access Attempt
   - **Actor**: Normal User
   - **Steps**: Login -> Manually type `/admin/analytics` in URL bar.
   - **Outcome**: Redirected to `/unauthorized` page.
7. **Scenario**: Session Expiry Handling
   - **Actor**: Any User
   - **Steps**: Login -> Wait for token expiry (or delete from localStorage) -> Refresh page.
   - **Outcome**: Automatically redirected to `/login`.
8. **Scenario**: Search and Rate
   - **Actor**: Normal User
   - **Steps**: Login -> Search "Gadgets" -> Click Star Rating -> Close Modal -> Open Modal again.
   - **Outcome**: Modal state should be clean or persist draft.
9. **Scenario**: Admin Store Creation
   - **Actor**: System Admin
   - **Steps**: Login -> Create Store -> Assign existing user as owner.
   - **Outcome**: User now sees "My Store" in their sidebar.
10. **Scenario**: Multi-User Rating
    - **Actor**: User A and User B
    - **Steps**: Both login -> Both rate same store 5 stars.
    - **Outcome**: Store's `totalRatings` increases by 2.

---

## 17. REAL BUG REPORTS FROM DEVELOPMENT

| ID | Title | Module | Severity | Root Cause | Fix Applied |
|----|-------|--------|----------|------------|-------------|
| BUG01 | CORS Error on Login | Auth | Critical | Missing production URL in CORS config | Added Render URL to `CLIENT_URL` |
| BUG02 | Rating not updating avg | Rating | High | Async race condition in Mongoose post-save | Used `statics.calculateAverageRating` |
| BUG03 | Sidebar icons missing | UI | Low | Lucide icons not imported correctly | Imported missing components |
| BUG04 | Token persist after logout | Auth | High | `localStorage.removeItem` missing | Added clear call in `logout` function |
| BUG05 | Registration fails on short name | Auth | Medium | Frontend validation missing for 20-char limit | Added `minLength: 20` to Register form |
| BUG06 | Admin stats show 0 initially | Admin | Medium | Stats API called before DB init finished | Added `await` in `startServer` for `initDb` |
| BUG07 | Rating modal doesn't close | UI | Low | `onClose` prop not passed to Modal component | Fixed prop drilling in `Stores.js` |
| BUG08 | Password update no feedback | Settings | Low | Toast notification missing on success | Added `toast.success` after API call |

---

## 18. RATELY - COMPLETE TEST PLAN

### 1. Introduction
This test plan covers the QA activities for Rately v1.0, focusing on role-based access and data integrity of the rating system.

### 2. Scope
- **In-Scope**: Authentication, Store Management, Rating System, Admin Analytics.
- **Out-of-Scope**: Mobile native apps, Third-party OAuth (Google/FB).

### 3. Test Approach
- **Manual**: Functional UI testing on Chrome/Firefox.
- **Automated**: API integration tests using Supertest for backend routes.

### 4. Risk Analysis
1. **Risk**: Database connection timeout. **Mitigation**: Implement retry logic and health checks.
2. **Risk**: Incorrect average calculation. **Mitigation**: Unit tests for the aggregation pipeline.
3. **Risk**: Token theft. **Mitigation**: Use `HttpOnly` cookies in future versions (currently using secure `localStorage`).

---

## 19. RATELY - INTERVIEW Q&A (60 QUESTIONS)

### Category A — General QA (15)
1. **[Easy] What is the difference between a Bug, Defect, and Failure?**
   - **Answer**: A bug is found by a developer, a defect by a QA, and a failure by an end-user. In Rately, I found a *defect* where the name length wasn't enforced in the frontend, preventing a *failure* where the DB would reject the request without a clear user message.
2. **[Medium] When should testing stop?**
   - **Answer**: When exit criteria are met (all P1/P2 bugs closed, 100% test case execution). In my project, I stopped when all core user journeys (Register to Rate) were verified and stable.
3. **[Easy] What is Regression Testing?**
   - **Answer**: It's re-testing existing features after a code change. For Rately, every time I modified the `Rating` model, I re-tested the Store list to ensure ratings still displayed correctly.
4. **[Medium] Explain the Bug Life Cycle.**
   - **Answer**: It starts as `New`, then `Open` when assigned. If fixed, it's `Fixed`, then `Verified` by QA before being `Closed`. If the fix doesn't work, it's `Reopened`.
5. **[Easy] What is a Test Case?**
   - **Answer**: A set of conditions/variables under which a tester determines if a system works correctly. My test cases include ID, steps, data, and expected results.
6. **[Medium] Difference between Sanity and Smoke testing?**
   - **Answer**: Smoke tests the whole system roughly; Sanity tests a specific area after a fix. After fixing the password update, I did a sanity test on the Settings page.
7. **[Easy] What is Black Box testing?**
   - **Answer**: Testing without looking at code. I did this when testing the Rately login page from a user perspective.
8. **[Medium] What is Boundary Value Analysis?**
   - **Answer**: Testing the edges of input ranges. For Rately's 20-60 char name limit, I tested 19, 20, 60, and 61 characters.
9. **[Easy] What is a Test Plan?**
   - **Answer**: A document describing the scope, approach, resources, and schedule of testing activities.
10. **[Medium] What is Exploratory Testing?**
    - **Answer**: Simultaneous learning, test design, and execution. I used this to find edge cases in the UI layout responsiveness.
11. **[Easy] What is a Test Suite?**
    - **Answer**: A collection of test cases. I organized my Rately tests into suites like "Auth Suite" and "Admin Suite".
12. **[Medium] Difference between Severity and Priority?**
    - **Answer**: Severity is technical impact (e.g., DB crash); Priority is business urgency (e.g., typo on landing page).
13. **[Easy] What is Static Testing?**
    - **Answer**: Reviewing code/docs without running it. I did this during peer reviews of our API routes.
14. **[Medium] What is Verification vs Validation?**
    - **Answer**: Verification: Are we building it right? Validation: Are we building the right thing?
15. **[Easy] What is an Edge Case?**
    - **Answer**: A problem that occurs only at extreme operating parameters. Example: A user having 1,000 ratings.

### Category B — Technical Testing (15)
16. **[Medium] How do you test a JWT-based authentication system?**
    - **Answer**: I verify that the token is stored in `localStorage`, sent in the `Authorization` header, and that the server returns 401/403 for invalid tokens.
17. **[Hard] How would you test for race conditions in the rating system?**
    - **Answer**: I'd use a tool like k6 to send multiple rating requests for the same store simultaneously and verify the `averageRating` is correct.
18. **[Medium] How do you test API status codes in Postman?**
    - **Answer**: I use `pm.response.to.have.status(200)` in the Tests tab.
19. **[Hard] How do you verify data in MongoDB after an API call?**
    - **Answer**: I use MongoDB Compass or a shell script to query the collection and ensure the fields match the API request.
20. **[Medium] What is API Mocking?**
    - **Answer**: Simulating an API response. I used this when the backend wasn't ready to test the frontend UI.
21. **[Hard] How do you test for SQL/NoSQL Injection?**
    - **Answer**: By entering special characters like `$` or `"` in input fields to see if the query breaks or returns unauthorized data.
22. **[Medium] How do you test responsive design?**
    - **Answer**: Using Chrome DevTools "Device Mode" to toggle between iPhone, iPad, and Desktop views.
23. **[Hard] How do you test a Paginated API?**
    - **Answer**: I verify the `limit` and `page` query params return the correct subset of data and total count.
24. **[Medium] What is Cross-Site Scripting (XSS)?**
    - **Answer**: Injecting malicious scripts into web pages. I tested this by entering `<script>alert(1)</script>` in the store name field.
25. **[Hard] How do you test the 'calculateAverageRating' logic?**
    - **Answer**: By manually adding 3 ratings (e.g., 3, 4, 5) and ensuring the DB reflects the average (4.0).
26. **[Medium] What is Load Testing vs Stress Testing?**
    - **Answer**: Load is testing normal capacity; Stress is finding the breaking point.
27. **[Hard] How do you test an Axios interceptor?**
    - **Answer**: By forcing a 401 error from the server and ensuring the interceptor redirects the user to `/login`.
28. **[Medium] How do you test Environment Variables?**
    - **Answer**: By switching between `.env.development` and `.env.production` and ensuring the API URL changes.
29. **[Hard] How do you test Mongoose Indexes?**
    - **Answer**: By trying to insert a duplicate email and ensuring the DB throws a "Unique Constraint" error.
30. **[Medium] What is a Headless Browser?**
    - **Answer**: A browser without a UI, used for fast automated testing (like Puppeteer).

### Category C — Project Specific (20)
31. **[Hard] Why did you choose 20-60 characters for the name field?**
    - **Answer**: To ensure data quality. I tested this by attempting to register with "Abhijay" (too short) and getting a 400 error.
32. **[Medium] How did you handle role-based redirection?**
    - **Answer**: Using a `ProtectedRoute` component in React. I verified this by trying to access `/admin` as a normal user.
33. **[Hard] What was the most difficult bug you found in Rately?**
    - **Answer**: The average rating not updating correctly due to the async nature of Mongoose hooks. I fixed it using a static method.
34. **[Medium] How do you test the 'Dashboard Stats' accuracy?**
    - **Answer**: I manually count the documents in MongoDB and compare them with the numbers shown on the Admin Dashboard.
35. **[Hard] How would you automate the Rately E2E flow?**
    - **Answer**: I'd use Cypress to script: Register -> Login -> Browse -> Rate -> Verify update.
36. **[Medium] How do you test the 'Unauthorized' page?**
    - **Answer**: By logging in as a store owner and trying to access `/admin/users`.
37. **[Hard] How do you handle database cleanup between tests?**
    - **Answer**: I use a separate test database and run a `beforeEach` hook to clear collections.
38. **[Medium] What happens if the backend is down?**
    - **Answer**: The frontend should show a "Network Error" toast message. I tested this by stopping the Node server.
39. **[Hard] How did you test the 'initDb.js' script?**
    - **Answer**: By running it on an empty database and verifying that the default admin/user/store were created correctly.
40. **[Medium] How do you test the Sidebar navigation?**
    - **Answer**: I verify that only the links relevant to the current user's role are visible.
41. **[Hard] How do you test the 'Search' regex logic?**
    - **Answer**: By searching for partial names and ensuring both "Tech Store" and "High Tech" appear for "Tech".
42. **[Medium] How do you test the 'Toast' notifications?**
    - **Answer**: By performing actions like "Login Success" and ensuring the green toast appears for 5 seconds.
43. **[Hard] How do you test the 'bcrypt' password hashing?**
    - **Answer**: By checking the DB and ensuring the password is a long hash, not plain text.
44. **[Medium] What is the 'trust proxy' setting for?**
    - **Answer**: It's for rate limiting when behind a proxy like Render/Vercel. I tested it by checking headers.
45. **[Hard] How do you test the 'Pagination' UI?**
    - **Answer**: By ensuring the "Next" button is disabled when on the last page.
46. **[Medium] How do you test 'Modal' accessibility?**
    - **Answer**: By ensuring focus moves into the modal when opened and returns to the button when closed.
47. **[Hard] How do you test 'Rate Limiting' in Rately?**
    - **Answer**: By using a script to send 100 requests and ensuring the 101st gets a 429 status.
48. **[Medium] How do you test the 'Landing Page' CTA?**
    - **Answer**: By clicking "Get Started" and ensuring it leads to the `/register` page.
49. **[Hard] How do you test 'Data Consistency' across roles?**
    - **Answer**: By rating a store as a user and then logging in as the owner to see that same rating.
50. **[Medium] What is your strategy for testing 'Settings'?**
    - **Answer**: Testing each field (Name, Password) individually and then both together.

### Category D — Tools and Automation (10)
51. **[Medium] Show a Jest unit test example.**
    - **Code**: `expect(2+2).toBe(4);` (Used for simple logic testing).
52. **[Hard] Show a Supertest integration test.**
    - **Code**: `request(app).post('/api/auth/login').send(creds).expect(200);`
53. **[Medium] How do you use Browser DevTools for QA?**
    - **Answer**: I use the Network tab to check API payloads and the Application tab for JWT storage.
54. **[Hard] How do you mock a function in Jest?**
    - **Code**: `const myMock = jest.fn();`
55. **[Medium] What is Cypress?**
    - **Answer**: An E2E testing framework for web applications.
56. **[Hard] How do you test 'Local Storage' in Cypress?**
    - **Code**: `cy.window().its('localStorage.token').should('exist');`
57. **[Medium] What is Postman Environment?**
    - **Answer**: A set of variables like `{{base_url}}` used across different requests.
58. **[Hard] How do you automate API tests in a CI/CD pipeline?**
    - **Answer**: By running `npm test` in a GitHub Action before deployment.
59. **[Medium] What is Lighthouse?**
    - **Answer**: An open-source tool for improving web page quality (Performance, SEO).
60. **[Hard] How do you test 'Authorization' headers automatically?**
    - **Answer**: In Postman, I set the token as a variable in the login request and use it in the 'Authorization' tab for subsequent requests.

---

## 20. QUICK FIRE ROUND (35 QUESTIONS)

1. **What is 404?** Not Found.
2. **What is 401?** Unauthorized (No login).
3. **What is 403?** Forbidden (No permission).
4. **Tool for API testing?** Postman.
5. **Tool for DB UI?** MongoDB Compass.
6. **What is Regression testing?** Testing old features after a change.
7. **What is Smoke testing?** Quick check of main features.
8. **What is BVA?** Boundary Value Analysis.
9. **What is a "Happy Path"?** The default successful user flow.
10. **What is "Negative Testing"?** Testing with invalid inputs.
11. **What is 200 OK?** Success.
12. **What is 201 Created?** Successful POST.
13. **What is 500?** Internal Server Error.
14. **What is 429?** Too Many Requests (Rate limit).
15. **What is a Bug?** Error in code.
16. **What is a Defect?** Deviation from requirement.
17. **What is SDLC?** Software Development Life Cycle.
18. **What is STLC?** Software Testing Life Cycle.
19. **What is Unit Testing?** Testing smallest code parts.
20. **What is Integration Testing?** Testing combined modules.
21. **What is System Testing?** Testing the whole app.
22. **What is UAT?** User Acceptance Testing.
23. **What is White Box?** Testing code internals.
24. **What is Grey Box?** Partial code knowledge testing.
25. **What is Static Testing?** No-execution testing.
26. **What is Dynamic Testing?** Execution-based testing.
27. **What is Retesting?** Testing a bug fix.
28. **What is Sanity Testing?** Check of specific area.
29. **What is Performance Testing?** Checking speed/stability.
30. **What is Load Testing?** Testing under normal load.
31. **What is Stress Testing?** Testing under extreme load.
32. **What is Usability Testing?** Checking user experience.
33. **What is Compatibility Testing?** Testing on different devices.
34. **What is Security Testing?** Checking vulnerabilities.
35. **What is a Test Suite?** Collection of test cases.

---

## 21. TOP 10 THINGS TO SAY THAT IMPRESS INTERVIEWERS

1. **"I don't just find bugs; I look for the root cause in the code."** (Shows technical depth).
2. **"I prioritize tests based on business risk, starting with the auth flows."** (Shows business alignment).
3. **"I always perform a 'Bug Triage' to ensure the team fixes critical issues first."** (Shows leadership).
4. **"I advocate for 'Shift Left' testing to find bugs earlier in the lifecycle."** (Shows efficiency).
5. **"I use Boundary Value Analysis to ensure robust validation on all inputs."** (Shows methodology).
6. **"I check the Network tab first when a UI action fails."** (Shows practical troubleshooting).
7. **"I write my bug reports so clearly that a developer never has to ask for more info."** (Shows communication).
8. **"I consider accessibility (a11y) as part of my usability testing."** (Shows inclusivity).
9. **"I understand that 100% test coverage is a goal, but 100% stability is the priority."** (Shows realism).
10. **"I'm comfortable testing APIs directly without a UI."** (Shows technical independence).

---

## 22. COMMON MISTAKES JUNIOR QA ENGINEERS MAKE

1. **Mistake**: Not checking console logs. **Avoid by**: Always keeping DevTools open.
2. **Mistake**: Testing only the "Happy Path". **Avoid by**: Spending 50% time on negative testing.
3. **Mistake**: Vague bug reports. **Avoid by**: Including clear steps and environment info.
4. **Mistake**: Not re-testing properly. **Avoid by**: Following the full bug lifecycle.
5. **Mistake**: Ignoring UI/UX details. **Avoid by**: Comparing UI against design mocks.
6. **Mistake**: Not asking "Why?". **Avoid by**: Understanding the business logic.
7. **Mistake**: Over-testing trivial areas. **Avoid by**: Focusing on high-risk modules.
8. **Mistake**: Not documenting manual tests. **Avoid by**: Maintaining a clean test suite.
9. **Mistake**: Fearing the code. **Avoid by**: Reading PRs to understand changes.
10. **Mistake**: Assuming the developer is always right. **Avoid by**: Trusting your requirements.

---

## INTERVIEW PREPARATION CHECKLIST 
- [x] Read all theory sections 
- [x] Practice all Q&A out loud 
- [x] Do the quick fire round with a friend 
- [x] Review all bug reports 
- [x] Run through all test scenarios mentally 
- [x] Prepare 2 minute project introduction 
- [x] Prepare demo of live project 
- [x] Research the company before interview 
