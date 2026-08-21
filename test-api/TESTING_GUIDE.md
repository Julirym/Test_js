# Writing API Tests

## Before You Start

- Read the API documentation and identify the endpoint, HTTP method, required headers, request body, and expected response.
- Use test data that is isolated and safe to create, update, and delete.
- Keep secrets, tokens, and passwords out of test files. Store them in environment variables instead.

## Test Structure

Each test should cover one clear behavior and follow the Arrange, Act, Assert pattern:

1. Arrange the required test data and request configuration.
2. Act by sending one request to the API.
3. Assert the status code, response body, headers, and any important side effects.

Use descriptive test names that state the expected result, for example:

```text
returns 200 and the requested user when a valid user ID is provided
returns 400 when the request body is missing a required field
```

## What To Check

- Expected success status codes, response fields, and data types.
- Validation errors for missing, invalid, or boundary-value input.
- Authentication and authorization behavior for permitted and forbidden users.
- Error responses for unknown resources and unsupported methods.
- Side effects, such as whether a record was created, updated, or deleted correctly.

## Good Practices

- Keep tests independent: one test must not rely on another test running first.
- Use constants or fixtures for reusable request data.
- Include both positive and negative scenarios.
- Clean up data created by a test whenever possible.
- Do not assert the entire response unless every field is part of the contract; check the fields that matter.
- Make failures easy to diagnose by including the expected and actual values in assertions.

## Review Checklist

Before submitting a test, confirm that it is deterministic, readable, isolated, and can run repeatedly with the same result.
