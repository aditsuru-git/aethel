# 00: Core Architecture and Setup

**Purpose:** This document defines the foundational technology stack, project structure, core patterns, and development environment for the Aethel application. All developers must follow these standards to ensure consistency and maintainability.

## 1. Technology Stack

The following tools are required for the project. Any changes must be formally approved.

- **Language:** TypeScript
- **Library:** React
- **State Management:** Redux
- **Router:** React Router
- **OpenAPI Client SDK:** `openapi-client-axios` (enforces OpenAPI contract with types)
- **Containerization:** Docker

## 2. Error Handling

Client SDK API calls must be wrapped in `try/catch` blocks.

Below is a concise list of the **types of errors you need to handle in a frontend React app using `openapi-client-axios`** and how to handle each:

### 2.1. Network Errors (Server Offline / No Internet)

- The request never reaches the backend (server is down, user is offline).

**How to handle:**

- Catch Axios/network errors in a `try/catch` block.
- Check if `err.response` is undefined → network issue.

```ts
if (!err.response) {
	showErrorUI("Server is offline or network is down.");
}
```

### 2.2. HTTP Errors (4xx / 5xx)

- Backend responds but with an error status (e.g., 404, 500).

**How to handle:**

- Check `err.response.status` for every request.
- Show user-friendly messages or retry options.

```ts
if (err.response) {
	switch (err.response.status) {
		case 404:
			showErrorUI("Resource not found");
			break;
		case 500:
			showErrorUI("Server error, please try again later");
			break;
	}
}
```

### 2.3. Timeout Errors

- Request takes too long and is aborted.

**How to handle:**

- Set Axios `timeout` in config (to be discussed with the lead).
- Catch the error and show a “request timed out” message.

```ts
if (err.code === "ECONNABORTED") {
	showErrorUI("Request timed out");
}
```

### 2.4. OpenAPI Contract Violations

- `openapi-client-axios` ensures requests/responses match the OpenAPI schema.
- You must validate all fields in the request body before making the API call.

### 2.5. Validation Errors (Form / User Input)

- User submits invalid data according to schema (e.g., missing required field).

**How to handle:**

- Validate data **before sending** using manual validation.
- Show inline error messages in the UI.

### 2.6. Unexpected Errors

- Any other runtime or JavaScript error (parsing issues, unhandled exceptions).

**How to handle:**

- Use a global error boundary in React.
- Log errors for debugging.
- Show a fallback UI with a “Something went wrong” message.

```tsx
<ErrorBoundary fallback={<div>Something went wrong</div>}>
	<App />
</ErrorBoundary>
```
