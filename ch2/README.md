# Chapter 2: Introducing Docker

All the instructions assumes you have Docker (Community Edition) installed and configured on your machine.

## Code Samples

### Code Sample 1: Dockerfile

The [sample1](sample1) includes a project to build Docker image which contains the Python interpreter. You can build it with the following command.

    $ docker build -t ubuntu_with_python .

### Code Sample 2: Complete Docker application

The [sample2](sample2) includes a project to build Hello World Python Docker image. You can build and run it with the following commands.

    $ docker build -t hello_world_python .
    $ docker run hello_world_python
    Hello World from Python!

### Code Sample 3: Environment variables

The [sample3](sample3) project presents the usage of environment variables with Docker image. You can build and run it with the following commands.

    $ docker build -t hello_world_python_name .
    $ docker run -e NAME=Rafal hello_world_python_name
    Hello World from Rafal ! 

## Exercise solutions

### Exercise 1: Run CouchDB Docker container



### Exercise 2: Create Docker image with REST service

The [exercise2](exercise2) directory contains the source code for the Docker image with a REST web service. You can build and run it with the following commands.
