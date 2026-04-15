---

title:  "Choose your Environment"
description: Based on your use case, choose how you want to install Spinnaker.
weight: 30
---

In this step, you choose where to install Spinnaker. The recommended path is an installation into a
Kubernetes cluster

The recommended path is a distributed installation onto a Kubernetes cluster,
but all of these methods are supported:

* [Distributed installation](#distributed-installation) on Kubernetes

  Halyard deploys each of Spinnaker's [microservices](/docs/reference/architecture)
  separately. __This is highly recommended for use in production.__

* [Local installations](#local-debian) of Debian packages

  Spinnaker is deployed on a single machine. This is ok for smaller
  Spinnaker deployments, but Spinnaker will be unavailable when it's being
  updated.

* [Local git installations](#local-git) from GitHub

  This is for developers contributing to the Spinnaker project. If you're a
  contributor, you'll probably have two separate installations&mdash;a
  distributed one for using Spinnaker in production, and this local Git one for
  developing Spinnaker contributions.

## Distributed installation

Kubernetes installations are recommended for most organizations and even
for test purposes. Spinnaker is deployed to a namespace in a kubernetes cluster
[microservice](/docs/reference/architecture/) deployed independently.

Spinnaker is deployed to a remote cloud, with each
[microservice](/docs/reference/architecture/) deployed independently. Halyard
creates a smaller, headless Spinnaker to update your Spinnaker and its
microservices, ensuring zero-downtime updates.

1. Make sure [kubectl is installed](https://kubernetes.io/docs/tasks/tools/)
2. Optionally,
   configure [Kubernetes probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)
   for your Spinnaker services in their deployment manifests (in the `~/spinnaker-kustomize/base/*/deployment.yaml`
   files.
3. Adjust the domains. Look for any example.com reference and replace with your DNS domain.
4. Create a file with the spinnaker kubernetes resources and apply it
    1. `kubectl kustomize -o spinnaker.yaml`
    2. `kubectl apply -f spinnaker.yaml`
5. Get the ingress IP address and create DNS entries for your new spinnaker domain to this new entry

REMINDER:  This basic setup defaults with a SIMPLE username/password auth.  It's recommended to change
this from the default or better yet, use an identity provider (saml/oidc/ldap) solution.  The spinnaker
project does integration tests today with Keycloak as a known out of the box solution.

## Local Debian

The __Local Debian__ installation means Spinnaker will be downloaded and run on the
single machine Halyard is currently installed on.

We recommend at least 4 cores and 16GB of RAM.

> **Note**: Local Debian installation requires either Ubuntu 18.04 or higher or Debian 10 or higher.

### Intended use case

The __Local Debian__ installation is intended for smaller deployments of Spinnaker,
and for clouds where the __Distributed__ installation is not yet supported;
however, since all services are on a single machine, there will be downtime when
Halyard updates Spinnaker.

Note that a Halyard [Docker
installation](https://www.spinnaker.io/setup/install/halyard/#docker) cannot be
used as a __Local Debian__ base image because it does not contain the necessary
packages to run Spinnaker.

### Required Halyard invocations

Currently, Halyard defaults to a __Local Debian__ install when first run,
and no changes are required on your behalf. However, if you've edited
Halyard's deployment type and want to revert to a local install, you can run
the following command.

```
hal config deploy edit --type localdebian
```

<span class="end-collapsible-section"></span>

<span class="begin-collapsible-section"></span>

## Local Git

The __Local Git__ installation means Spinnaker will be cloned, built, and run on
the single machine Halyard is run on.

### Intended use case

The __Local Git__ installation is intended for developers who want to contribute
to Spinnaker. It is not intended to be used to manage any production environment.

For a short guide to getting up and running with developing Spinnaker, see the
[developer setup guide](/docs/community/contributing/code/developer-guides/dev-env/getting-set-up).

### Prerequisites

We recommend at least 4 cores and 16GB of RAM.

#### Install local dependencies

Ensure that the following are installed on your system:

##### Ubuntu/Debian

* git: `sudo apt-get install git`
* curl: `sudo apt-get install curl`
* netcat: `sudo apt-get install netcat`
* redis-server: `sudo apt-get install redis-server`
* OpenJDK 11 - JDK (we're building from source, so a JRE is not sufficient)
    ```
    sudo apt-get update
    sudo apt-get install openjdk-11-jdk
    ```
* node (version >=12.16.0, [can be installed via nvm](https://github.com/creationix/nvm#install-script), summarized below)
    ```
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
    # Follow instructions at end of script to add nvm to ~/.bash_rc

    nvm install v12.16.0
    ```
* yarn: `npm install -g yarn` or [guide](https://yarnpkg.com/lang/en/docs/install/)

##### MacOS

* brew (a package manager for MacOS, [can be installed via here](https://brew.sh/), summarized below)
    ```
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
* git: `brew install git`
* curl: `brew install curl`
* netcat: `brew install netcat`
* redis-server:
  * Install: `brew install redis`
  * Start: `brew services start redis`
* OpenJDK 8 - JDK (we're building from source, so a JRE is not sufficient)
    ```
  brew cask install adoptopenjdk/openjdk/adoptopenjdk8
    ```
* node (version >=12.16.0, [can be installed via nvm](https://github.com/creationix/nvm#install-script), summarized below)
    ```
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
    # Follow instructions at end of script to add nvm to ~/.bash_rc

    nvm install v12.16.0
    ```
* yarn: `npm install -g yarn` or [guide](https://yarnpkg.com/lang/en/docs/install/)

#### Fork all Spinnaker repos

Fork all of the microservices listed here: [Spinnaker Microservices](https://www.spinnaker.io/reference/architecture/#spinnaker-microservices) on github ([guide](https://guides.github.com/activities/forking/#fork)).

#### Setup SSH keys

Follow these guides to setup ssh access to your github.com account from your local machine:

* [Generating a new ssh key and adding it to your ssh agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
* [Adding a new ssh key to your Github account](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

### Required Halyard invocations

Halyard defaults to a __Local Debian__ install when first run. If you will be
contributing code to the Spinnaker project, you can change your deployment type
to __Local Git__ type and set up your development environment with the latest
code.

```
hal config deploy edit --type localgit --git-origin-user=<YOUR_GITHUB_USERNAME>

hal config version edit --version branch:upstream/master
```

*NOTE: Be sure to use the same username here that you forked the Spinnaker repositories to*

<span class="end-collapsible-section"></span>

## Further reading

* [Spinnaker Architecture](/docs/reference/architecture/) for a better understanding
  of the Distributed installation.

## Next steps

Now that your deployment environment is set up, you need to provide Spinnaker
with a [Persistent Storage](/docs/setup/install/storage/) source.
