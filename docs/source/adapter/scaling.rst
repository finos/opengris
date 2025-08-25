Scaling Controller
==================

The scaling controller determines when to start and stop workers based on task and other state updates emitted by 
the scheduler. The scaling controller implements a scaling policy that can suit specific needs such as:

- Cost optimized: Start and stop workers based on task queue length and worker idle time.
- Performance optimized: Start workers as soon as tasks are available.
- Time constrained: Start workers based on a schedule.
- Resource constrained: Start and stop workers based on available budget or resource quotas.

To perform a scaling action, the scaling controller sends a webhook request to the adapter. The adapter then starts or
stops workers as requested. For more details on the adapter, see the `Adapter Webhook API <webhook.html>`_.

Scaling Controller Interface
----------------------------

.. code-block:: python

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
