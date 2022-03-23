# This example uses python ARG below
# uncomment one BASE_IMAGE as needed
ARG BASE_IMAGE
ARG BASE_IMAGE_VERSION
ARG BASE_IMAGE_NAME=${BASE_IMAGE}:${BASE_IMAGE_VERSION}
#ARG BASE_IMAGE=php:7.4-cli
#ARG BASE_IMAGE=php:7.4-apache
#ARG BASE_IMAGE=golang:1.16
#ARG BASE_IMAGE=ruby:2.6-slim
#ARG BASE_IMAGE=node:16-slim
#ARG BASE_IMAGE=node:lts-slim
#ARG BASE_IMAGE=maven:3.8-jdk-8-slim
#ARG BASE_IMAGE=ubuntu:xenial

# Jfrog is used by default in REPO_LOCATION
# Solving dockerhub rate limiting
# and improving security scanning
ARG REPO_LOCATION
FROM ${REPO_LOCATION}${BASE_IMAGE_NAME}

# Update the base image dependencies using a generic command for most OS.
RUN  (apt-get update && apt-get upgrade -y && apt-get clean)  \
  || (yum update -y && yum clean all) \
  || (apk update && apk -U upgrade && apk cache clean) \
  || echo "No valid package manager found."

# Install git, purge some packages and clean up.
RUN apt-get update \
  && apt-get install -y git \
  && apt-get purge -y sqlite3 libsqlite3-0 \
  && apt-get clean \
  && apt-get -y autoremove \
  && rm -rf /var/lib/apt/lists/*

# The WORKDIR for adding code and to run tests in pipeline.yaml
WORKDIR /usr/src/app

# To improve cache, intall requirements.txt first
COPY app/requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application
COPY app /usr/src/app/

# Copy or change other files
COPY version /usr/src/app/version
RUN date -u +%y.%m.%d.%H.%M.%S > /usr/src/app/build


# test_key also passed as argument while building docker image
ARG test_key
RUN echo $test_key && [ $test_key = 'ThisIsaTestKey' ]

# Environment variable passed in build_args
ARG EXAMPLE_ARG
RUN echo $EXAMPLE_ARG && [ $EXAMPLE_ARG = 'PASSED_TO_CONTAINER_BUILD' ]

# Open service port and define excution on docker run
EXPOSE 8080
CMD [ "python", "hello.py" ]
