---
title: Build Static Web App on Azure with JavaScript
description: Build a JAMstack app (JavaScript, APIs, and Markup) on Azure
ms.topic: how-to
ms.date: 05/03/2021
ms.custom: devx-track-js
---

# Build a new Static Web App on Azure with Node.js

Azure Static Web Apps is a service that automatically builds and deploys full stack web apps to Azure from a code repository.

* **Client apps**: Static web apps are commonly built using libraries and frameworks like Angular, React, Svelte, Vue, or Blazor where server-side rendering is not required.
* **APIs**: API endpoints are hosted using a serverless architecture, which avoids the need for a full back-end server all together.

**Samples**:

* [Static Web Apps community samples](https://github.com/microsoft/static-web-apps-gallery-code-samples) are a great way to find a code to use a starter for your project.
* [Static Web App starter projects](https://github.com/staticwebdev/) are another great way to start your project.

## What is a Static Web App?

An Azure Static Web Apps is a hosted app with both the generated static client files and the optional API endpoints. When you create the Static Web App, you include information necessary for a GitHub Action to build the static files from your GitHub repository then deploy to Azure.

Create the Static Web App with one of the following:

* [VS Code extension](/azure/static-web-apps/getting-started?tabs=vanilla-javascript)
* [Azure CLI](/azure/static-web-apps/get-started-cli?tabs=vanilla-javascript)
* [Azure portal](/azure/static-web-apps/get-started-portal?tabs=vanilla-javascript)

    :::image type="content" source="../media/howto-static-web-app/azure-portal-create-static-web-app.png" alt-text="When you create the Static Web App, you include information necessary for a GitHub Action to build the static files and deploy to Azure.":::

## Use Static Web Apps CLI

The [Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli), also known as SWA CLI, serves as a local development tool for Azure Static Web Apps. It can:

* Serve static static app assets, or proxy to your app dev server
* Serve API requests, or proxy to APIs running in Azure Functions Core Tools
* Emulate authentication and authorization
* Emulate Static Web Apps configuration, including routing

## Include APIs for a full-stack app

[Azure Function](/azure/azure-functions/) APIs are provided in Static Web Apps optionally and typically live in a folder named `/api`. These functions allow you to develop a full-stack web site without needing to deal with the server-side configuration of an entire web hosting environment. Learn more about [Azure Function apps with JavaScript](/azure/azure-functions/functions-reference-node).

**Samples**:

* [Azure serverless community library of samples](https://serverlesslibrary.net/)

## Develop with Visual Studio Code

Use the Visual Studio code [extension for Static Web Apps](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) to create your local folder structure and initial dependencies.

1. Fork one of the [GitHub templates repositories](https://github.com/staticwebdev/) for your client and API choice or [create a new repository](https://github.com/new).
1. In VS Code, create a new Static Web App.
1. In the creation steps, select your repository fork and branch.

    When you push to this repository and branch, your code is also deployed to the Static Web App. It is common to have a `live` or `deploy` branch for that purpose.

1. In the creation steps, select the project structure, location of the application code, and the build directory.

    You can usually take the default values if your folder structure follows the typical folder structure for the project type.

1. When you finish the creation steps, your repository fork has a GitHub Action to build and deploy to your Static Web App, located in the `/.github/workflows` directory.

**Tutorials**, which use the [Azure Static Web Apps extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) include:

* [Building your first static site](/azure/static-web-apps/getting-started?tabs=vanilla-javascript)
* [Add search to a website with Azure Search](/azure/search/tutorial-javascript-overview)
* [Analyze an image with Azure Computer Vision](/azure/developer/javascript/tutorial/static-web-app/introduction)

## Configure client environment variables

The GitHub Action controls [environment variables](/azure/static-web-apps/github-actions-workflow#environment-variables) injected into your project at build time. These client-side variables need to be configured in the GitHub Action's yaml in the `env` section. Secrets should be [stored in GitHub secrets and pulled in to the `env` section](/azure/developer/github/github-variable-substitution).

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_<GENERATED_HOSTNAME> }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match your app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "search-website" # App source code path
          api_location: "search-website/api" # Api source code path - optional
          output_location: "build" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          # Inject vars at build time
          myvarname: 'myvarvalue' 
          # Inject secrets at build time from GitHub Secrets
          password: ${{ secrets.PASSWORD }}

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_<GENERATED_HOSTNAME> }}
          action: "close"
```  

## Configure API environment variables

The API environment variables are runtime variables configured in the Azure portal or Azure CLI.

* Azure portal: Under **Settings** then **Configuration**

    :::image type="content" source="../media/static-web-app/azure-portal-settings-configuration.png" alt-text="Azure portal: Under **Settings** then **Configuration**":::

* VSCode extension: Under **Production** then **Application Settings**

    :::image type="content" source="../media/static-web-app/vscode-settings-configuration.png" alt-text="VSCode extension: Under **Production** then **Application Settings**":::

* Azure CLI: Using [az staticwebapp appsettings set](/cli/azure/staticwebapp/appsettings#az_staticwebapp_appsettings_set)

## Deploy to Azure

Deploying a Static Web App to Azure is started by pushing to the source code repository's specific branch, listed in the GitHub Action under `pull_requests:branches`. The push from your local computer needs to use the Static Web App's repository or fork of a repository. If your GitHub user account doesn't have permission to push to the specified branch on the specified organization repo, such as your company's GitHub organization, you should fork the repository, then configure your GitHub Action to use your fork.

View deployment success from the GitHub Action.

:::image type="content" source="../media/howto-static-web-app/github-action-build-and-deploy-status.png" alt-text="View deployment success from the GitHub Action.":::

## Enable logs

Turn on **Application Insights** in the Azure portal for your Static Web App to collect logging. The integrated [Application Insights](/azure/azure-monitor/app/javascript) logging collects a huge amount of information for you, without any changes to your code.

## Develop custom logging

To add custom logging from your app to Application Insights, add the [@microsoft/applicationinsights-web](https://www.npmjs.com/package/@microsoft/applicationinsights-web) npm package then add the JavaScript code to capture custom information.

```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.trackTrace({message: 'some trace'});
```

## Next steps

* Learn more about [Static Web Apps](/azure/static-web-apps/)
* [Add an API](/azure/static-web-apps/add-api) in Static Web Apps
