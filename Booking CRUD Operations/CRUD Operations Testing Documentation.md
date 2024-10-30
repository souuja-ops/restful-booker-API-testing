# Restful-Booker CRUD Operations Testing Documentation

This documentation provides a step-by-step guide to set up, run, and test CRUD operations on the Restful-Booker API. The API allows you to create, retrieve, update, and delete booking information for a hotel booking system.

## Prerequisites

1. **Postman**: Make sure Postman is installed on your computer. [Download Postman here](https://www.postman.com/downloads/).
2. **API Base URL**: We will be using the base URL for the Restful-Booker API - `https://restful-booker.herokuapp.com/`.

## Setting Up Postman Environment

1. Open Postman and create a new environment by going to **Environments** > **Add New Environment**.
2. Add the following variables to the environment:
   - `baseUrl`: Set to `https://restful-booker.herokuapp.com`
   - `token`: Leave empty for now (we will generate this in the authentication step)
   - `bookingId`: Leave empty (it will be set after creating a booking)
3. Save the environment and select it from the top-right environment dropdown.

## Authentication

1. **Generate Auth Token**:
   - In Postman, create a new `POST` request.
   - Set the request URL to `{{baseUrl}}/auth`.
   - In the **Body** tab, select `raw`, set the format to `JSON`, and enter the following JSON:
     ```json
     {
       "username": "admin",
       "password": "password123"
     }
     ```
   - Send the request. A `token` will be returned in the response.
   - Copy the token value and add it to your environment under the `token` variable.

## Booking CRUD Operations

### 1. **Create New Bookings**

   - **Description**: Creates a new booking in the system.
   - **Request Type**: `POST`
   - **Endpoint**: `{{baseUrl}}/booking`
   - **Headers**:
     - `Content-Type`: `application/json`
     - `Accept`: `application/json`
   - **Body**: Use the following JSON structure in the **Body** tab (set to `raw`):
     ```json
     {
       "firstname": "John",
       "lastname": "Doe",
       "totalprice": 150,
       "depositpaid": true,
       "bookingdates": {
         "checkin": "2024-10-30",
         "checkout": "2024-11-02"
       },
       "additionalneeds": "Breakfast"
     }
     ```
   - **Response**: On success, the response will include a unique `bookingid`. Store this ID in the `bookingId` variable in the environment.

### 2. **Retrieve Booking Details**

   - **Description**: Retrieves booking details using the booking ID.
   - **Request Type**: `GET`
   - **Endpoint**: `{{baseUrl}}/booking/{{bookingId}}`
   - **Headers**:
     - `Accept`: `application/json`
   - **Steps**:
     1. Ensure that `bookingId` is set in the environment.
     2. Send the `GET` request.
   - **Response**: The response should contain the booking details, including guest name, total price, booking dates, and any additional needs.

### 3. **Update Existing Bookings**

   - **Description**: Updates an existing booking using a full update.
   - **Request Type**: `PUT`
   - **Endpoint**: `{{baseUrl}}/booking/{{bookingId}}`
   - **Headers**:
     - `Content-Type`: `application/json`
     - `Accept`: `application/json`
     - `Cookie`: `token={{token}}`
   - **Body**: Modify any details you wish to update. For example:
     ```json
     {
       "firstname": "Jane",
       "lastname": "Doe",
       "totalprice": 200,
       "depositpaid": false,
       "bookingdates": {
         "checkin": "2024-11-01",
         "checkout": "2024-11-03"
       },
       "additionalneeds": "Lunch"
     }
     ```
   - **Response**: The response should reflect the updated details of the booking.

### 4. **Delete Bookings**

   - **Description**: Deletes a booking from the system.
   - **Request Type**: `DELETE`
   - **Endpoint**: `{{baseUrl}}/booking/{{bookingId}}`
   - **Headers**:
     - `Cookie`: `token={{token}}`
   - **Steps**:
     1. Ensure `token` and `bookingId` are set in the environment.
     2. Send the `DELETE` request.
   - **Response**: A successful deletion will result in a `201 Created` response.

### 5. **Handle Concurrent Modifications**

   - **Description**: Demonstrates handling concurrent updates on the same booking by simulating two updates at the same time.
   - **Steps**:
     1. Duplicate the **Update Existing Bookings** request created in Step 3.
     2. Open both requests and set slightly different body values (for example, `totalprice: 250` in one and `totalprice: 300` in the other).
     3. Send both requests as quickly as possible.
   - **Expected Outcome**: The API should process both updates; however, only one final state will be saved. Review both responses to understand which update persisted.

## Testing Checklist

To confirm all CRUD operations work as expected:
- **Create** a new booking and verify the response includes a unique booking ID.
- **Retrieve** the booking by its ID and confirm details match the created data.
- **Update** the booking with modified details and confirm the update is reflected in the response.
- **Delete** the booking and verify that retrieving the booking by ID results in an error or no data.
- **Concurrent Update**: Confirm that concurrent updates are handled without conflicts or unexpected errors.

## Conclusion

Following these steps will ensure thorough testing of CRUD operations within the Restful-Booker API. Document and address any errors or inconsistencies encountered during testing.


