---
title:  Application Lifecycle Management (ALM) APIs 
description: ALM APIs provide simple APIs to manage deployment of your SharePoint Framework solutions and add-ins across your tenant.
ms.date: 11/16/2019
ms.prod: sharepoint
ms.assetid: fdf7ecb2-8851-425b-b058-3285fba77b68
localization_priority: Priority
---

# Application Lifecycle Management (ALM) APIs

ALM APIs provide simple APIs to manage deployment of your SharePoint Framework solutions and add-ins across your tenant. ALM APIs support the following capabilities: 

- Add SharePoint Framework solution or SharePoint Add-in to tenant or site collection app catalog.
- Remove SharePoint Framework solution or SharePoint Add-in from tenant or site collection app catalog.
- Enable SharePoint Framework solution or SharePoint Add-in to be available for installation in tenant or site collection app catalog.
- Disable SharePoint Framework solution or SharePoint Add-in not to be available for installation in tenant or site collection app catalog.
- Install SharePoint Framework solution or SharePoint Add-in from tenant or site collection app catalog to a site.
- Upgrade SharePoint Framework solution or SharePoint Add-in to a site, which has a newer version available in the tenant or site collection app catalog.
- Uninstall SharePoint Framework solution or SharePoint Add-in from the site.
- List all and get details about SharePoint Framework solutions or SharePoint Add-ins in the tenant or site collection app catalog.

ALM APIs can be used to perform exactly the same operations that are available from a UI perspective. When these APIs are used, all typical actions are performed. Following are some of the characteristics of ALM APIs:

- `Install` and `UnInstall` events are being fired for provider-hosted add-ins when corresponding operations occur.
- ALM APIs support app-only-based operations for SharePoint Framework solutions only. 

ALM APIs are natively provided by using REST APIs, but there are additional CSOM extensions, PowerShell cmdlets, and the cross-platform Office 365 CLI available through SharePoint PnP Community channels.

