Adapter Webhook API
===================

The Adapter Webhook API provides a single unified endpoint for the scheduler to request the start and stop of workers.

Here is a high-level diagram illustrating how the Adapter Webhook API fits into the overall architecture:

.. code-block::

                                                Start  ┌────────┐
                                            ┌──────────► Worker │
                                            │          └────────┘
                                            │                    
    ┌───────────┐ Webhook Requests  ┌───────┴─┐ Start  ┌────────┐
    │  Scaling  ├───────────────────► Adapter ├────────► Worker │
    │  Policy   │                   └───────┬─┘        └────────┘
    └─────▲─────┘                           │                    
          │                                 │   Start  ┌────────┐
          │ Task Updates                    └──────────► Worker │
          │                                            └────────┘
    ┌─────┴─────┐                                                
    │ Scheduler │                                                
    └───────────┘                                                

Webhook Specification
---------------------

.. openapi:: ../_static/schema.yaml

Scaling Controller Interaction
------------------------------

Interactions with the scaling controller can be complex given the possbility of dependency failures, network issues, 
delays, etc. Here are some guidelines to ensure the scaling controller can communicate with the adapter reliably:

- **Return the correct error code**: The scaling controller relies on the adapter to return the correct HTTP status code
  to indicate success or failure of the request and whether the request should be retried.
- **Return when the action is finished**: The scaling controller relies on the adapter to return a response only when
  the requested action is finished. This ensures that the scaling controller has an accurate view of the current
  state of workers.
