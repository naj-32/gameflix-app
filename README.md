GameFlix – User Authentication Service

Project Overview
GameFlix is a video game subscription management system that I am developing for HCDD 412. For Module 4, I focused on building the user registration and login service using Java 21, Spring Boot 4.1.1, Spring Security, and MySQL.
The service allows users to create an account, log in, and view their profile using a JWT. Passwords are hashed before being saved to the database, and protected information can only be accessed with a valid token.

How to Run the Application
1	Install Java 21 and MySQL.
2	Create a MySQL database named demo40.
3	Create the users table using the schema provided below.
4	Open the project in IntelliJ IDEA.
5	Configure the MYSQL_USER and MYSQL_PASSWORD environment variables using credentials that can access the database.
6	Generate a JWT secret using openssl rand -base64 32 and save the result in the JWT_SECRET environment variable.
7	Run DemoApplication.java.
8	Use Postman to test the endpoints at http://localhost:8080.
The application.properties.example file shows the configuration needed to run the application without including private credentials.

API Endpoints
The application includes three endpoints:
•	POST /api/auth/register: Allows users to create an account. The password is hashed before the user is saved in MySQL.
•	POST /api/auth/login: Checks the user's email and password and returns a JWT if the information is correct.
•	GET /api/users/me: Allows users to view their own profile after providing a valid JWT.

Database Design
I used MySQL to store user account information in the demo40 database. The users table contains an ID, email, and password. Each user has a unique ID, and the email field has a unique constraint to prevent duplicate registrations. The password column uses VARCHAR(255) to provide enough space for the hashed password.
MySQL schema:
'users', 'CREATE TABLE `users` (\n  `id` bigint NOT NULL AUTO_INCREMENT,\n  `email` varchar(255) NOT NULL,\n  `password` varchar(255) NOT NULL,\n  PRIMARY KEY (`id`),\n  UNIQUE KEY `email` (`email`)\n) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci'

Hibernate setting:
I used spring.jpa.hibernate.ddl-auto=update so Hibernate can update the database structure without deleting existing user records. This was useful during development because I could restart GameFlix without losing the users I registered for testing.

Password Security
I used Spring Security's delegating password encoder, which uses bcrypt with a work factor of 10. I chose bcrypt because it is designed for password hashing, and its work factor makes it more difficult for someone to guess passwords through repeated attempts. The delegating encoder also includes the algorithm name with the stored hash, which makes it easier to support different hashing methods later.
The original password is never stored in MySQL or returned in the registration response.

JWT Security
When a user logs in successfully, the application generates a signed JWT that expires after one hour. I chose a one-hour lifetime because it gives users time to access their accounts without logging in for every request while limiting how long the token remains usable.
The token contains the user's email and time-related claims, but it does not contain their password. The JWT signing secret is provided through an environment variable instead of being written directly in the source code.
I also configured CORS for the local frontend origin rather than allowing requests from every origin.

Testing
I used Postman to test the registration, login, and protected profile endpoints.
My tests included:
•	Successful registration returning 201 Created.
•	Duplicate registration returning 409 Conflict.
•	Invalid registration information returning 400 Bad Request.
•	Successful login returning 200 OK with a JWT.
•	Incorrect login credentials returning 401 Unauthorized.
•	Accessing the user profile with a valid JWT returning 200 OK.
•	Accessing the profile without a token returning 401 Unauthorized.
•	Accessing the profile with an altered token returning 401 Unauthorized.
I also used MySQL Workbench to confirm that registered users were saved in the database and their passwords were stored as bcrypt hashes instead of plaintext.
The exported Postman collection and screenshots of the test results are included with my submission.

Changes From Module 3
In Module 3, I planned GameFlix around separate services for user accounts, the game catalog, and subscriptions. For this assignment, I focused on implementing the User Service rather than building the entire system at once.
I also reused my existing demo40 MySQL database instead of creating a new database for GameFlix. The user authentication service uses a separate users table, and the other planned features will be implemented in later stages of the project.

AI Assistance
I used Gemini throughout parts of the assignment to help with writing and understanding the Spring Boot code. For example, it helped me set up the registration and login endpoints and explained how JWT authentication works. I also used it to troubleshoot errors during testing. One change I made was updating the security configuration after my invalid registration test returned 401 Unauthorized instead of 400 Bad Request. After making the change, I tested it again in Postman and got the correct response.