> [!IMPORTANT]
> Tenant-scoped permissions which require [tenant administrative approval](https://docs.microsoft.com/en-us/sharepoint/dev/solution-guidance/how-to-provide-add-in-app-only-tenant-administrative-permissions-in-sharepoint-online) are not supported with the ALM APIs.

## REST API

> [!TIP] 
> ALM APIs are also supported for the [site collection app catalog](../general-development/site-collection-app-catalog.md). URLs for the site collection app catalog operations are exactly the same, but you can change the `tenantappcatalog` to `sitecollectionappcatalog`. Notice also that you will need to enable the site collection app catalog in your site collection or you will get an exception when trying to use these APIs.

### Add solution package to the app catalog

This API is designed to be executed in the context of the app catalog site.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/Add(overwrite=true, url='test.txt')
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |
| Content-Type            | `application/json`                  |
| X-RequestDigest         | `{form digest}`                     |
| binaryStringRequestBody | `true`                              |

#### Request body

Byte array of the file

### Deploy solution packages in the app catalog

This enables the solution to be available to install to specific sites. This API is designed to be executed in the context of the app catalog site.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/AvailableApps/GetById('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')/Deploy
```

#### Request headers

|     Header      |                       Value                       |
| :-------------- | :------------------------------------------------ |
| Authorization   | `Bearer {token}`                                  |
| Accept          | `application/json;odata=nometadata`               |
| Content-Type    | `application/json;odata=nometadata;charset=utf-8` |
| X-RequestDigest | `{form digest}`                                   |

#### Request body

```json
{"skipFeatureDeployment":true}
```

> [!NOTE]
> This operation is required to be completed after Add, before you can install packages to specific sites. 

> [!NOTE]
> It is currently not supported to deploy several packages in parallel - make sure to serialize your deployment operations to avoid deployment errors.

### Retract solution packages in the app catalog

This retracts the solution to be available from the sites. This API is designed to be executed in the context of the app catalog site.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/AvailableApps/GetById('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')/Retract
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |
| X-RequestDigest         | `{form digest}`                     |

> [!NOTE]
> If you run this operation after you have installed solutions to the site, they stop working because the solution is disabled from the tenant level.

### Remove solution packages from the app catalog

This API is designed to be executed in the context of the app catalog site.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/AvailableApps/GetById('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')/Remove
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |


> [!NOTE]
> If the Retract operation is not performed before the Remove operation, the solution is automatically retracted.

### List available packages from the app catalog

Use this REST API for getting a list of available SharePoint Framework solutions or add-ins in the app catalog.

#### HTTP Request

```http
GET /_api/web/tenantappcatalog/AvailableApps
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |

### Get details about individual solution packages in the app catalog

Use this REST API for getting details about individual SharePoint Framework solutions or add-ins available in the app catalog.

#### HTTP Request

```http
GET /_api/web/tenantappcatalog/AvailableApps/GetById('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |

### Install solution package from the app catalog to a SharePoint site

Install a solution package with a specific identifier from the app catalog to the site based on URL context. This REST call can be executed in the context of the site where the install operation should happen.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/AvailableApps/GetById('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')/Install
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |
| X-RequestDigest         | `{form digest}`                     |

### Upgrade solution packages on the SharePoint site

Upgrade a solution package from the site to a newer version available in the app catalog. This REST call can be executed in the context of the site where the upgrade operation should happen.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/AvailableApps/GetById('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')/Upgrade
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |
| X-RequestDigest         | `{form digest}`                     |

### Uninstall solution packages from the SharePoint site

This REST call can be executed in the context of the site where the uninstall operation should happen.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/AvailableApps/GetById('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')/Uninstall
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |
| X-RequestDigest         | `{form digest}`                     |

> [!NOTE]
> When you use the REST API to uninstall a solution package from the site, it is not relocated to the recycle bin.

### Synchronize a solution to the Microsoft Teams App Catalog

This REST call requires that you refer the **list item id** of the solution in the app catalog site.

#### HTTP Request

```http
POST /_api/web/tenantappcatalog/SyncSolutionToTeams(id=xxxxx)
```

#### Request headers

|         Header          |                Value                |
| :---------------------- | :---------------------------------- |
| Authorization           | `Bearer {token}`                    |
| Accept                  | `application/json;odata=nometadata` |
| X-RequestDigest         | `{form digest}`                     |

## SharePoint PnP PowerShell cmdlets 

By using [PnP PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/sharepoint-pnp/sharepoint-pnp-cmdlets?view=sharepoint-ps), you can automate deploying, publishing, installing, upgrading, and retracting your apps. 

> [!NOTE]
> Support for scope option was released on the April 2018 release of PnP PowerShell.

### Add and publish your app to the app catalog

Adding your app (.sppkg file, .app file) to the app catalog is a prerequisite to making your app available for use on your SharePoint sites. You can do this by using the following cmdlet:

```powershell
Add-PnPApp -Path ./myapp.sppkg -Scope Tenant
```

Once added, you need to continue with publishing your app, effectively making the app available to be used by the users of your tenant. The following PnP PowerShell cmdlet shows how this can be done:

```powershell
Publish-PnPApp -Identity <app id> -SkipFeatureDeployment -Scope Tenant
```

> [!NOTE]
> Use the `SkipFeatureDeployment` flag to allow an app that was developed for tenant-wide deployment to be actually available as a tenant-wide deployed app.

### Remove the app from the app catalog

To remove an app added earlier, use the following cmdlet:

```powershell
Remove-PnPApp -Identity <app id> -Scope Tenant
```

### Use apps on your site

After the app is added to the app catalog and published, you can install the app to your site:

```powershell
Install-PnPApp -Identity <app id> -Scope Tenant
```

To upgrade the app:

```powershell
Update-PnPApp -Identity <app id> -Scope Tenant
```

To uninstall the app from your site:

```powershell
Uninstall-PnPApp -Identity <app id> -Scope Tenant
```
> [!NOTE]
> When you uninstall an app from your site, the app is completely deleted, so it does not end up in the site's recycle bin.

### Know which apps are there for you to use

You can get a list of apps that can be added to the site by using:

```powershell
Get-PnPApp -Scope Tenant
```

## Office 365 CLI commands to add, deploy, and manage SharePoint apps cross-platform

Using the [Office 365 CLI](https://pnp.github.io/office365-cli?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs), you can automate deploying, publishing, installing, upgrading, and retracting your apps. The Office 365 CLI is a cross-platform command-line interface that can be used on any platform, including Windows, MacOS, and Linux. To learn more about these commands, see the following sections.

### Add and publish your app to the app catalog

Adding your app (.sppkg file, .app file) to the tenant app catalog is a prerequisite to making your app available for use on your SharePoint sites. Use the [add](https://pnp.github.io/office365-cli/cmd/spo/app/app-add/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command to do this:

```shell
spo app add --filePath ./spfx.sppkg
```

Once added, you need to continue with publishing your app, effectively making the app available to be used by the users of your tenant. Use the [deploy](https://pnp.github.io/office365-cli/cmd/spo/app/app-deploy/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command to do this:

```shell
spo app deploy --id <app id> --skipFeatureDeployment
```

> [!NOTE]
> Use the **SkipFeatureDeployment** flag to allow an app that was developed for tenant-wide deployment to be actually available as a tenant-wide deployed app.

### Remove the app from the app catalog

You may want to remove an app that you added earlier, and you can do this by using the [remove](https://pnp.github.io/office365-cli/cmd/spo/app/app-remove/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command:

```shell
spo app remove --id <app id>
```

### Use apps on your site

After the app is added to the app catalog and published, you can install the app to your site by using the [install](https://pnp.github.io/office365-cli/cmd/spo/app/app-install/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command:

```shell
spo app install --id <app id> --siteUrl <url>
```

To upgrade the app, use the [upgrade](https://pnp.github.io/office365-cli/cmd/spo/app/app-upgrade/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command:

```shell
spo app upgrade --id <app id> --siteUrl <url>
```

To uninstall the app from your site, use the [uninstall](https://pnp.github.io/office365-cli/cmd/spo/app/app-uninstall/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command:

```shell
spo app uninstall --id <app id> --siteUrl <url>
```

> [!NOTE]
> When you uninstall an app from your site, the app is completely deleted, so it will not end up in the site's recycle bin.

### List and get apps in the app catalog
You can see what apps have been added to the app catalog by using the [list](https://pnp.github.io/office365-cli/cmd/spo/app/app-list/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command:

```shell
spo app list
```

You can get a single app's details by using the [get](https://pnp.github.io/office365-cli/cmd/spo/app/app-get/?utm_source=msft_docs&utm_medium=page&utm_campaign=Application+Lifecycle+Management+ALM+APIs) command:

```shell
spo app get --id <app id>
```

## See also

- [Get to know the SharePoint REST service](../sp-add-ins/get-to-know-the-sharepoint-rest-service.md)
