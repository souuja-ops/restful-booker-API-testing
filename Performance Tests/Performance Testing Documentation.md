# Restful-Booker API Performance Testing Documentation

This documentation details how to set up, run, and test the performance of the **Restful-Booker API** with specific focus on response times, concurrent requests, and rate limiting behavior.

## Prerequisites

1. **Postman**: Ensure Postman is installed. [Download here](https://www.postman.com/downloads/).
2. **Newman** (Optional): To run Postman collections for automated performance testing, install Newman via npm:
   ```bash
   npm install -g newman

3. **API Base URL**: Set up an environment variable in Postman for baseUrl, e.g., https://restful-booker.herokuapp.com.

## Set Up Postman Environment
1. In Postman, go to Environments and create a new environment called Restful-Booker.
2. Add the following variables:
   - `baseUrl`: Set to `https://restful-booker.herokuapp.com`
   - `token`: Leave this empty initially; update it after generating an auth token.
   - `bookingId`: Leave this empty initially; update it once a booking is created.

## Authentication
Some endpoints require a token. Here’s how to obtain it:

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

## Performance Tests
### 1. Response Times
**Objective**: Confirm API response times are within acceptable limits.

**Steps**:

1. Create a Test Collection in Postman specifically for performance testing.
2. Add a request to `{{baseUrl}}/ping` or any endpoint you want to test.
3. In the Scripts > Post-response tab, add the following script to assert response times:
   ```javascript
   pm.test("Response time is below 200ms", function () {
       pm.expect(pm.response.responseTime).to.be.below(200);
   });

4. Run the Collection

    - Go to Collection Runner and select your collection.
    - Set the number of iterations (e.g., 10 for quick tests).
    - Click Run to view response times in the results.
    - **Optional**: Use Newman to automate tests:
    ```bash
    newman run [collection-file].json
    ```
Review the response times in the Newman output.

### 2. Concurrent Requests
**Objective**: Verify the API’s stability and response consistency under multiple concurrent requests.

**Steps**:

1. Create a Concurrent Requests Collection:
    - Add a request to `{{baseUrl}}/booking` or a similar endpoint.
    - Add the following script in the Scripts > Post-response tab to log each request’s response time:
    ```javascript
    console.log("Response time:", pm.response.responseTime, "Status code:", pm.response.status);
    ```
2. Run Concurrent Requests:
    - In Collection Runner, select the Concurrent Requests Collection.
    - Set Iterations to 50 or more to simulate multiple requests.
    - Click Run and observe the responses.
3. Analyze Results:
    - Check for high variability in response times or error codes.
    - Note any increase in error rates, timeouts, or unexpected delays.
    - **Optional**: For more advanced concurrent testing, use Apache JMeter or k6 (CLI tool for testing performance).

### 3. Rate Limiting Behavior
**Objective**: Test if rate limits are in place and how they are enforced.

**Steps**:

1. Configure a High-Frequency Test:
    - Use an endpoint like `{{baseUrl}}/booking` and set up a high number of requests in the Collection Runner (e.g., 100).
2. Check Rate Limiting Headers:
    - In the Scripts > Post-response tab, add a check for rate limiting headers:
    ```javascript
    pm.test("Check for rate limiting headers", function () {
        pm.expect(pm.response.headers.has("X-RateLimit-Limit")).to.be.true;
        pm.expect(pm.response.headers.has("X-RateLimit-Remaining")).to.be.true;
    });
    ```
3. Monitor Responses for 429 Errors:
    - A 429 Too Many Requests error indicates a rate limit has been triggered.
    - If you receive this error, note the frequency and interval to identify the limit threshold.
4. Document Rate Limits:
    - If the rate limit behavior is unclear, record your observations on the expected limits or lack thereof.
    - Automate with Newman:

    ```bash
    newman run [collection-file].json
    ```
    - Review output to confirm if rate limiting occurs and observe the consistency of responses.
## Summary
These tests ensure that the Restful-Booker API is robust and performs reliably under varying loads. Response Time tests check latency, Concurrent Requests test the API’s stability under high traffic, and Rate Limiting identifies request thresholds for optimal usage.

To expand performance testing or integrate with CI/CD, use automated tools like Newman or CI environments that support Postman collections.

### Running Tests with Newman (Optional)
To run these performance tests in bulk or as part of CI/CD, use Newman:
```
newman run [collection-file].json --reporters cli,html --reporter-html-export [path-to-report].html
```
Newman will produce a detailed report with response times and any rate-limiting information for further analysis.