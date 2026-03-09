Day 1: 
-Installed Linux
-Learned basic commands
-Started my cloud/devops path
Day 2:
-Learned about Permissions
-Learned SSH
Day 3:
-Github basics
-Connected and shared from the this VM to Github acc
Day 4:
-Learned Networking basics
-IP, DNS, ports
-Used ping, curl, ip a 
Day 5:
-Learned what processes are
-Used ps aux and top
-Understood processes vs services
-Used systemctl to manage sshd
-Viewed logs with journalctl
Day 6:
-Used ping to test connectivity 
-Understood DNS vs IP
-Used curl to talk to web servers
-Checked open ports with ss and netstat
-Verified SSH listening on port 22
Day 7:
-Learned what a firewall does
-Used ufw to manage firewall rules
-Allowed SSH(22), HTTP(80), HTTPS(443)
-Enabled ufw safely
-Listed and deleted firewall rules
Day 8:
-Learned about users and root
-Used Sudo to run commands as root
-Understood groups and sudo group
-Viewed file ownership with ls -l
-Changed ownership with chown
-Practiced permission changes with chmod
Day 9:
-Checked disk usage with df -h
-Found large directories with du
-Explored /var/log
-Read logs with less
-Viewed service logs with journalctl
Day 10:
-Learned difference between apt update and apt upgrade
-Installed and removed packages
-Used apt search
-Cleaned unused packages with autoremove
-Viewed installed packages with dpkg
Day 11:
-Created an AWS account
-Created a proper IAM admin user
-Launched EC2 Instance
	-Made sure to make a key pair
	-Allowed SSH traffic from My IP
-Connected to Instance VIA the windows Powershell
	-Learned a shortcut to opening the shell in the file containing the .pem file
	-Established the fingerprint
-Ran update and upgrade on EC2 Instance
-Installed Nginx
-Opened port 80 in AWS and tested connection in browser
-Rebooted EC2 instance
-Stopped Instance
Day 12: 
-On Ec2
-Installed and enabled UFW
	-Allowed OpenSSH (22)
	-Allowed Nginx Full (80/443)
-Hardened SSH
	-Edited /etc/ssh/sshd_config
		-PasswordAuthentication no 
		-PermitRootLogin no
	-Restarted SSH safely and verified new connection
-Installed Fail2ban for intrusion protection
	-Verified sshd jail is active
-Replaced default Nginx page with custom HTML
	-Verified live deployment
#Reflection
Today I moved from “launching a VM” to understanding server hardening fundamentals.
Security is layered:
-Cloud firewall, OS firewall, SSH configuration, and intrusion prevention.
Day 13:
-Attached IAM Role (EC2-S3-ReadOnly-Role) to EC2 Instance
-Installed AWS CLI (Tested via IAM Role)
-Verified access to S3 Bucket without using credentials
-Learned that IAM Roles are the secure, credential-free way for servers to access AWS rescources
-Key takeaway: Never hardcore AWS keys; always use IAM roles
Day 14:
-Purchased domain syl-awstraining.space (for learning/project purposes)
-Configured DNS in Namecheap:
	-Root domain(@)
	-www
	-Tied to EC2 Public IP
