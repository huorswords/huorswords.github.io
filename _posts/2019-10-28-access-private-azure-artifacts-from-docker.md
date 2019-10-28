---
layout: post
title:  "Azure Artifacts on Docker | Access Nuget private feed"
date:   2019-10-28 11:00:00
categories: azure pipeline devops docker artifacts nuget
---
# >_

## Scenario

Let's supose that your project is containerized using Docker, and you should include a new nuget package that is stored on your private Azure Artifacts server feed.

How do you access it from your local environment or your build Azure Pipeline?

## Setup

I am assuming that you have already configured your [Azure Artifacts](https://docs.microsoft.com/en-us/azure/devops/pipelines/artifacts/artifacts-overview?view=azure-devops) service and include some packages on it.

Also, I'm assuming that you are using, in this case, [docker-compose](https://docs.docker.com/compose/) to build your dockerfile containerized projects.

## Problem

The main problem of using a private feed, regardless it is Azure Artifacts private feed or any other private nuget service, is that on the docker execution environment the feed access credentials are not available if you do not explicitly include it passing it on **Dockerfile** argument or environment variable - or hardcoding it... but no one does it.

Additionally, when you access to any Azure Artifacts private feed, you need to get the Artifact Credentials provider in order to be able to use a **PAT** (*Personal Access Token*) instead of **plain credentials** (*Username / Password* pair)

## Dockerfile

First, let's configure our **Dockerfile**.

```dockerfile
FROM microsoft/dotnet:2.2-sdk AS dotnet-builder

ARG nuget_pat

# ... other arguments and pre-build steps

# Set environment variables
ENV NUGET_CREDENTIALPROVIDER_SESSIONTOKENCACHE_ENABLED true
ENV VSS_NUGET_EXTERNAL_FEED_ENDPOINTS '{"endpointCredentials":[{"endpoint":"https://company.pkgs.visualstudio.com/_packaging/FeedName/nuget/v3/index.json","username":"NoRealUserNameAsIsNotRequired","password":"'${nuget_pat}'"}]}'

# Get and install the Artifact Credential provider
RUN wget -O - https://raw.githubusercontent.com/Microsoft/artifacts-credprovider/master/helpers/installcredprovider.sh  | bash

# Restore your nugets from nuget.org and your private feed.
RUN dotnet restore . -s "https://company.pkgs.visualstudio.com/_packaging/FeedName/nuget/v3/index.json" -s "https://api.nuget.org/v3/index.json"

```

As you can see, you should setup your **Dockerfile** with argument (i.e. named `nuget_pat`) to allow pass the PAT into your Dockerfile execution.

Then, you should "activate" the **Session Token Cache** setting the `NUGET_CREDENTIALPROVIDER_SESSIONTOKENCACHE_ENABLED` variable to `true`. And, after that, prepare the environment variable that stores the available nuget external endpoints, through a JSON object in the `VSS_NUGET_EXTERNAL_FEED_ENDPOINTS`. By default, only the nuget.org one is automatically available.

Now, only when you should access to an Azure Artifact feed, you need to **download** the corresponding **credentials provider**, in order to allow the use of *PAT* instead of pair *Username - Secret* combination. If not, feel free to avoid the ***Get and install the Artifact Credential provider*** line.

And, at last, just call the `dotnet restore` instruction passing the required sources using `-s` modifier, specifing the whole URL included on the json configuration environment variable.

## docker-compose

Let's prepare the **docker-compose.yml** file to pass the `nuget_pat` argument...

```yml
version: "3.6"
services:
  mycustom-api:
    image: ${REGISTRY:-local}/mycustom.api:${TAG:-latest}
    build:
      context: .
      dockerfile: ${API_PATH}Dockerfile
      args:
        nuget_pat: ${NUGET_PAT}
```

The `${NUGET_PATH}` value, in this case, is an environment variable that will be passed to the docker-compose execution externally.

## azure-pipeline

Just be sure that you include the `NUGET_PAT` variable in your environment execution for the given step responsible to perform the docker image build.

```yml
- bash: ./build.sh
  env:
    # ...
    NUGET_PAT: $(NUGET_PAT)
  displayName: 'Build API image'
```

Store it as secret, in order to not expose the content to any non-authorized user that could have access to your Azure DevOps Deployment definitions.

Like this:

![A](/assets/access-private-azure-artifacts-from-docker-variables.jpg)

## Acknoledgement

This solution is just an implementation suggested on [this stackoverflow question](https://stackoverflow.com/questions/57549771/azure-devops-private-nuget-repo-unauthorized-when-building-with-docker). Thank you OP and Stack Overflow users that answered de question. All the kudos are yours. :)
