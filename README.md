# gogs-drone-docker
For the better CI as well as CD using gogs &amp; drone &amp; docker



- **About**

  - [`gogs`](https://gogs.io/)

    > Gogs is a painless self-hosted Git service

  - [`drone`](https://drone.io/) 

    > Drone is a Continuous Delivery platform built on Docker, written in Go

  - `docker` 


___



- **Precondition**

  - [x] installed docker , [If you haven't installed yet, click here.](https://docs.docker.com/install/)

  - [x] installed docker-compose , [If you haven't installed yet, click here.](https://docs.docker.com/compose/install/)


___



- **Configure**

  - create `DRONE_SECRET` by using next command

    ```shell
    openssl rand -hex 16
    ```



___



- **Run**

  ```shell
  docker-compose up -d
  ```



___



**How to use gogs**

> open `https://{$ip}:3000` on brower
>
> O(∩_∩)O哈哈~ , I prefer domain name access.



___



**How to use drone**

> drone is core~ , get started is easy~
>
> open `https://{$ip}:8000` on brower