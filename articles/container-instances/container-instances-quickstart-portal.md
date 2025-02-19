---
title: Quickstart - Deploy Docker container to container instance - Portal
description: In this quickstart, you use the Azure portal to quickly deploy a containerized web app that runs in an isolated Azure container instance
ms.date: 08/24/2020
ms.topic: quickstart
ms.custom: mvc, devx-track-js, mode-portal
---

# Quickstart: Deploy a container instance in Azure using the Azure portal

Use Azure Container Instances to run serverless Docker containers in Azure with simplicity and speed. Deploy an application to a container instance on-demand when you don't need a full container orchestration platform like Azure Kubernetes Service.

In this quickstart, you use the Azure portal to deploy an isolated Docker container and make its application available with a fully qualified domain name (FQDN). After configuring a few settings and deploying the container, you can browse to the running application:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App deployed using Azure Container Instances viewed in browser":::

## Sign in to Azure

Sign in to the Azure portal at https://portal.azure.com.

If you don't have an Azure subscription, create a [free account][azure-free-account] before you begin.

## Create a container instance

Select the **Create a resource** > **Containers** > **Container Instances**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Begin creating a new container instance in the Azure portal":::

On the **Basics** page, enter the following values in the **Resource group**, **Container name**, and **Container image** text boxes. Leave the other values at their defaults, then select **OK**.

* Resource group: **Create new** > `myresourcegroup`
* Container name: `mycontainer`
* Image source: **Quickstart images**
* Container image: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Configuring basic settings for a new container instance in the Azure portal":::

For this quickstart, you use default settings to deploy the public Microsoft `aci-helloworld` image. This sample Linux image packages a small web app written in Node.js that serves a static HTML page. You can also bring your own container images stored in Azure Container Registry, Docker Hub, or other registries.

On the **Networking** page, specify a **DNS name label** for your container. The name must be unique within the Azure region where you create the container instance. Your container will be publicly reachable at `<dns-name-label>.<region>.azurecontainer.io`. If you receive a "DNS name label not available" error message, try a different DNS name label.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Configuring network settings for a new container instance in the Azure portal":::

Leave the other settings at their defaults, then select **Review + create**.

When the validation completes, you're shown a summary of the container's settings. Select **Create** to submit your container deployment request.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Settings summary for a new container instance in the Azure portal":::

When deployment starts, a notification appears to indicate the deployment is in progress. Another notification is displayed when the container group has been deployed.

Open the overview for the container group by navigating to **Resource Groups** > **myresourcegroup** > **mycontainer**. Take note of the **FQDN** (the fully qualified domain name) of the container instance, as well its **Status**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Container group overview in the Azure portal":::

Once its **Status** is *Running*, navigate to the container's FQDN in your browser.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App deployed using Azure Container Instances viewed in browser":::

Congratulations! By configuring just a few settings, you've deployed a publicly accessible application in Azure Container Instances.

## View container logs

Viewing the logs for a container instance is helpful when troubleshooting issues with your container or the application it runs.

To view the container's logs, under **Settings**, select **Containers**, then **Logs**. You should see the HTTP GET request generated when you viewed the application in your browser.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Container logs in the Azure portal":::


## Clean up resources

When you're done with the container, select **Overview** for the *mycontainer* container instance, then select **Delete**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Deleting the container instance in the Azure portal]":::

Select **Yes** when the confirmation dialog appears.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Delete confirmation of a container instance in the Azure portal]":::

## Next steps

In this quickstart, you created an Azure container instance from a public Microsoft image. If you'd like to build a container image and deploy it from a private Azure container registry, continue to the Azure Container Instances tutorial.

> [!div class="nextstepaction"]
> [Azure Container Instances tutorial](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
