---
title: Rendering capabilities
description: Standard Azure Batch capabilities are used to run rendering workloads and apps. Batch includes specific features to support rendering workloads.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
---

# Azure Batch rendering capabilities

Standard Azure Batch capabilities are used to run rendering workloads and applications. Batch also includes specific features to support rendering workloads.

For an overview of Batch concepts, including pools, jobs, and tasks, see [this article](./batch-service-workflow-features.md).

## Batch pools using custom VM images and standard application licensing

As with other workloads and types of application, a custom VM image can be created with the required rendering applications and plug-ins. The custom VM image is placed in the [Azure Compute Gallery](../virtual-machines/shared-image-galleries.md) and [can be used to create Batch Pools](batch-sig-images.md).

The task command line strings will need to reference the applications and paths used when creating the custom VM image.

Most rendering applications will require licenses obtained from a license server. If there's an existing on-premises license server, then both the pool and license server need to be on the same [virtual network](../virtual-network/virtual-networks-overview.md). It is also possible to run a license server on an Azure VM, with the Batch pool and license server VM being on the same virtual network.

## Batch pools using rendering VM images

> [!IMPORTANT]
> The rendering VM images and pay-for-use licensing have been [deprecated and will be retired on February 29, 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). To use Batch for rendering, [a custom VM image and standard application licensing should be used.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### Rendering application installation

An Azure Marketplace rendering VM image can be specified in the pool configuration if only the pre-installed applications need to be used.

There is a Windows image and a CentOS image.  In the [Azure Marketplace](https://azuremarketplace.microsoft.com), the VM images can be found by searching for 'batch rendering'.

The Azure portal and Batch Explorer provide GUI tools to select a rendering VM image when you create a pool.  If using a Batch API, then specify the following property values for [ImageReference](/rest/api/batchservice/pool/add#imagereference) when creating a pool:

| Publisher | Offer | Sku | Version |
|---------|---------|---------|--------|
| batch | rendering-centos73 | rendering | latest |
| batch | rendering-windows2016 | rendering | latest |

Other options are available if additional applications are required on the pool VMs:

* A custom image from the Azure Compute Gallery:
  * Using this option, you can configure your VM with the exact applications and specific versions that you require. For more information, see [Create a pool with the Azure Compute Gallery](batch-sig-images.md). Autodesk and Chaos Group have modified Arnold and V-Ray, respectively, to validate against an Azure Batch licensing service. Make sure you have the versions of these applications with this support, otherwise the pay-per-use licensing won't work. Current versions of Maya or 3ds Max don't require a license server when running headless (in batch/command-line mode). Contact Azure support if you're not sure how to proceed with this option.
* [Application packages](./batch-application-packages.md):
  * Package the application files using one or more ZIP files, upload via the Azure portal, and specify the package in pool configuration. When pool VMs are created, the ZIP files are downloaded and the files extracted.
* Resource files:
  * Application files are uploaded to Azure blob storage, and you specify file references in the [pool start task](/rest/api/batchservice/pool/add#starttask). When pool VMs are created, the resource files are downloaded onto each VM.

### Pay-for-use licensing for pre-installed applications

The applications that will be used and have a licensing fee need to be specified in the pool configuration.

* Specify the `applicationLicenses` property when [creating a pool](/rest/api/batchservice/pool/add#request-body).  The following values can be specified in the array of strings - "vray", "arnold", "3dsmax", "maya".
* When you specify one or more applications, then the cost of those applications is added to the cost of the VMs.  Application prices are listed on the [Azure Batch pricing page](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> If instead you connect to a license server to use the rendering applications, do not specify the `applicationLicenses` property.

You can use the Azure portal or Batch Explorer to select applications and show the application prices.

If an attempt is made to use an application, but the application hasn’t been specified in the `applicationLicenses` property of the pool configuration or does not reach a license server, then the application execution fails with a licensing error and non-zero exit code.

### Environment variables for pre-installed applications

To be able to create the command line for rendering tasks, the installation location of the rendering application executables must be specified.  System environment variables have been created on the Azure Marketplace VM images, which can be used instead of having to specify actual paths.  These environment variables are in addition to the [standard Batch environment variables](./batch-compute-node-environment-variables.md) created for each task.

|Application|Application Executable|Environment Variable|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos Group V-Ray Standalone|vray.exe|VRAY_4.10.03_EXEC|
|Arnold 2020 command line|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## Azure VM families

As with other workloads, rendering application system requirements vary, and performance requirements vary for jobs and projects.  A large variety of VM families are available in Azure depending on your requirements – lowest cost, best price/performance, best performance, and so on.
Some rendering applications, such as Arnold, are CPU-based; others such as V-Ray and Blender Cycles can use CPUs and/or GPUs.
For a description of available VM families and VM sizes, [see VM types and sizes](../virtual-machines/sizes.md).

## Low-priority VMs

As with other workloads, low-priority VMs can be utilized in Batch pools for rendering.  Low-priority VMs perform the same as regular dedicated VMs but utilize surplus Azure capacity and are available for a large discount.  The tradeoff for using low-priority VMs is that those VMs may not be available to be allocated or may be preempted at any time, depending on available capacity. For this reason, low-priority VMs aren't going to be suitable for all rendering jobs. For example, if images take many hours to render then it's likely that having the rendering of those images interrupted and restarted due to VMs being preempted wouldn't be acceptable.

For more information about the characteristics of low-priority VMs and the various ways to configure them using Batch, see [Use low-priority VMs with Batch](./batch-low-pri-vms.md).

## Jobs and tasks

No rendering-specific support is required for jobs and tasks.  The main configuration item is the task command line, which needs to reference the required application.
When the Azure Marketplace VM images are used, then the best practice is to use the environment variables to specify the path and application executable.

## Next steps

* Learn about [using rendering applications with Batch](batch-rendering-applications.md).
* Learn about [Storage and data movement options for rendering asset and output files](batch-rendering-storage-data-movement.md).
