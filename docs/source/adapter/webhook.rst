Adapter Webhook API
===================

The Adapter Webhook API provides a single unified endpoint for the scheduler to request the start and stop of workers.

Webhook Specification
---------------------

.. openapi:: ../_static/schema.yaml

Webhook Request Examples
------------------------

Example: start_worker (minimal)

.. code-block:: json

    {
      "action": "start_worker",
      "worker_id": ""
    }

Example: start_worker (with suggested worker_id and auth)

.. code-block:: json

    {
      "action": "start_worker",
      "worker_id": "worker-12345",
      "metadata": {
        "instance_type": "m8gd.16xlarge",
        "region": "us-east-1",
        "image_tag": "python:3.11-alpine",
        "tags": ["python"]
      },
      "auth_token": "<your-webhook-token>"
    }

Example: shutdown_worker

.. code-block:: json

    {
      "action": "shutdown_worker",
      "worker_id": "worker-12345"
    }

Webhook Reponse Examples
------------------------

Success (200)

.. code-block:: json

    {
      "status": "ok",
      "worker_id": "worker-12345",
      "metadata": {
        "instance_type": "m8gd.16xlarge",
        "region": "us-east-1"
      }
    }

Client error (400)

.. code-block:: json

    {
      "error": "missing required field 'action'"
    }

Unauthorized (401)

.. code-block:: json

    {
      "error": "invalid auth_token"
    }

Rate limit / resource unavailable (429)

.. code-block:: json

    {
      "error": "no available capacity, retry later"
    }

Server error (500)

.. code-block:: json

    {
      "error": "internal provisioning error"
    }

Scaling Controller Interaction
------------------------------

Interactions with the scaling controller can be complex given the possibility of dependency failures, network issues,
delays, etc. Here are some guidelines to ensure reliable communication between the scaling controller and the adapter:

- **Return the correct error code**: Return an appropriate HTTP status to indicate how the controller should proceed:
    - 200: action completed successfully (include resulting worker_id)
    - 400: client error — fix the request (do not retry)
    - 401: unauthorized — reject immediately
    - 429: retryable (rate limiting or temporary lack of capacity) — controller should back off and retry
    - 5xx: server error — controller should retry with backoff
- **Blocking vs asynchronous actions**: The adapter SHOULD complete the requested action before returning 200. If the
  adapter cannot complete the action synchronously, return 202 Accepted and provide a status endpoint or include a
  request_id in the response so the controller can poll for completion.
- **Idempotency**: Support idempotent requests. If a controller retries the same start/stop request, the adapter should
  detect duplicates (eg. via a request id in metadata) and avoid double-provisioning.
- **Validation**: Validate incoming payloads and return 400 for malformed requests. Include a helpful error message in the
  body following the Error schema.
- **Security**: Require an auth_token or use mutual TLS. Reject invalid auth with 401.
