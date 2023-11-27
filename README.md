# Azure_SDK_Sample2_CreateStorageAccount

**NOTE:**
For general info about Azure SDK for .NET navigate to the following github repo: https://github.com/Azure/azure-sdk-for-net

## 0. Prerequisites

Install Azure CLI: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli

For upgrading Azure CLI run the following command:

```
az upgrade
```

To check the current Azure CLI version run the command:

```
az --version
```

To loggin in Azure run the command:

```
az login
```

![image](https://github.com/luiscoco/Azure_SDK_Sample2_CreateStorageAccount/assets/32194879/493bc6a4-5e11-4069-9cba-9cad96e6f7d1)


Instead of constantly typing a location as "**euwest**" or a resource group name as "**Luis-RG**" for the commands I am running, I can set these as defaults in my session using the following command:

```
az configure --defaults location=euwest group=luis-RG
```

## 1. We include the Azure SDK for .NET libraries references

```csharp
﻿using System;
using System.Threading.Tasks;
using Azure.Identity;
using Azure;
using Azure.Core;
using Azure.ResourceManager;
using Azure.ResourceManager.Resources;
using Azure.ResourceManager.Storage;
using Azure.ResourceManager.Storage.Models;
```

## 2. Get the default Azure Subscription

```csharp
ArmClient armClient = new ArmClient(new DefaultAzureCredential());
SubscriptionResource subscription = await armClient.GetDefaultSubscriptionAsync();
```

## 3. Create or update the Azure ResourceGroup 

```csharp
string rgName = "myRgNameLUISCOCO";
AzureLocation location = AzureLocation.WestEurope;
ArmOperation<ResourceGroupResource> operation = await subscription.GetResourceGroups().CreateOrUpdateAsync(WaitUntil.Completed, rgName, new ResourceGroupData(location));
ResourceGroupResource resourceGroup = operation.Value;
Console.WriteLine(resourceGroup.Data.Name);
```

## 4. Create the Azure Storage

```csharp
//we need to define the StorageAccountCreateParameters
StorageSku sku = new StorageSku(StorageSkuName.StandardGrs);
StorageKind kind = StorageKind.Storage;
StorageAccountCreateOrUpdateContent parameters = new StorageAccountCreateOrUpdateContent(sku, kind, location);

//now we can create a storage account with defined account name and parameters
StorageAccountCollection accountCollection = resourceGroup.GetStorageAccounts();
string accountName = "myaccountluiscoco";
ArmOperation<StorageAccountResource> accountCreateOperation = await accountCollection.CreateOrUpdateAsync(WaitUntil.Completed, accountName, parameters);
StorageAccountResource storageAccount = accountCreateOperation.Value;
Console.WriteLine(storageAccount.Data.Name);
```

## 5. Source code.


```csharp
﻿using System;
using System.Threading.Tasks;
using Azure.Identity;
using Azure;
using Azure.Core;
using Azure.ResourceManager;
using Azure.ResourceManager.Resources;
using Azure.ResourceManager.Storage;
using Azure.ResourceManager.Storage.Models;

ArmClient armClient = new ArmClient(new DefaultAzureCredential());
SubscriptionResource subscription = await armClient.GetDefaultSubscriptionAsync();

string rgName = "myRgNameLUISCOCO";
AzureLocation location = AzureLocation.WestEurope;
ArmOperation<ResourceGroupResource> operation = await subscription.GetResourceGroups().CreateOrUpdateAsync(WaitUntil.Completed, rgName, new ResourceGroupData(location));
ResourceGroupResource resourceGroup = operation.Value;
Console.WriteLine(resourceGroup.Data.Name);

//first we need to define the StorageAccountCreateParameters
StorageSku sku = new StorageSku(StorageSkuName.StandardGrs);
StorageKind kind = StorageKind.Storage;
StorageAccountCreateOrUpdateContent parameters = new StorageAccountCreateOrUpdateContent(sku, kind, location);
//now we can create a storage account with defined account name and parameters
StorageAccountCollection accountCollection = resourceGroup.GetStorageAccounts();
string accountName = "myaccountluiscoco";
ArmOperation<StorageAccountResource> accountCreateOperation = await accountCollection.CreateOrUpdateAsync(WaitUntil.Completed, accountName, parameters);
StorageAccountResource storageAccount = accountCreateOperation.Value;
Console.WriteLine(storageAccount.Data.Name);
```
