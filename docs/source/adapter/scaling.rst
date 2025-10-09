Scaling Controller
==================

The scaling controller determines when to start and stop workers based on task and other state updates emitted by 
the scheduler. The scaling controller implements a scaling policy that can suit specific needs such as:

- Cost optimized: Start and stop workers based on task queue length and worker idle time.
- Performance optimized: Start workers as soon as tasks are available.
- Time constrained: Start workers based on a schedule.
- Resource constrained: Start and stop workers based on available budget or resource quotas.

To perform a scaling action, the scaling controller sends a webhook request to the adapter. The adapter then starts or
stops workers as requested.

Scaling Controller Interface
----------------------------

.. code-block:: python

    import abc

    from scaler.protocol.python.message import InformationSnapshot
    from scaler.utility.mixins import Reporter

    class ScalingController(Reporter, abc.ABC):
        def get_status(self):
            pass
    
        async def on_snapshot(self, information_snapshot: InformationSnapshot):
            pass

Implementing a Scaling Controller
---------------------------------

Some pointers to implement a robust Scaling Controller:

- Decide when to scale up:
    - if backlog > threshold OR average task wait time > latency_target, issue a start_worker_group webhook
    - include suggested instance type, region, and any labels in metadata
- Decide when to scale down:
    - if backlog is small or task wait time is low, issue a shutdown_worker_group webhook for specific worker_group_id
- Validate webhook responses: only treat action as completed when adapter returns 200
- Use exponential backoff for retries when receiving 429 or 5xx responses from the adapter
- Idempotency: you may include a request identifier in metadata if your adapter supports it, or detect repeated requests
  and avoid duplicate actions.
- Observability: emit metrics for actions issued, successful starts/stops, failures, and retries
