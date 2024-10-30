# Restful-Booker Error Scenarios Testing Documentation

This documentation provides a step-by-step guide to set up, run, and test error scenarios on the Restful-Booker API. These tests cover invalid input data, non-existent resources, unauthorized access, and malformed requests.

## Prerequisites

1. **Postman**: Ensure Postman is installed.
2. **API Base URL**: We will use `https://restful-booker.herokuapp.com/`.

## Setting Up Postman Environment

1. Open Postman and set up a new environment by navigating to **Environments** > **Add New Environment**.
2. Add the following variables:
   - `baseUrl`: Set to `https://restful-booker.herokuapp.com`
   - `token`: Leave empty (this will be generated in the authentication step)
   - `bookingId`: Leave empty (used for booking operations)
3. Save the environment and select it from the environment dropdown.

## Authentication

To access protected endpoints, you need an authentication token.

1. **Generate Auth Token**:
   - **Request Type**: `POST`
   - **Endpoint**: `{{baseUrl}}/auth`
   - **Body**: Use the following JSON in the **Body** tab (select `raw` > `JSON`):
     ```json
     {
       "username": "admin",
       "password": "password123"
     }
     ```
   - **Response**: Copy the `token` from the response and set it in the environment under the `token` variable.

## Error Scenarios

### 1. **Invalid Input Data**

   - **Description**: Test scenarios where data types or required fields are missing or incorrect.
   - **Create Booking with Invalid Data**:
     - **Request Type**: `POST`
     - **Endpoint**: `{{baseUrl}}/booking`
     - **Headers**:
       - `Content-Type`: `application/json`
       - `Accept`: `application/json`
     - **Body**: Provide invalid data in JSON, such as a negative `totalprice` or missing fields:
       ```json
       {
         "firstname": "",
         "lastname": "Doe",
         "totalprice": -100,
         "depositpaid": "yes",
         "bookingdates": {
           "checkin": "2024-10-32",
           "checkout": "2024-13-45"
         }
       }
       ```
     - **Response**: Expect a `400 Bad Request` error. Confirm the response contains an error message specifying the validation failure.

### 2. **Non-Existent Resources**

   - **Description**: Test scenarios where a booking ID does not exist in the system.
   - **Retrieve Non-Existent Booking**:
     - **Request Type**: `GET`
     - **Endpoint**: `{{baseUrl}}/booking/9999999`
     - **Headers**:
       - `Accept`: `application/json`
     - **Response**: Expect a `404 Not Found` error with a message indicating the booking does not exist.
   - **Update Non-Existent Booking**:
     - **Request Type**: `PUT`
     - **Endpoint**: `{{baseUrl}}/booking/9999999`
     - **Headers**:
       - `Content-Type`: `application/json`
       - `Accept`: `application/json`
       - `Cookie`: `token={{token}}`
     - **Body**: Send any JSON structure (e.g., sample booking data).
     - **Response**: Expect a `404 Not Found` error. Verify that the response indicates the resource could not be found.

### 3. **Unauthorized Access**

   - **Description**: Test scenarios where access to a protected resource is attempted without a valid token.
   - **Create Booking Without Token**:
     - **Request Type**: `POST`
     - **Endpoint**: `{{baseUrl}}/booking`
     - **Headers**:
       - `Content-Type`: `application/json`
       - `Accept`: `application/json`
     - **Body**: Use valid booking data:
       ```json
       {
         "firstname": "John",
         "lastname": "Doe",
         "totalprice": 150,
         "depositpaid": true,
         "bookingdates": {
           "checkin": "2024-10-30",
           "checkout": "2024-11-02"
         }
       }
       ```
     - **Response**: Expect a `403 Forbidden` or `401 Unauthorized` error. The response should indicate that authentication is required.
   - **Delete Booking with Invalid Token**:
     - **Request Type**: `DELETE`
     - **Endpoint**: `{{baseUrl}}/booking/{{bookingId}}`
     - **Headers**:
       - `Cookie`: `token=invalidtoken`
     - **Response**: Expect a `403 Forbidden` or `401 Unauthorized` error. Confirm the response mentions invalid or expired token.

### 4. **Malformed Requests**

   - **Description**: Test scenarios where the request format is incorrect.
   - **Create Booking with Malformed JSON**:
     - **Request Type**: `POST`
     - **Endpoint**: `{{baseUrl}}/booking`
     - **Headers**:
       - `Content-Type`: `application/json`
       - `Accept`: `application/json`
     - **Body**: Use an incorrectly formatted JSON (missing a closing brace):
       ```json
       {
         "firstname": "Jane",
         "lastname": "Doe",
         "totalprice": 200,
         "depositpaid": true,
         "bookingdates": {
           "checkin": "2024-10-30",
           "checkout": "2024-11-02"
       ```
     - **Response**: Expect a `400 Bad Request` error. Confirm the response indicates a syntax error in JSON formatting.
   - **Update Booking with Invalid Method**:
     - **Request Type**: `PATCH` (Instead of `PUT`)
     - **Endpoint**: `{{baseUrl}}/booking/{{bookingId}}`
     - **Headers**:
       - `Content-Type`: `application/json`
       - `Accept`: `application/json`
       - `Cookie`: `token={{token}}`
     - **Body**: Use valid JSON booking data.
     - **Response**: Expect a `405 Method Not Allowed` error. Verify the response message indicates that the `PATCH` method is not supported.

## Testing Checklist

1. **Invalid Input Data**: Confirm each invalid data case results in `400 Bad Request`.
2. **Non-Existent Resources**: Confirm attempts to access non-existent bookings result in `404 Not Found`.
3. **Unauthorized Access**: Verify attempts without valid authentication return `403 Forbidden` or `401 Unauthorized`.
4. **Malformed Requests**: Ensure that malformed JSON or invalid HTTP methods result in `400 Bad Request` or `405 Method Not Allowed`.

## Conclusion

This documentation covers setting up and testing error scenarios to ensure the API correctly handles invalid input, missing resources, unauthorized requests, and malformed requests.


