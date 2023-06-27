# containers_in_iot

IOT development is complex.
    - The hardware is providing more features.
    - The range of skills needed is high
    - Deployment process (maintain software for the LONG term.)

We may sinplify development by Modularizarion. This way, we abstract the work of different teams:
    1. Modularizing hardware - Suggest we use RISCV
    2. Modularizing software - we use building blocks (libraries, services)

Containers are not truly portable. They are kernel dependent

Containers are heavy, like Virtual Machines!

It is hard to access the hardware using containers by default. You have to share the hardware device to the Container.