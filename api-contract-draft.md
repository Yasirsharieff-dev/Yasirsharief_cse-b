# Draft API Contract

This is an initial interface proposal to guide implementation. It is not a deployed API specification.

## Submit an inference request

`POST /api/v1/inference`

Example request:

```json
{
  "input": {
    "text": "Example content to analyse"
  },
  "mode": "sync"
}
```

Example synchronous response (proposed):

```json
{
  "request_id": "generated-request-id",
  "status": "completed",
  "result": {
    "label": "example"
  }
}
```

For asynchronous processing, clients may submit with `mode` set to `async`. The service is expected to return an accepted status and request identifier; clients can retrieve the result using the status endpoint below.

## Retrieve request status/result

`GET /api/v1/inference/{request_id}`

Proposed response fields:
- `request_id`
- `status` — for example, `queued`, `processing`, `completed` or `failed`
- `result` — present when completed
- `error` — present when failed

## Design considerations

- Validate inputs and return consistent error responses.
- Attach a correlation ID to each request and propagate it across events.
- Define authentication and rate-limiting policy at the gateway.
- Make asynchronous consumers idempotent.
- Finalise schemas, status codes and error formats during implementation.
