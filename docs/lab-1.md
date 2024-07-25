# Introduction

The goal of this lab session is to let you practice the creation of
Docker images using Dockerfiles, and the inspectation of Docker
containers. Note that the Java image produced by this session **will be
used in the lab sessions** of the following chapters. So please
prioritize your work and ensure that it is done before the next lecture.

To submit the answers to this lab session, you need to copy this
markdown document to the `reports/` directory of this Git repository and
fill in your answers. This should be done before the beginning of the
next course.

# Prerequisite

This lab session requires the use of Docker Desktop. If you are using
the computer of ESIGELEC, the Docker Desktop is preinstalled. If you are
using your personal computer, please download Docker Desktop from the
official website of Docker, Inc.

<https://www.docker.com/products/docker-desktop/>

Run the command `docker -v` to ensure that the `docker` command line is
available in your terminal. Please paste the result below.

  

    *\[answer\]*

  

  

Tell M. Huang your GitHub accounts so that you can have access to the
target Git repository.

Clone the Git repository by replacing the `$TEAM` by your actual team in
lowercase.

``` sh
git clone git@github.com:mincong-h/containers-${TEAM}.git
```

For example, if you are in the `red` team:

``` sh
git clone git@github.com:mincong-h/containers-red.git
```

  

Perform the following actions to ensure that the project can be
installed on your machine.

``` sh
cd containers-red/weekend-server
mvn install
```

# Exercises

## Exercise 1: Create a Java image

Create a Java image. The image should be built on top of OpenJDK 21 and
Spring Boot. It should accept external traffic using port 8080 of the
container. The image needs to have the environment variable `APP_TEAM`
(e.g. `red`) to identify themselves as the owners of the service. This
image must be built using the `docker build` command and must be run as
a Docker container.

At the end of the lab session, your project is expected to meet the
following requirements:

- The Dockerfile is implemented correctly.
- The Dockerfile can be built in one command using docker CLI.
- The image is published to the repository `weekend-server-${TEAM}`,
  e.g. `weekend-server-red`.
- The image can be run using docker CLI, the container can be seen from
  Docker runtime.
- When sending an HTTP request to the server via URL
  <http://localhost:8080>, it should return a valid response containing
  an ID and a description explaining whether today is the weekend.

### 1.1 Create a JAR without Docker

Create a Java Archive (JAR) file using the source code available in your
Git repository, under the directory `weekend-server/`. You can build it
using Maven (`mvn`). Please provide the command that you used below.
Also, what is the name of the JAR file? Can you start the web server
using the `java` command, e.g. `java -jar /path/to/my.jar`? What do you
see if you visit the URL <http://localhost:8080> in your browser?

  

    *\[answer\]*

  

  

### 1.2 Define a Dockerfile

Define a Dockerfile, which is the first step of creating a Docker image.
Create a Dockerfile based on one of the JDK images available on Docker
Hub <https://hub.docker.com/_/eclipse-temurin>. You have to pick a
version that is compatible with JDK 21, the latest long-term support
(LTS) version. There are multiple choices, ideally, you can find a small
image, which reduces the size of the container. The Java Archive (JAR)
file should be built outside of Docker via the command you used in the
previous question. Expose port 8080 of the container so that it can
accept requests from the clients. The Dockerfile should be stored under
the following directory: `${PROJECT_ROOT}/weekend-server/Dockerfile`.
Please commit the changes and push them to your Git repository.

  

    *\[answer: commit the Dockerfile to the Git repository\]*

  

  

### 1.2 Build your Docker image

Now you have your Dockerfile, try to build your Docker image using the
command `docker build` with additional options. You are expected to
build your image with the name of the repository as
`weekend-server-${team}`. Once your Docker image is built successfully,
please paste your command below. This build command should be executed
from the root directory of the project `${PROJECT_ROOT}`.

  

    *\[answer: docker build …\]*

  

  

Verify that your image is successfully built by inspecting the image via
the command `docker images`. Then, fill information in the table below.

  

| Field      | Value |
|:-----------|------:|
| Repository |       |
| Tag        |       |
| Image ID   |       |
| Created    |       |
| Size       |       |

  

  

### 1.3 Run your Docker image

Run your Docker image using the command `docker run`. Your container
should receive HTTP requests on port 8080. Once your Docker container is
running, take a screenshot of your terminal showing the logs from Spring
Boot.

  

    *\[Paste your command here: docker run …\]*

    *\[Paste your screenshot here\]*

  

  

Then, use curl to perform an HTTP request to the Java server and observe
the response.

  

    *\[Paste your curl command and result here\]*

  

  

### 1.4 Inspect Docker container

Inspect the docker container using the command `docker ps`. What is the
container ID of your Java container?

    *\[answer\]*

  

  

### 1.5 Building the JAR in Docker

In the previous step “1.2 Define a Dockerfile”, the JAR file wasn’t part
of Docker build. Can you refactor the Dockerfile, so that you build the
JAR when building the Docker image?

> Hint: this is called a multi-stage build. You can use
> `COPY --from=${ANOTHER_IMAGE}` to copy the artifact built by another
> image. For example, you can copy the JAR file from a Maven image, and
> paste it to a JDK image. See more informations in
> <https://docs.docker.com/build/building/multi-stage/#name-your-build-stages>

## Exercise 2: Run a database image

We have a MySQL database that is ready to be used. You can run it using
the following command:

### 2.1 Run MySQL as a container

``` sh
docker run \
    --name weekend-mysql \
    --rm \
    -d \
    -p 3306:3306 \
    -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
    mysql:9
```

Ensure that you can visit it using the command below:

``` sh
docker exec -it weekend-mysql mysql -u root
```

  

    *\[Paste the information that you see from the MySQL shell\]*

  

### 2.1 Initialize database

Import the following SQL script into your MySQL database. You can
execute it from the root of the Git repository:

``` sh
docker exec -i weekend-mysql mysql -u root < weekend-mysql/init.sql
```

  

Now if you connect to MySQL again using the `docker exec` command, do
you see the new data inserted there?

  

    *\[answer: show the database and tables here using SQL commands\]*

  

  

Now, complete the `init.sql` script for all the days of the week **in
English**. Please commit the changes to the Git repository directly.
Here is the French version if you need inspiration:

| Weekday   | Value                       |
|:----------|:----------------------------|
| Monday    | `Non.`                      |
| Tuesday   | `Non.`                      |
| Wednesday | `Non.`                      |
| Thursday  | `Bientôt…`                  |
| Friday    | `Presque, mais pas encore.` |
| Saturday  | `C'est le week-end !`       |
| Sunday    | `C'est le week-end !`       |

### 2.2 Data persistence

Stop the container, and start it again. What do you observe? Are your
previous modifications lost?

  

    *\[answer\]*

  

### 2.3 Volume

Use a volume to persist the data so that the data lifecycle and the
container lifecycle are decoupled. Create a volume called “mysql-data”
and mount it to the container. Then, retry the steps in exercises 2.1
and 2.2 to see whether the data are persisted. For more details, see the
instructions for creating a volume in Docker:
<https://docs.docker.com/reference/cli/docker/volume/create/>, and how
to mount a volume to a container:
<https://docs.docker.com/reference/cli/docker/container/run/#volume>.
Please describe the whole process including the commands used and your
observations.

## Clean up

If you use a computer of ESIGELEC, please stop and remove all the
running containers and volumes before leaving the lab session. Thank
you.