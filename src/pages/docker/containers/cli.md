---
title: Docker CLI Containers
description: Learn about the Cloud Docker for Commerce CLI containers, images used, and useful guidance.
---

# CLI containers

The following CLI containers, most of which are based on a [PHP-CLI version 7 image][], provide `magento-cloud` and `ece-tools` commands to perform file system operations and interact with the application.

Operations include:

- `build`—extends the CLI container to perform operations with writable filesystem, similar to the build phase
- `deploy`—extends the CLI container to use read-only file system, similar to the deploy phase
- `cron`—extends the CLI container to run cron
- `node`—based on node image, used for NPM activities

For example, you can check the state of your project using the _ideal-state_ wizard:

```bash
docker compose run --rm deploy ece-command wizard:ideal-state
```

Sample response:

```terminal
 - Your application does not have the "post_deploy" hook enabled.
The configured state is not ideal
```

All build and deploy processes are defined and configured using ece-tools and the Cloud template.

## CLI container commands

These commands are available in the Cloud Docker for Commerce environment:

| Command       | Target Containers   |  Notes |
| ------------- |  ------------------ |------------------ |
| `cloud-build` | build | Build the application in production mode, configured by the build hook in the `.magento.app.yml` file |
| `cloud-deploy` | deploy | Deploy the application, configured by the deploy hook in the `.magento.app.yml` file |
| `cloud-post-deploy` | deploy | Run post deploy hooks, configured by the post deploy hook in the `.magento.app.yml` file |
| `ece-command` | deploy | Run [ece-tools CLI commands][] |
| `magento-command` | deploy | Run bin/magento commands |
| `mftf-command` | deploy | Run MFTF command for testing |
| `run-cron` | cron | Run cron jobs |

See the [scripts in the Cloud Docker for Commerce GitHub repository][scripts] to understand the processing for each command.

## Build container

- **Container name**: build
- **Docker base image**: [magento/magento-cloud-docker-php][], which is based on the Docker [php][] image

The Build container mimics the behavior of the Adobe Commerce on cloud infrastructure build process so that testing the build and deploy process is as close to testing in production as possible.

You can run build commands manually from the build container to perform individual steps from the build process. For example, you can run the following command to deploy static content:

```bash
docker compose run --rm build magento-command setup:static-content:deploy
```

## Cron container

- **Container name**: cron
- **Docker base image**: [magento/magento-cloud-docker-php][], which is based on the Docker [php][] image

The Cron container runs operations in the background immediately after the Docker environment starts. This container uses the cron configuration defined in the [`crons` property of the `.magento.app.yaml` file](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/app/properties/crons-property.html). This container has no custom configuration.

<InlineAlert variant="info" slots="text"/>

To improve overall performance in the local Docker environment, the Cron container is not present by default. You can use the following command to add the Cron container to the Cloud Docker environment: `./vendor/bin/ece-docker build:compose --mode="developer" --with-cron`

For details on managing cron jobs in the Cloud Docker environment, see [Manage cron jobs](../configure/manage-cron-jobs.md).

## Deploy container

- **Container name**: deploy
- **Docker base image**: [magento/magento-cloud-docker-php][], which is based on the [php][] Docker image

The Deploy container mimics the Adobe Commerce on cloud infrastructure deploy process so that testing the build and deploy process is as close to testing in production as possible.

You can run `build` and `deploy` commands manually from the deploy container. The following example reindexes the Adobe Commerce store:

```bash
docker compose run --rm deploy magento-command index:reindex
```

## Node container

- **Container name**: node
- **Docker base image**: [node][]

The Node container is based on the [official Node Docker image][node]. You can use the container to install NPM dependencies, such as Gulp, or run any Node-based command-line tools.

To add the Node container to the Docker environment, you must specify the Node version to install:

```bash
./vendor/bin/ece-docker build:compose --node <version>
```

[PHP-CLI version 7 image]: https://hub.docker.com/r/magento/magento-cloud-docker-php
[magento/magento-cloud-docker-php]: https://hub.docker.com/r/magento/magento-cloud-docker-php
[scripts]: https://github.com/magento/magento-cloud-docker/tree/develop/images/php/cli/bin
[Cloud Docker scripts]: https://github.com/magento/magento-cloud-docker/tree/develop/images/php/cli/bin
[magento/magento-cloud-docker-php]: https://hub.docker.com/r/magento/magento-cloud-docker-php
[php]: https://hub.docker.com/_/php
[node]: https://hub.docker.com/_/node
[ece-tools CLI commands]: https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/dev-tools/ece-tools/package-overview.html
