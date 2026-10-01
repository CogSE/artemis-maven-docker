# artemis-maven-docker

Docker image for building and testing Java programming exercises in Artemis.

This also includes git and replaces https://github.com/ls1intum/artemis-maven-git-docker.

The image is based on `maven:3.9.16-eclipse-temurin-25-noble` and contains:

- Java 25 (Eclipse Temurin) on Ubuntu 24.04
- Maven 3.9.16
- Gradle 9.0.0, installed through the Gradle wrapper
- git and gnupg
- the dependencies of an Ares 2 exercise, already downloaded for Maven and for Gradle

### Pre-loaded dependencies

During the image build, the exercise in `artemis-java-template` is built once with Maven and once with Gradle, and is removed from the image afterwards. This fills the local Maven repository and the Gradle cache, so that exercise builds do not have to download everything again.

The template uses Ares 2.1.5 with AspectJ 1.9.25.1, and SpotBugs, Checkstyle and PMD for static code analysis.

The Maven build runs the tests of the template. The Gradle build compiles them but does not run them. The security policy of the template (`test/de/tum/in/ase/SecurityPolicy.yaml`) is declared for Maven; an exercise that is built with Gradle needs a policy that uses `JAVA_USING_GRADLE_ARCHUNIT_AND_ASPECTJ`.

### Use the image

	docker pull ghcr.io/cogse/artemis-maven-docker:latest

The image is built for `linux/amd64` only.

### Build and run locally

	docker build --no-cache -t artemis-maven-docker .

	docker run -itd --name artemis-maven-docker artemis-maven-docker /bin/bash

	docker exec -it artemis-maven-docker /bin/bash

	java -version

	mvn -version

	git --version

### Publish to the GitHub Container Registry

The workflow `.github/workflows/build-and-push.yml` builds the image and pushes it to `ghcr.io/cogse/artemis-maven-docker`:

- a push to `main` publishes the tag `latest`
- a pushed git tag publishes an image tag with the same name
- a pull request builds the image without publishing it

The workflow logs in with the `GITHUB_TOKEN` of the run, so it needs no additional secrets.

#### Publish manually

	docker login ghcr.io

	docker buildx build --no-cache -t ghcr.io/cogse/artemis-maven-docker:<tagname> . --push --platform=linux/amd64
