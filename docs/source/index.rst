OpenGRIS: Open Standard for Grid Resource Scheduling
====================================================

OpenGRIS is an open standard for grid resource scheduling. It provides a standardized way to prepare, distribute, and
execute tasks across elastic and heterogeneous computing environments.

OpenGRIS contains the following components:

- Scheduler: Manages tasks using a stable and language-agnostic communication protocol.
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
