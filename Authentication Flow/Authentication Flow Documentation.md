# Restful-Booker API Authentication Flow Documentation

This guide covers the steps to set up, run, and test the authentication flow for the Restful-Booker API. Follow each step carefully to ensure a successful setup and testing experience.

## Prerequisites
1. **Postman**: Ensure Postman is installed for testing API requests.
2. **Environment Setup**: Create a Postman environment with variables to store the base URL and token.

## Step-by-Step Guide

### 1. Generate Auth Token

To access protected endpoints, you need to generate an authentication token.

1. **Open Postman** and create a new request.
2. **Set Method**: Choose `POST`.
3. **URL**: Enter the auth endpoint URL: https://restful-booker.herokuapp.com/auth

4. **Headers**:
- Add a `Content-Type` header and set it to `application/json`.
5. **Body**:
- Select `raw` and choose `JSON`.
- Enter the following payload:
  ```json
  {
    "username": "admin",
    "password": "password123"
  }
  ```
6. **Send the Request**.
7. **Save the Token**:
- You should receive a response similar to:
  ```json
  {
    "token": "your_auth_token"
  }
  ```
- Copy the token and save it in your Postman environment as a variable called `token`.

### 2. Test Invalid Credentials

1. **Duplicate the Previous Request**.
2. **Modify the Body**:
- Change the `username` or `password` to incorrect values, for example:
  ```json
  {
    "username": "invalid_user",
    "password": "wrong_password"
  }
  ```
3. **Send the Request**.
4. **Expected Response**:
- You should receive a `403 Forbidden` response, confirming that invalid credentials are rejected.

### 3. Test Token Expiration

The Restful-Booker API tokens do not actually expire, but this is a sample guide for implementing a token expiration test if needed.

1. **Manually Expire the Token**:
- In your Postman environment, change the `token` variable to a random string (e.g., `expired_token123`).
2. **Use Expired Token in Requests**:
- Make any request to a protected endpoint (e.g., `PUT /booking/:id`) using this expired token.
3. **Expected Response**:
- The server should respond with a `403 Forbidden` or `401 Unauthorized` error, simulating token expiration behavior.

### 4. Test Protected Endpoints

Protected endpoints require a valid token for access. Testing these endpoints helps confirm that authentication is enforced.

#### Step 1: Test Access **Without** Authentication

1. **Open Postman** and create a request for a protected endpoint (e.g., `PUT /booking/:id`).
- URL: `https://restful-booker.herokuapp.com/booking/:id`
- Replace `:id` with an actual booking ID.
2. **Remove Authentication**:
- Ensure there is no `Cookie` or `Authorization` header containing a token.
3. **Body** (for `PUT` requests):
- Select `raw` and `JSON`.
- Add a JSON payload:
  ```json
  {
    "firstname": "John",
    "lastname": "Doe",
    "totalprice": 200,
    "depositpaid": true,
    "bookingdates": {
      "checkin": "2023-01-01",
      "checkout": "2023-01-07"
    },
    "additionalneeds": "Breakfast"
  }
  ```
4. **Send the Request**.
5. **Expected Response**:
- You should receive a `403 Forbidden` or `401 Unauthorized` response, confirming that access is restricted without authentication.

#### Step 2: Test Access **With an Invalid Token**

1. **Add Invalid Token**:
- In your Postman environment, set the `token` variable to an invalid value (e.g., `invalid_token123`).
2. **Add Authorization Header**:
- In the `Headers` tab, add a `Cookie` header with:
  ```
  token={{token}}
  ```
3. **Send the Request**.
4. **Expected Response**:
- A `403 Forbidden` or `401 Unauthorized` response should confirm that only valid tokens are accepted.

#### Step 3: Test Access **With a Valid Token**

1. **Restore Valid Token**:
- Go to your Postman environment and reset the `token` variable with the valid token generated in Step 1.
2. **Add Authorization Header**:
- In the `Headers` tab, add a `Cookie` header with:
  ```
  token={{token}}
  ```
3. **Send the Request**.
4. **Expected Response**:
- You should receive a `200 OK` response (or another success code), indicating successful access to the protected endpoint with a valid token.

---

### Summary of Expected Results

| Test Scenario                      | Expected Response                        |
|------------------------------------|------------------------------------------|
| No Authentication                  | `403 Forbidden` or `401 Unauthorized`    |
| Invalid Token                      | `403 Forbidden` or `401 Unauthorized`    |
| Valid Token                        | `200 OK` (or success response)           |

This completes the setup and testing guide for the Restful-Booker API authentication flow.

