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

    from scaler.protocol.python.message import (
        StateBalanceAdvice,
        StateClient,
        StateGraphTask,
        StateObject,
        StateScheduler,
        StateTask,
        StateWorker
    )
    from scaler.utility.mixins import Reporter

    class ScalingController(Reporter, abc.ABC):
        async def on_state_client(self, state_client: StateClient):
            pass
    
        async def on_state_object(self, state_object: StateObject):
            pass
    
        async def on_state_balance_advice(self, state_balance_advice: StateBalanceAdvice):
            pass
    
        async def on_state_scheduler(self, state_scheduler: StateScheduler):
            pass
    
        async def on_state_worker(self, state_worker: StateWorker):
            pass
    
        async def on_state_task(self, state_task: StateTask):
            pass
    
        async def on_state_graph_task(self, state_graph_task: StateGraphTask):
            pass

In most cases, the scaling controller will only need to implement the ``on_state_task`` and ``on_state_worker``
handlers. The other handlers are provided for completeness and future use cases.

Implementing a Scaling Controller
---------------------------------

Some pointers to implement a robust Scaling Controller:

- Receive scheduler state updates via the provided on_state_* handlers and keep a small internal model of:
    - current running workers and their states
    - pending tasks and backlog size
    - recent failures or rate-limit signals from the adapter
- Decide when to scale up:
    - if backlog > threshold OR average task wait time > latency_target, issue a start_worker webhook
    - include suggested instance type, region, and any labels in metadata
- Decide when to scale down:
    - if backlog is small or task wait time is low, issue a shutdown_worker webhook for specific worker_id
- Validate webhook responses: only treat action as completed when adapter returns 200 and the returned worker_id is recorded
- Use exponential backoff for retries when receiving 429 or 5xx responses from the adapter
- Idempotency: you may include a request identifier in metadata if your adapter supports it, or detect repeated requests
  and avoid duplicate actions.
- Observability: emit metrics for actions issued, successful starts/stops, failures, and retries
