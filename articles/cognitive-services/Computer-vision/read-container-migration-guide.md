---
title: Migrating from the v2 read container to v3
titleSuffix: Azure Cognitive Services
description: Learn how to migrate to the v3 Read container  
services: cognitive-services 
author: aahill
manager: nitinme
ms.service: cognitive-services 
ms.subservice: computer-vision 
ms.topic: overview
ms.date: 09/21/2020
ms.author: aahi
---

# Migrate to the Read v3.x container

If you're using version 2 of the Computer Vision Read container, Use this article to learn about upgrading your application to use version 3.x of the container. 



## Configuration changes

* `ReadEngineConfig:ResultExpirationPeriod` is no longer supported. The Read container has a built Cron job that removes the results and metadata associated with a request after 48 hours.
* `Cache:Redis:Configuration` is no longer supported. The Cache is not used in the v3.x containers, so you do not need to set it.

## API changes

The Read v3.x containers use version 3 of the Computer Vision API and have the following endpoints:

#### [Version 3.1-preview](#tab/version-3-1)

* `/vision/v3.1-preview.2/read/analyzeResults/{operationId}`
* `/vision/v3.1-preview.2/read/analyze`
* `/vision/v3.1-preview.2/read/syncAnalyze`

#### [Version 3.0-preview](#tab/version-3)

* `/vision/v3.0/read/analyzeResults/{operationId}`
* `/vision/v3.0/read/analyze`
* `/vision/v3.0/read/syncAnalyze`

---

See the [Computer Vision v3 REST API migration guide](https://docs.microsoft.com/azure/cognitive-services/computer-vision/upgrade-api-versions) for detailed information on updating your applications to use version 3 of cloud-based Read API. This information applies to the container as well. Note that sync operations are only supported in containers.

## Memory requirements

The requirements and recommendations are based on benchmarks with a single request per second, using an 8-MB image of a scanned business letter that contains 29 lines and a total of 803 characters. The following table describes the minimum and recommended allocation of resources for each Read container.

|Container  |Minimum | Recommended  |
|---------|---------|------|
|Read 3.0-preview     | 8 cores, 16-GB memory         | 8 cores, 24-GB memory
|Read 3.1-preview | 8 cores, 16-GB memory         | 8 cores, 24-GB memory

Each core must be at least 2.6 gigahertz (GHz) or faster.

Core and memory correspond to the `--cpus` and `--memory` settings, which are used as part of the docker run command.

## Storage implementations

>[!NOTE]
> MongoDB is no longer supported in 3.x versions of the container. Instead, the containers support Azure Storage and offline file systems.

| Implementation |	Required runtime argument(s) |
|---------|---------|
|File level (default)	| No runtime arguments required. `/share` directory will be used. |
|Azure Blob	| `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## Queue implementations

In v3.x of the container, RabbitMQ is currently not supported. The supported backing implementations are:

| Implementation | Runtime Argument(s) | Intended use |
|---------|---------|-------|
| In Memory (default) | No runtime arguments required. | Development and testing |
| Azure Queues | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Production |
| RabbitMQ	| Unavailable | Production |

For added redundancy the Read v3.x container uses a visibility timer to ensure requests can be successfully processed in the event of a crash, when running in a multi-container set-up. 

Set the timer with `Queue:Azure:QueueVisibilityTimeoutInMilliseconds`, which sets the time for a message to be invisible when another worker is processing it. To avoid pages from being redundantly processed, we recommend setting the timeout period to 120 seconds. The default value is 30 seconds.

| Default value | Recommended value |
|---------|---------|
| 30000 |	120000 |


## Next steps

* Review [Configure containers](computer-vision-resource-container-config.md) for configuration settings
* Review [Computer Vision overview](overview.md) to learn more about recognizing printed and handwritten text
* Refer to the [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) for details about the methods supported by the container.
* Refer to [Frequently asked questions (FAQ)](FAQ.md) to resolve issues related to Computer Vision functionality.
* Use more [Cognitive Services Containers](../cognitive-services-container-support.md)
