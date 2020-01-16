# Example Voting App with Openshift Go

A simple distributed application running across multiple containers.

![Architecture diagram](architecture.png)

* A front-end web app in [Python](/vote) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) queue which collects new votes
* A [.NET Core 2.1](/worker) worker which consumes votes and stores them in…
* A [Postgres] database backed
* A [Node.js](/result) webapp which shows the results of the voting in real time


## Note

The voting application only accepts one vote per client. It does not register votes if a vote has already been submitted from a client.

## Prerequisites

* odo is installed.
* oc client is installed.
* You have a running OpenShift Container Platform cluster. Developers can use CodeReady Containers (CRC) to deploy a local OpenShift Container Platform cluster quickly.
* Service Catalog is enabled.

The service catalog is deprecated in OpenShift Container Platform 4. Equivalent and better functionality is present in the Operator Framework and Operator Lifecycle Manager (OLM).

Nevertheless, the Operator Framework is not yet supported with odo, so we have to enable/install the service catalog to be able to add odo services like postgresql and redis for this example.

### Installing the service catalog

https://docs.openshift.com/container-platform/4.2/applications/service_brokers/installing-service-catalog.html

### Installing the Template Service Broker

https://docs.openshift.com/container-platform/4.2/applications/service_brokers/installing-template-service-broker.html

## Usage

[Official doc](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_developer_cli/understanding-odo.html)

### login to your openshift cluster

```
odo login -t $token
```

### Create a new project

```
odo project create vote-odo
```

### Deploy the needed services

1. List the available services

```
odo catalog list services
```

Create the postgresql service

```
odo service create postgresql-ephemeral postgresql \
  --plan default \
  -p DATABASE_NAME=postgresql \
  -p MEMORY_LIMIT=512Mi \
  -p NAMESPACE=openshift \
  -p POSTGRESQL_DATABASE=vote \
  -p POSTGRESQL_PASSWORD=1234 \
  -p POSTGRESQL_USER=vote \
  -p POSTGRESQL_VERSION=10
```

Create the redis service

```
odo service create redis-ephemeral redis \
--plan default \
-p DATABASE_NAME=redis \
-p MEMORY_LIMIT=512Mi \
-p NAMESPACE=openshift \
-p REDIS_PASSWORD=1234 \
-p REDIS_VERSION=3.2
```

### Create the app component configs

```
odo component create nodejs:10 --context ./result
odo component create python:2.7 --context ./vote
odo component create dotnet:2.1 --context ./worker
```

### Add environment variables to component configs

Redis password for vote

```
pushd vote
odo config set --env REDIS_HOST=redis --REDIS_PASSWORD=1234
odo config view
popd
```

Postgres user, password and database for result

```
pushd result 
odo config set --env POSTGRESQL_HOST=postgresql --env POSTGRESQL_USERNAME=vote --env POSTGRESQL_PASSWORD=1234 --env POSTGRESQL_DATABASE=vote
odo config view
popd
```

Postgres user, password and database for worker

```
pushd worker 
odo config set --env POSTGRESQL_HOST=postgresql --env POSTGRESQL_USERNAME=vote --env POSTGRESQL_PASSWORD=1234 --env POSTGRESQL_DATABASE=vote --env REDIS_HOST=redis --env REDIS_PASSWORD=1234
odo config view
popd
```

## Expose frontends services

```
pushd vote 
odo url create
popd
pushd result 
odo url create
popd
```

## Pushing the configs to the cluster

```
odo push worker --context ./worker
odo push vote --context ./vote
odo push result --context ./result
```

## tips & tricks

If you have troubles removing a project (remining in Terminating state),
delete the finalyzer lines in

```
oc edit serviceinstances -n vote-odo
```

Delete the whole project

```

```


