Example Voting App with Openshift Go
=========

A simple distributed application running across multiple containers.

![Architecture diagram](architecture.png)

* A front-end web app in [Python](/vote) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) queue which collects new votes
* A [.NET Core 2.1](/worker) worker which consumes votes and stores them in…
* A [Postgres] database backed
* A [Node.js](/result) webapp which shows the results of the voting in real time


Note
----

The voting application only accepts one vote per client. It does not register votes if a vote has already been submitted from a client.
