# Chapter 5: Automated Acceptance Testing

All the instructions assumes that:
* You have Docker installed and configured
* You have Java JDK 8+ installed
* You have a running Jenkins instance
* Your Jenkins agents (executors) must have JDK 8+ and Docker installed

## Code Samples

### Code Sample 1: Using Docker registry

The [sample1](sample1) includes a project to build Docker image which contains the Python interpreter. You can build it with the following command.

    $ docker build -t ubuntu_with_python .

Let's tag the image to use Docker Hub registry (if you don't have an account, you can create it [here](https://hub.docker.com/signup)).

    $ docker tag ubuntu_with_python <username>/ubuntu_with_python:1

Log into Docker Hub.

    $ docker login --username <username> --password <password>

Push image into the registry.

    $ docker push <username>/ubuntu_with_python:1

To check that the image was pushed, we can remove it from the Docker Daemon and pull from the registry.

    $ docker rmi ubuntu_with_python <username>/ubuntu_with_python:1
    $ docker pull <username>/ubuntu_with_python:1

### Code Sample 2: Dockerizing Spring Boot Application

 The [sample2](sample2) includes a Spring Boot Application and the related Dockerfile. You can build it with the following commands.

	$ ./gradle build
	$ docker build -t calculator .

To test if the Docker image is correct we can run it and use `curl` to call our Spring Boot Application.

	$ docker run -p 8080:8080 --name calculator calculator
	$ curl localhost:8080/sum?a=1\&b=2
    3

To include Gradle build and Docker build stages in the Jenkins pipeline, add the following part to Jenkinsfile.

```
stage("Package") {
     steps {
          sh "./gradlew build"
     }
}

stage("Docker build") {
     steps {
          sh "docker build -t <username>/calculator ."
     }
}
```

Note that you need to change `<username>` to your Docker Hub ID. Note also that your Jenkins executor needs to have Docker client configured.

As the last part of this sample, you can add a stage which logs into Docker Hub and pushes Docker image.

```
stage("Docker login") {
     steps {
          withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-hub-credentials',
                   usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
               sh "docker login --username $USERNAME --password $PASSWORD"
          }
     }
}

stage("Docker push") {
     steps {
          sh "docker push <username>/calculator"
     }
}
```

Note that before executing the build you have to set up the credentials `docker-hub-credentials` in Jenkins.

### Code Sample 3: Acceptance testing stage

To run the simplest Acceptance testing stage on the [sample2](sample2) project, create the following `acceptance_test.sh` file.

```bash
#!/bin/bash
test $(curl localhost:8765/sum?a=1\&b=2) -eq 3
```

Then, add the following stages to your Jenkinsfile:
```
stage("Deploy to staging") {
     steps {
          sh "docker run -d --rm -p 8765:8080 --name calculator <username>/calculator"
     }
}

stage("Acceptance test") {
     steps {
          sleep 60
          sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
     }
}
```

Add also the clean up stage to avoid keeping the test containers alive.

```
post {
     always {
          sh "docker stop calculator"
     }
}
```

### Code Sample 4: Writing Cucumber acceptance test

The [sample4](sample4) includes a Spring Boot Application with the Acceptance Test which uses the Cucumber framework. Please check the following files:
* `src/test/resources/feature/calculator.feature`: Acceptance Testing Scenario (acceptance criteria)
* `src/test/java/acceptance/StepDefinitions.java`: Step Definitions
* `src/test/java/acceptance/AcceptanceTest.java`: Cucumber JUnit Runner

You can run the acceptance test with the following command:

    $ ./gradlew acceptanceTest -Dcalculator.url=http://localhost:8765

Change `calculator.url` to the address of your running Calculator service.

To use this acceptance test in your Jenkins, use the following stage:
```
stage("Acceptance test") {
     steps {
          sleep 60
          sh "./gradlew acceptanceTest -Dcalculator.url=http://localhost:8765"
     }
}
```

