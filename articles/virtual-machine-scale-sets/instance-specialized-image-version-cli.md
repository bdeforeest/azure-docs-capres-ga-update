---
title: Create a scale set from a specialized image version using the Azure CLI
description: Create a scale set using a specialized image version in an Azure Compute Gallery using the Azure CLI.
author: sandeepraichura
ms.author: saraic
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.reviewer: cynthn 
ms.custom: devx-track-azurecli
---

# Create a scale set using a specialized image version with the Azure CLI

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Uniform scale sets

Create a scale set from a [specialized image version](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) stored in an Azure Compute Gallery. If you want to create a scale set using a generalized image version, see [Create a scale set from a generalized image](instance-generalized-image-version-cli.md).

If you choose to install and use the CLI locally, this tutorial requires that you are running the Azure CLI version 2.4.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI]( /cli/azure/install-azure-cli).

Replace resource names as needed in this example. 

List the image definitions in a gallery using [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) to see the name and ID of the definitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Create a scale set using [`az vmss create`](/cli/azure/vmss#az-vmss-create) using the `--specialized` parameter to indicate the image is a specialized image.

Use the image definition ID for `--image` to create the scale set instances from the latest version of the image that is available. You can also create the scale set instances from a specific version by supplying the image version ID for `--image`. Be aware that using a specific image version means automation could fail if that specific image version isn't available because it was deleted or removed from the region. We recommend using the image definition ID for creating your new VM, unless a specific image version is required.

In this example, we are creating instances from the latest version of the *myImageDefinition* image.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## Next steps
[Azure Image Builder (preview)](../virtual-machines/image-builder-overview.md) can help automate image version creation, you can even use it to update and [create a new image version from an existing image version](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

You can also create Azure Compute Gallery resource using templates. There are several Azure Quickstart Templates available: 

- [Create an Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/)
- [Create an Image Definition in an Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Create an Image Version in an Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-version-create/)