-Learned the fundamentals of EC2 and S3
-Verified IAM role access from EC2 to S3
-Prepared for HTTPS setup (Let's Encrypt/Certbot)
-Allocated an Elastic IP for EC2 to make the public IP static
-Learned about AWS Elastic IP billing
	-Free when attached to running or stopped instance
	-Charged only when unattached
#Reflection
Thought ahead when establishing the DNS tie in. With the IP changing with every reset on the
EC2 instance, I should probably set something more permenant to avoid having to go through
the DNS Host records menu every time I began a learning session. They also stated on namecheap
that multiple IP changes within a designated time would result in charges or shutdown.
Day 15: Node.js Deployment on AWS EC2

**Tasks Completed:**
- Installed Node.js and npm
- Created a simple Node.js JSON API
- Tested Node.js locally (`curl http://localhost:3000`)
- Installed PM2 to keep Node.js running in background
- Configured Nginx as a reverse proxy for Node.js
- Updated both HTTP and HTTPS `location / {}` blocks to proxy traffic
- Installed Certbot & configured HTTPS for domain
- Verified HTTPS traffic routes through Node.js successfully
- JSON response is visible at https://syl-awstraining.space

**Key Learnings:**
- Reverse proxy allows internal app ports to remain private
- PM2 handles background processes and startup scripts
- Nginx + Certbot provides production-ready HTTPS
- Traffic flows through Nginx to Node.js without exposing Node ports
- Full cloud deployment: EC2 + Elastic IP + Namecheap domain + HTTPS

**Reflection**
- When using certbot I broke the webpage temporarily
- The certificate turned the webpage to an https on port 443
- I had not opened port 443 through the EC2 instance causing the website to stop loading.
- When first booting up the EC2 instance, found that I couldnt load in to the website on the browser
- Discovered that I hadnt assigned the elastic IP address to the associated instance ID
Day 16 – Production Deployment Workflow & SSH Git Integration
- Converted GitHub authentication from HTTPS to SSH
- Generated SSH key on EC2
- Added EC2 SSH key to GitHub
- Verified authentication via `ssh -T git@github.com`
- Moved application from /home/ubuntu to /var/www/myapp
- Fixed ownership permissions
- Restarted app using PM2
- Verified production deployment still functioning
Day 17 – Environment Variables, Debugging & Health Checks
-Installed dotenv
-Updated server.js to load environment config:
	require('dotenv').config();
-Replaced hardcoded port with:
	const PORT = process.en.PORT || 3000;
-Created .env file:
	PORT=3000
	APP_NAME=ShaylynProductionApp
-Verified environment variables were properly injected in to runtime
-Observed a 502 error in browser
-Used:
	pm2 list
	pm2 logs myapp
-Identified syntax error
-Fixed missing comma
-Restarted with:
	pm2 restart myapp
-Added a Production Health Check Endpoint
	if (req.url === '/health') {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  return res.end(JSON.stringify({
    status: "OK",
    uptime: process.uptime(),
    timestamp: new Date()
  }));
}
-Verified with:
	curl http://localhost:3000/health
	https://syl-awstraining.space/health
-Application now supports uptime monitoring and load balancer health checks
Day 18 – PostgreSQL CRUD Foundation

Objective:
Add real persistent data storage to the Node backend and create API endpoints for users.

PostgreSQL Setup:
- Installed PostgreSQL on EC2
- Verified cluster running
- Created database: myappdb
- Created user: myappuser with password
- Learned about table ownership and permissions

Users Table:
- Created table 'users':
    id SERIAL PRIMARY KEY
    name VARCHAR(100) NOT NULL
    email VARCHAR(100) UNIQUE NOT NULL
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
- Granted myappuser access to table and sequence
- Learned PostgreSQL ownership vs app user concepts

Node.js API Endpoints:
- GET /users -> fetch all users
- POST /users -> insert a new user
- Parsed JSON body manually
- Parameterized queries to prevent SQL injection

Lessons Learned:
- Initialization order matters (dotenv before Pool)
- Route ordering matters (default route vs POST/GET)
- Node streams: req.on('data') & req.on('end')
- Troubleshooting hanging POST requests via Nginx vs localhost
- Debugging permissions with pg errors
- Full backend chain: Nginx -> Node -> PostgreSQL -> client

Outcome:
- Successfully inserted a user via POST
- Verified persistence in database via GET
- Backend now fully supports CRUD foundation
Day 19 – Express Refactor & API Validation Upgrade

Objective:
Refactor Node server to Express framework and improve API structure and validation.

Express Refactor:
- Installed Express
- Replaced http.createServer with Express app
- Enabled express.json() middleware
- Simplified routing using app.get() and app.post()
- Maintained PostgreSQL Pool configuration
- Preserved environment variables
- Restarted and verified with PM2

Project Structure Upgrade:
myapp/
 ├── server.js
 ├── app.js
 ├── db.js
 └── routes/
      └── users.js

Separation of Concerns:
- db.js handles database connection
- routes/users.js handles user endpoints
- app.js handles middleware and route mounting
- server.js starts the server

Endpoints:
GET /
GET /health
GET /db-test
GET /users
POST /users

Validation Added to POST /users:
- Required field validation (name and email)
- Basic email format validation
- Duplicate email handling (Postgres error code 23505)
- Proper HTTP status codes (400, 201, 500)

Improvements Over Previous Version:
- Removed manual body parsing
- Cleaner async route handling
- Modular file structure
- Better error handling
- More scalable architecture
- Industry-standard Express setup

Architecture Now:
Browser -> Nginx -> Express (PM2) -> PostgreSQL -> Response

Outcome:
Successfully refactored to modular Express structure.
Implemented input validation and controlled error responses.
Application remains stable under HTTPS and PM2.

DAY 20 – Production Debugging & Duplicate Constraint Handling
Overview
-Today focused on debugging a production API issue and properly handling database constraint errors.
-Key Areas Covered
-Testing POST /users in production
-Handling duplicate email constraint
-Fixing 502 / 500 server errors
-Understanding Express error propagation
-Correct use of next() in async routes
-Reading PM2 logs for debugging

-Main Production Error
-Error Message:
	-ReferenceError: next is not defined at routes/users.js
-Root Cause:
	-The route handler did not include "next" in the function parameters, but the code attempted to call next(err).
-Incorrect Route Definition:
	-router.post("/", async (req, res) => {
-Inside the route:
	-next(err)
-Because "next" was not defined, the application crashed.
-Fix Applied:
	-Updated the route definition to include next:
	-router.post("/", async (req, res, next) => {
-After making the change:
	-pm2 restart myapp
	-The server restarted successfully and stopped crashing.
-Database Behavior
	-PostgreSQL has a UNIQUE constraint on the email field.
	-When a duplicate email is inserted, PostgreSQL throws error code: 23505
-Global error middleware handles it like this:
	-if (err.code === '23505') { return res.status(400).json({ error: "Duplicate value violates unique constraint"});}
-This prevents server crashes and returns a proper 400 response.
-Testing in Production
-Command Used:
	curl -X POST https://syl-awstraining.space/users
	-H "Content-Type: application/json"
	-d '{"name":"DuplicateTest","email":"dupe@test.com"}'
-Expected Behavior:
	-First request → Success
	-Second request → 400 Duplicate error
-Lessons Learned
	-Express async routes must include next for error propagation
	-Errors in async functions must be passed to middleware
	-PM2 logs are critical for debugging production issues
	-PostgreSQL enforces unique constraints at the database level
	-Proper error middleware prevents crashes
-Full request lifecycle:
	-Nginx → Express → Route → Database → Middleware → Client Response
-Project Status
	-Production API is stable.
	-Duplicate protection is working.
	-Error handling middleware is functioning correctly.
	-Server no longer crashes on database constraint violations.
-DAY 21 – Input Validation & Controller Refactor
-Overview
	-Today focused on improving production API structure and adding input validation to protect the database.
-Key areas covered:
	-Added input validation middleware
	-Ensured required fields (name and email) are present
	-Checked for valid email format
	-Moved business logic from routes to controllers
	-Learned proper Express route syntax
	-Fixed syntax and module import issues
	-Restarted PM2 to reflect changes
-Middleware Implementation
-File: middleware/validateUser.js
	-Validates name and email fields
	-Blocks requests with missing or invalid fields
	-Returns 400 responses for invalid input
	-Calls next() for valid input to continue request chain
-Route Changes
-File: routes/users.js
	-Replaced inline async function with controller reference
	-Connected validation middleware
-Old:
	-router.post("/", async (req, res, next) => { ... })
-New:
	-router.post("/", validateUser, createUser);
-Controller Implementations
-File: controllers/userController.js
	-Handles database logic (INSERT INTO users)
	-Uses async/await
	-Errors passed to global middleware with next(err)
	-Keeps route file clean and maintainable
-Key errors encountered
	-MODULE_NOT_FOUND for validator → solved by npm install validator in project directory
	-SyntaxError: malformed arrow function → fixed by removing => in route
-Testing
	-1. Missing fields:
	 curl -X POST https://syl-awstraining.space/users
	 -H "Content-Type: application/json"	
	 -d '{}'

	-Expected: {"error":"Name and email are required"}

	-2. Invalid email:
	 curl -X POST https://syl-awstraining.space/users
	 -H "Content-Type: application/json"
	 -d '{"name":"Test","email":"notanemail"}'

	-Expected: {"error":"Invalid email format"}

	-3.Valid input:
	 curl -X POST https://syl-awstraining.space/users
	 -H "Content-Type: application/json"
	 -d '{"name":"Valid","email":"valid@test.com"}'

	-Expected: User created successfully (or duplicate error if email exists)

-Lessons Learned
	-Difference between npm install (project dependency) and pm2 install (PM2 module)
	-Proper error propagation using next()
	-Middleware protects database from invalid data
	-Separation of concerns makes code maintainable and professional
	-PM2 logs are essential for debugging production apps
	-Express route syntax must be correct: router.post(path, middleware, handler)
-Status
	-Application is online and stable under PM2
	-Input validation works correctly
	-Controller structure implemented
	-Backend architecture now production-grade
DAY 22 – Completing Full CRUD API
Overview

Today we completed full CRUD functionality for the Users API and finalized a production-structured backend architecture.

The application is now a fully functional REST API with proper error handling and clean controller separation.

Endpoints Implemented
CREATE

POST /users

Creates a new user.

400 → Invalid input

409 → Duplicate email

201 → User created successfully

READ (All Users)

GET /users

Returns all users ordered by ID.

Supports optional query filtering:

GET /users?email=test@test.com

GET /users?name=shay

Returns:

200 → Array of users

[] → If no matches found

READ (Single User)

GET /users/:id

Returns a specific user by ID.

200 → User object

404 → User not found

UPDATE

PUT /users/:id

Updates name and email.

200 → User updated successfully

404 → User not found

409 → Email already exists

Uses:
UPDATE users SET name = $1, email = $2 WHERE id = $3 RETURNING *

DELETE

DELETE /users/:id

Deletes a user by ID.

200 → User deleted successfully

404 → User not found

Uses:
DELETE FROM users WHERE id = $1 RETURNING *

Key Technical Concepts Learned

RESTful API structure

Express route ordering

Route parameters (req.params)

Query parameters (req.query)

Proper HTTP status codes (200, 201, 400, 404, 409)

PostgreSQL error code 23505 (unique violation)

Parameterized queries (SQL injection protection)

Controller separation (Separation of Concerns)

PM2 production process management

Reading PM2 logs for debugging

Database constraints enforcing data integrity

Architecture Status

Application is:

Running under PM2

Hosted on EC2

Connected to PostgreSQL

Using MVC-style structure

Protected by input validation middleware

Returning clean JSON responses

Handling database errors properly

Current Backend Capabilities

POST /users
GET /users
GET /users/:id
PUT /users/:id
DELETE /users/:id

Full CRUD complete.

Status

Application is stable.
All endpoints tested successfully.
404 handling works.
Duplicate protection works.
Query filtering works.

Backend is now portfolio-ready and production-structured.
DAY 23 – JWT Authentication & Protected Routes
Overview

Today we implemented secure authentication using JSON Web Tokens (JWT).

The backend now supports:

Password hashing with bcrypt

Secure login endpoint

JWT token generation

Authentication middleware

Protected routes

This transitions the API from a basic CRUD system to a secure backend architecture.

Database Update

Added password column to users table:

ALTER TABLE users ADD COLUMN password TEXT NOT NULL DEFAULT 'temp';

Passwords are now stored as hashed values (never plain text).

Packages Installed

npm install bcrypt jsonwebtoken

bcrypt → Secure password hashing
jsonwebtoken → Token-based authentication

Password Hashing (Registration)

When creating a user:

Password is hashed using bcrypt

Salt rounds: 10

Only id, name, email returned in response

Hashed password stored in database

Security improvement:
Passwords are no longer stored in plain text.

Login Endpoint

POST /users/login

Flow:

User submits email + password

Server checks if email exists

bcrypt.compare verifies password

If valid → JWT token generated

Token returned to client

Token contains:

user id

user email

expiration (1 hour)

Invalid credentials return:

401 → "Invalid credentials"

JWT Structure

Token is signed using:

jwt.sign(payload, secret, { expiresIn: "1h" })

Authentication is stateless.
No sessions are stored server-side.

Authentication Middleware

Created middleware:

middleware/auth.js

Flow:

Check for Authorization header

Extract Bearer token

Verify using jwt.verify()

If valid → attach user to req.user

If invalid → return 401 or 403

Error responses:

401 → Access token required
403 → Invalid or expired token

Protected Route

Protected:

GET /users

Now requires:

Authorization: Bearer <token>

Without token → 401
With valid token → 200

Security Concepts Learned

Password hashing vs encryption

Salt rounds and bcrypt

Stateless authentication

JWT payload and signature

Bearer token header structure

Middleware-based route protection

401 vs 403 difference

Never storing plain text passwords

Architecture Status

Application now includes:

Full CRUD

Input validation middleware

Global error handler

Secure password storage

Login system

JWT token generation

Protected route

Running under PM2

Hosted on EC2

PostgreSQL database integration

Backend is now secure and production-structured.

Current Endpoint Capabilities

POST /users
POST /users/login
GET /users (Protected)
GET /users/:id
PUT /users/:id
DELETE /users/:id

Status

Authentication working correctly.
Token generation successful.
Protected route verified.
Application stable under PM2.

Backend is now portfolio-level secure API.
Day 24 – Rate Limiting & Request Logging Prep
Goal

Protect the API from abuse by adding rate limiting and prepare for production-ready logging.

Steps Completed

Installed rate limiter

npm install express-rate-limit

Configured limiter in app.js

const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // max requests per IP
  message: { error: "Too many requests, please try again later." }
});

app.use(limiter);

Placed limiter above routes

Ensures all requests are checked before reaching controllers.

Added app.set('trust proxy', 1) because app is behind Nginx.

Tested limiter

Sent 120 requests quickly to /health

Requests 101+ returned:

{"error":"Too many requests, please try again later."}

Request logger middleware

app.use((req, res, next) => {
  console.log(`${new Date().toISOString()} | ${req.method} ${req.originalUrl}`);
  next();
});

Logs timestamp, HTTP method, and route

Useful for debugging and monitoring API traffic

Lessons Learned

Middleware order matters: Limiter must come before routes

Rate limiting protects against brute force & spam

app.set('trust proxy', 1) is essential when behind Nginx

Logging requests early helps track and debug production traffic

Centralized error handling ensures consistent API responses

Status

✅ Rate limiting is working
✅ Request logging prints timestamps and routes
✅ App remains online and stable under PM2
✅ Backend is now production-ready for security & observability improvements
Day 25 – Structured Logging with Morgan and Winston
Goal

Replace basic console logging with structured production logging.

Packages Installed
npm install morgan winston

Morgan logs HTTP requests.

Winston manages structured logs and writes them to files.

Created Logger Utility

File created:

utils/logger.js

Code:

const { createLogger, transports, format } = require("winston");

const logger = createLogger({
  level: "info",
  format: format.combine(
    format.timestamp(),
    format.json()
  ),
  transports: [
    new transports.File({ filename: "logs/error.log", level: "error" }),
    new transports.File({ filename: "logs/combined.log" })
  ]
});

module.exports = logger;
Created Logs Directory
mkdir logs

Log files created automatically:

logs/error.log
logs/combined.log
Connected Morgan to Winston

Added to app.js:

const morgan = require("morgan");
const logger = require("./utils/logger");

app.use(
  morgan("combined", {
    stream: {
      write: (message) => logger.info(message.trim())
    }
  })
);

Morgan now sends request logs to Winston.

Centralized Error Logging

File:

middleware/errorHandler.js

Code:

const logger = require("../utils/logger");

function errorHandler(err, req, res, next) {
  logger.error(err.stack);

  if (err.code === "23505") {
    return res.status(409).json({
      error: "Duplicate value violates unique constraint"
    });
  }

  res.status(500).json({
    error: "Internal server error"
  });
}

module.exports = errorHandler;

Added to app.js:

app.use(errorHandler);
Testing Logs

Test request:

curl https://syl-awstraining.space/users

View logs:

cat logs/combined.log

Check error logs:

cat logs/error.log
What I Learned

Structured logging improves debugging

Winston writes logs to files for persistence

Morgan captures HTTP request details

Centralized error handling ensures consistent logging

Logs are essential for monitoring production systems

Current Backend Features

HTTPS reverse proxy

PostgreSQL database

JWT authentication

Rate limiting

structured logging

centralized error handling

Express API with routes and controllers
