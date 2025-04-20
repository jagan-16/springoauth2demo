
This project is a Spring Boot REST API demonstrating secure endpoint access using JWT tokens, validated through JWKS (JSON Web Key Set) from an AuthAction M2M (Machine-to-Machine) OAuth2 flow.



Setup Instructions

1. Clone the Repository
```
git clone https://github.com/your-username/springoauth2demo.git
cd springoauth2demo
```

 2. Configure Application Properties
Edit the `src/main/resources/application.properties` file:
```properties
spring.application.name=springoauth2demo
server.port=3000

auth0.audience=https://authsecurity.in.authaction.com
auth0.domain=authsecurity.in.authaction.com
spring.security.oauth2.resourceserver.jwt.issuer-uri=https://${auth0.domain}/
```
 3. Run the Project
```
./mvnw spring-boot:run
```

The application will start on: `http://localhost:3000`

---
How to Get a JWT Access Token

To obtain a valid JWT, create a Machine-to-Machine Application and a Custom API in your [AuthAction Dashboard](https://authaction.com).

Use the following curl command, replacing the placeholders with your actual credentials:

```
curl --request POST \
  --url https://authsecurity.in.authaction.com/oauth2/m2m/token \
  --header 'content-type: application/json' \
  --data '{
    "client_id": "<YOUR_CLIENT_ID>",
    "client_secret": "<YOUR_CLIENT_SECRET>",
    "audience": "https://authsecurity.in.authaction.com",
    "grant_type": "client_credentials"
  }'
```

Copy the `access_token` value from the JSON response.

---

Sample curl Commands

Test Public Endpoint (No Token Required)
```bash
curl http://localhost:3000/public
```

Expected Output:
```json
{ "message": "This is a public message!" }
```

Test Protected Endpoint (Requires Token)
```bash
curl http://localhost:3000/protected \
  -H "Authorization: Bearer <YOUR_ACCESS_TOKEN>" \
  -H "content-type: application/json"
```

Expected Output:
```json
{
  "message": "This is a protected message!",
  "sub": "auth0|...",
  "email": "..."
}
```

---

Notes on JWKS Integration

- The API uses Spring Security's OAuth2 Resource Server to validate JWTs.
- Public keys are fetched automatically from:
  ```
  https://authsecurity.in.authaction.com/.well-known/jwks.json
  ```
- This JWKS must contain at least one RSA key. If the `keys` array is empty, the API will fail to validate tokens.
- Validation includes:
  - Verifying the token's signature using the JWK
  - Ensuring the `audience` matches the configured API
  - Confirming the `issuer` matches the trusted domain
- Audience validation is implemented using a custom `AudienceValidator.java` class.

---

Project Structure

```
src/main/java/com/authaction/springoauth2demo
├── ApiController.java            # REST endpoints
├── SecurityConfig.java           # Security configuration
└── security/
    └── AudienceValidator.java    # Custom audience claim validator
```

---

Technologies Used

- Java 17
- Spring Boot 3.4.4
- Spring Security
- OAuth2 Resource Server
- JWT and JWKS

---

Author

Jagan  
This project was developed as part of the AuthAction Interview Assignment. For any questions or clarifications, feel free to reach out.
