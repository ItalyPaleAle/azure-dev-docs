---
title: Configure your local JavaScript environment for Azure development
description: How to set up a local JavaScript dev environment for working with Azure, including an editor, the Azure SDK libraries, optional tools, and the necessary credentials for library authentication.
ms.date: 04/16/2021
ms.topic: conceptual
ms.custom: devx-track-js, azure-sdk-javascript-ai-text-analytics-5.0.0
---

# Configure your local JavaScript dev environment for Azure

When creating cloud applications, developers typically prefer to test code on their local workstations before deploying that code to a cloud environment like Azure. Local development gives you the advantage of a wider variety of tools along with a familiar development environment.

This article provides setup instructions to create and validate a local dev environment that's suitable for JavaScript with Azure.

## One-time subscription creation

Azure resources are created within a subscription, which is the billing unit for using Azure. While you can create free resources (each subscription offers a free resource for most services), you should create paid-tier resources when you expect to deploy your resource to production.

|Type|Description|
|--|--|
|Trial subscription|Create a _free_ [trial subscription](https://azure.microsoft.com/free/).|
|Existing subscription|If you already have a subscription, access your existing subscription in the [Azure portal](https://portal.azure.com), the [Azure CLI](), or JavaScript.|
|Across multiple subscriptions|If you need to manage multiple subscriptions, [learn how](/azure/governance/management-groups/create-management-group-javascript) to create a management group with JavaScript.|

## One-time software installation

To develop using an Azure resource with JavaScript on your local workstation, you need the following installed once:

|Name/Installer|Description|
|--|--|
|[Node.js 8+](https://www.npmjs.com/)|Install latest long-term support (LTS) runtime environment for local workstation development. A package manager is also required. Node.js installs NPM in the 8.x version. The Azure SDK generally requires a minimum version of Node.js of 8.x. Azure hosting services, such as Azure App service, provides runtimes with more recent versions of Node.js. If you target a minimum of 8.x for local and remove development, your code should run successfully.|
|[Visual Studio Code](https://code.visualstudio.com/)| Visual Studio Code will give you a great JavaScript integration and coding experience but it is not required. You can use any code editor. For this document, if you are using a different editor, check for integration with Azure or use the Azure CLI.|

> [!CAUTION]
> If you plan to use an Azure resource as the runtime environment for your code, such as an Azure web app or an Azure Container Instance, you should verify your local Node.js development environment matches the Azure resource runtime you plan to use.

### Recommended local installations

The following common local workstation installations are recommended to help with your local development tasks.

|Name/Installer|Description|
|--|--|
|[Azure CLI](/cli/azure/get-started-with-azure-cli) or [Visual Studio Code extensions for Azure](https://marketplace.visualstudio.com/search?term=azure&target=VSCode&category=All%20categories&sortBy=Relevance) |Working with Azure is usually completed with the [Azure portal](https://ms.portal.azure.com/), the Azure CLI, or specific Visual Studio Code extensions to work with Azure. While you don't have to have the Azure CLI, unless specified in a quickstart or tutorial, it is a single tool to work with Azure while Visual Studio Code provides the same functionality on an extension-per-service basis.|
| [git](https://git-scm.com/downloads) | Command-line tools for source control. You can use a different source control tool if you prefer. |


## One-time configuration of service principal

[Create a service principal](/azure/developer/javascript/how-to/with-sdk/set-up-development-environment?tabs=azure-sdk-for-javascript#1-create-a-service-principal) to create, use Azure resources _without_ using your personal account. 

Learn more about a [service principals](/azure/active-directory/develop/app-objects-and-service-principals).

## Working with Azure and the Azure SDK client libraries

The [Azure SDK libraries](../azure-sdk-library-package-index.md) are provided individually for each service. You install each library based on the Azure service you need to use.

Each new project using Azure should:
- Create Azure resources and save associated keys or configuration to a [secure location](#securing-configuration-information).
- Install Azure SDK libraries from NPM or Yarn. 
- Use Service Principal to authenticate to Azure SDKs, then use configuration information to access specific services.

## Securing configuration information

You have several options to store configuration information:
- [Dotenv](https://www.npmjs.com/package/dotenv) is a popular npm package to read environment variables from a `.env` file. Make sure to add the `.env` file to the `.gitignore` file so the `.env` file is not checked into to source control. Learn more about [environment variables](../how-to/configure-web-app-settings.md) in web apps for Azure. 
- Azure [Key Vault](/azure/key-vault/) to create and maintain keys that access and encrypt your cloud resources, apps, and solutions

### Create environment variables for the Azure libraries

To use the Azure settings needed by the Azure SDK libraries to access the Azure cloud, set the most common values to [environment variables](../how-to/configure-web-app-settings.md). The following commands set the environment variables to the local workstation. Another common mechanism is to use the `DOTENV` NPM package to create a `.env` file for these settings. If you plan to use a `.env`, make sure to not check in the file to source control. Add the `.env` file to git's `.ignore` file is the standard way to ensure those settings are checked into source control.

In the following examples, the client ID is the service principal ID and service principal secret.

# [bash](#tab/bash)

```bash
AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
```

# [cmd](#tab/cmd)

```cmd
set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
```

---

Replace the values shown in these commands with those of your specific service principal.

## Install npm packages

For every project, we recommend that you always create a separate folder, and its own `package.json` file using the following steps:

1. Open a terminal, command prompt, or bash shell and create a new folder to the project. Then move into that new folder.

    ```console
    mkdir MY-NEW-PROJECT && cd MY-NEW-PROJECT
    ```

1. Initialize the package file:

    ```console
    npm init -y
    ```

    This command runs the NPM command to create the package.json file and initialize the minimum properties. As you install Azure SDK library packages with NPM or Yarn, the package.json file captures that installation information.

1. Install the Azure SDK libraries you need for the quickstart. The following command is an example.

    ```console
    npm install @azure/ai-text-analytics@5.0.0
    ```

## Use source control with Visual Studio Code

We recommend that you get into the habit of creating a source control repository whenever you start a project. You can do this from Visual Studio Code. 

1. In Visual Studio Code, select the source control icon to open the **Source Control** explorer, then select **Initialize Repository** to initialize a local Git repository:

    ![Initialize git repository](../media/setup-environment/initialize-local-repository.png)

1. After the repository is initialized, and you have files to store in source control, enter the message `Initial commit` and select the checkmark to create the initial commit of your source files.

    ![Complete an initial commit to the repository](../media/setup-environment/initial-commit.png)

1. Create a new repository on [GitHub](https://github.com/new) or [Azure DevOps](https://dev.azure.com/) and copy the repository URL for the next few steps. 

1. In the Visual Studio integrated terminal, use the following [git](https://git-scm.com/docs) command to add your remote repository to your local repository. Replace `YOUR-ALIAS` and `YOUR-REPOSITORY` with your own values.

    ```bash
    git remote add origin https://github.com/YOUR-ALIAS/YOUR-REPOSITORY
    ```

Visual Studio Code includes a number of built-in git features. For more information, see [Using Version Control in VS Code](https://code.visualstudio.com/docs/editor/versioncontrol).

## Next steps

* [Create and use a service principal](../how-to/with-sdk/set-up-development-environment.md)
* [Authenticate with the Azure modules for Node.js](node-sdk-azure-authenticate.md)

