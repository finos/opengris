OpenGRIS: Open Standard for Grid Resource Scheduling
====================================================

OpenGRIS is an open standard for grid resource scheduling. It provides a standardized way to prepare, distribute, and
execute tasks across elastic and heterogeneous computing environments.

.. https://asciiflow.com/#/share/eJytVLFuwjAQ%2FRXkmaVVh8JW8QEdaMWSxQQLKK6NHFOBEFKFOnZgiGiGjhk7VZ0qviZf0tgVJol9TlphWcLBd%2B%2B9e3fJGjH8SFCXLShtI4pXRKAuWgfoiYhoylmAupftAC3z387VRX5aqX%2Bu1UmSpcwfAtSqrix%2BzeJn795ZSfX5KicImCN82w8xnbLx70NrQIYTzmeRB3X%2FnQcOuJgRkcXvhgGA73EmBae0FAzWBZe%2F0%2FJ0SOy6TzSbW4QzoZJsgo8idZk3IzyXJ%2Bmp32IjwNMFQ7T%2FtM3yqk3Kd%2B42pLmAAnvao1PCZNn6XYnQltDMP2O4ToKDCqDqdIejWet%2BPsKSRABhSc%2BJALTUff8Feg9U6e9q0WOXqQfTk344IaOFHvhtldQ%2F37UvWdrUbuv%2BAxr4Qqhn4htNgL9BsPU1M789R3sSrwBAyv%2BbYOD2By%2Bdr%2FyyvVYOPEgN1xG89utobe3L7fCBhFKhZG8vf4Y4z05c33slqS%2B5wGNS1%2B3iAqDgyWwMjDZo8wNBgNAj)

.. code-block::

                     ┌──────────┐                            ┌──────┐   
                     │Scaling   │ Webhooks              ┌───►│Worker├──┐
                     │Controller├─────────┐   ┌───────┐ │    └──────┘  │
                     └──────────┘         ├──►│Adapter├─┤    ┌──────┐  │
       ┌──────┐           ▲               │   └───────┘ └───►│Worker├──┤
    ┌──┤Client├───┐       │               │                  └──────┘  │
    │  └──────┘   │       │ Task Updates  │                            │
    │  ┌──────┐   │  ┌────┴────┐          │                  ┌──────┐  │
    ├──┤Client├───┼─►│Scheduler│          │   ┌───────┐ ┌───►│Worker├──┤
    │  └──────┘   │  └────┬────┘          └──►│Adapter├─┤    └──────┘  │
    │  ┌──────┐   │       │                   └───────┘ │    ┌──────┐  │
    ├──┤Client├───┘       │                             └───►│Worker├──┤
    │  └──────┘           ▼                                  └──────┘  │
    │                 ┌───────┐                                        │
    └────────────────►│Object │◄───────────────────────────────────────┘
                      │Storage│                                         
                      └───────┘                                         

OpenGRIS contains the following components:

- Scheduler: Manages tasks using a stable and language-agnostic communication protocol.
    - Includes the Scaling Controller which issues webhook requests to the Adapters.
    - Reference scheduler implementation: `OpenGRIS Scaler <https://github.com/finos/opengris-scaler>`_.
- Worker: Executes tasks assigned by the scheduler.
    - Reference worker implementation is part of OpenGRIS Scaler.
    - IBM Spectrum Symphony worker implementation:
      `Worker Code <https://github.com/finos/opengris-scaler/tree/main/scaler/worker/symphony>`_.
- Adapter: Interfaces with the scheduler to start and stop workers.
    - Reference adapter implementation for VMs:
      `VM Adapter Pull Request <https://github.com/finos/opengris-scaler/pull/190>`_.
    - Documentation: `Adapter Webhook API <adapter/webhook.html>`_.
- Client Libraries: Helper libraries for implicit parallelization.
    - Python client library is part of OpenGRIS Scaler.
    - Graph parallelization library: `OpenGRIS Pargraph <https://github.com/finos/opengris-pargraph>`_.
    - Map-reduce parallelization library: `OpenGRIS Parfun <https://github.com/finos/opengris-parfun>`_.
- Object Storage: For sharing data objects between clients, schedulers, and workers.
    - Reference object storage implementation:
      `OpenGRIS Object Storage <https://github.com/finos/opengris-scaler/tree/main/scaler/object_storage>`_.

Contents
========

.. toctree::
   :maxdepth: 2

   protocol/scheduler
   adapter/scaling
   adapter/webhook
