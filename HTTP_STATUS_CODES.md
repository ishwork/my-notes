# HTTP Status Codes: Key Codes and Usage in JavaScript

This guide covers the most important HTTP status codes, their meanings, and how to use them in JavaScript for web development, API calls, and error handling.

## Table of Contents
1. [Overview of HTTP Status Codes](#overview-of-http-status-codes)
2. [Common Status Codes](#common-status-codes)
3. [Usage in JavaScript](#usage-in-javascript)
4. [References](#references)

---

## Overview of HTTP Status Codes

HTTP status codes are issued by a server in response to a client's request. They indicate whether a request was successful, failed, or requires further action.

- **1xx (Informational):** Request received, continuing process
- **2xx (Success):** Request was successfully received, understood, and accepted
- **3xx (Redirection):** Further action needs to be taken to complete the request
- **4xx (Client Error):** The request contains bad syntax or cannot be fulfilled
- **5xx (Server Error):** The server failed to fulfill a valid request

## Common Status Codes

| Code | Meaning                | Typical Usage                          |
|------|------------------------|----------------------------------------|
| 200  | OK                     | Successful GET/POST/PUT/DELETE         |
| 201  | Created                | Resource created (POST/PUT)            |
| 204  | No Content             | Success, no response body              |
| 301  | Moved Permanently      | Permanent redirect                     |
| 302  | Found                  | Temporary redirect                     |
| 304  | Not Modified           | Resource not changed (caching)         |
| 400  | Bad Request            | Invalid request from client            |
| 401  | Unauthorized           | Authentication required                |
| 403  | Forbidden              | No permission to access                |
| 404  | Not Found              | Resource not found                     |
| 405  | Method Not Allowed     | HTTP method not supported              |
| 409  | Conflict               | Resource conflict (e.g., duplicate)    |
| 429  | Too Many Requests      | Rate limiting                          |
| 500  | Internal Server Error  | Server error                           |
| 502  | Bad Gateway            | Invalid response from upstream server  |
| 503  | Service Unavailable    | Server temporarily unavailable         |
| 504  | Gateway Timeout        | Upstream server timeout                |

## Usage in JavaScript
### Alternative: Using `fetch` with .then
```js
fetch('https://api.example.com/data')
  .then(response => {
    if (response.status === 200) {
      return response.json();
    } else if (response.status === 404) {
      throw new Error('Resource not found');
    } else if (response.status === 401) {
      window.location.href = '/login';
    } else if (response.status >= 500) {
      throw new Error('Server error, please try again later');
    } else {
      throw new Error('Unexpected response');
    }
  })
  .then(data => {
    if (data) {
      console.log('Data:', data);
    }
  })
  .catch(error => {
    console.error(error);
  });
```

When making HTTP requests in JavaScript (using `fetch`, `axios`, etc.), check the status code to handle responses appropriately.

### Example: Using `fetch` with try/catch
```js
async function fetchData() {
  try {
    const response = await fetch('https://api.example.com/data');
    if (response.status === 200) {
      // Success: parse JSON
      const data = await response.json();
      console.log('Data:', data);
    } else if (response.status === 404) {
      // Not found: show error message
      throw new Error('Resource not found');
    } else if (response.status === 401) {
      // Unauthorized: redirect to login
      window.location.href = '/login';
    } else if (response.status >= 500) {
      // Server error: show generic error
      throw new Error('Server error, please try again later');
    } else {
      // Other status codes
      throw new Error('Unexpected response');
    }
  } catch (error) {
    console.error(error);
  }
}

fetchData();
```

### Example: Using `axios`
```js
axios.get('https://api.example.com/data')
  .then(response => {
    // Axios throws for non-2xx status codes by default
    console.log('Data:', response.data);
  })
  .catch(error => {
    if (error.response) {
      switch (error.response.status) {
        case 404:
          alert('Not found!');
          break;
        case 401:
          alert('Please log in.');
          break;
        case 500:
          alert('Server error.');
          break;
        default:
          alert('Error: ' + error.response.status);
      }
    } else {
      alert('Network error');
    }
  });
```

## References
- [MDN: HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [RFC 7231: HTTP/1.1 Semantics and Content](https://tools.ietf.org/html/rfc7231)
- [Axios Documentation](https://axios-http.com/docs/handling_errors)
