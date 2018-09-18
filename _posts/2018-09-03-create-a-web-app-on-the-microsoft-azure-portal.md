---
image:
  path: "/assets/2018-08-22-p1/microsoft-azure.png"
---

You want to host your ASP.NET Core Web app on Microsoft Azure and have no idea where to start?

This article will provide a thorough guide on how to use the Azure Portal. You'll create your Web app Container, that will ultimately host your actual Web app.

## Prerequisites
Assuming you are familiar with Azure App Service, also known as Web Apps we can proceed. If not, I recommend the official documentation for Azure App Service, on the Microsoft Docs website. Get yourself acquainted with all you need to know and discover what Azure App Service can provide you as a developer.

To access the Microsoft official documentation on Azure App Service, follow this link: [Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/)

In addition, you need to have a valid Azure subscription. 

## Azure Portal

Microsoft provides the Azure Portal that plays the role of a Web Interface for all of the features that Azure contains. For this article and the coming ones in this series, I am going to use the **preview** version of the Azure Portal. Microsoft announced the preview version back in February 2017 here: [New Azure Preview Portal Grants Early Access to Features](https://buildazure.com/2017/02/03/new-azure-preview-portal-grants-early-access-to-features/) 

To start with:

1. Go to [Azure Portal][https://preview.portal.azure.com/] 

2. Login with your credentials to access the portal.
 
## Create Web App Container

To host any Web app on the Azure Portal, start by creating a new resource of type Web App.

1. Locate and click on **Create a resource** link at the top-left side of your screen.

   ![Create a Resource](/assets/2018-09-03-p1/create-a-resource.png)

2. The portal navigates you to the **Azure Marketplace** blade. You can then search for any resource type you are interested in creating. In this case, type **Web App** and hit *Enter* to start the search.

   ![Search Azure Marketplace](/assets/2018-09-03-p1/search-marketplace.png)

3. Click on **Web App** resource type. The Web App documentation blade opens.

4. Next, click on the button **Create**. The Portal navigates you to the **New Web App** blade.

5. Give your app a name by filling in the **App Name** field. In this case, I'm going to call this app **AnAppADay**. 

6. Select a valid subscription from the dropdown list field.

   ![New Web App](/assets/2018-09-03-p1/new-web-app.png)

### Add a Resource Group

When you create a new Web App, you need to assign a resource group for that app.

In this article, I won't delve into a detailed explanation of what resource groups are. However, in summary, a resource group is a logical grouping or categorization of resources making the task of managing and controlling resources easier. To read more about resource groups, you can watch this video: [Getting Started on Azure Resource Groups](https://www.youtube.com/watch?v=favhwFCx2VU)

Going back to the **New Web App** blade, the Portal automatically selects the **Create new** option under the **Resource Group** field. In addition, the Portal is using the application name you selected as a name for the resource group.

You have the option to either create a new resource group or use an existing one. In our case, we've decided to create a new resource group.

### Web App Operating System

For the OS (Operating System) field, you have the choice of three different types of operating systems. Windows, Linux or Docker. For now, we will choose the Windows OS. You may choose any of the other options depending on the final outcome you desire.

### Add an App Service Plan

Essentially, the Web App stores and serves your application from one or more virtual machines. One of the best features of Azure Web Apps is that you don't have to deal with the underlying virtual machines or even know anything about them. The App Service plan does that automatically. You only need to focus on your Web app, building and testing it properly and finally deploying it to Azure. The rest Azure handles for you!

An App Service plan is the container for your app. The App Service plan settings will determine the location, features, cost and compute resources associated with your app. To learn more about App Service plan you can follow this link: [Azure App Service plan overview](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)

Going back to the **New Web App** blade:

1. Click on the **App Service plan/location** field.

2. Azure Portal opens a new blade to list existing App Service plans.

3. Locate and click the **Create new** link 

4. Azure Portal opens the **New App Service Plan** blade as shown below

    ![New App Service Plan](/assets/2018-09-03-p1/new-app-service-plan.png)
    
5. Select a relevant name for the new App Service plan. Azure will check if it is available to use. In this case, you may type something like *AnAppADayAppServicePlan*.

6. For the location field, you may select any location of interest. In this case, I will keep the default selection as is (Central US).

7. The pricing tier field allows you to select the **virtual machine** resources that are going to host your Web app. If you click this field, you can browse and read all the details about the available pricing tiers on Azure. For instance, the **S1 Standard** pricing tier is characterized by the features below:

    - 1x cores
    - 100 total ACU
    - 1.75 GB memory
    - A-Series compute
    - 44.64 USD/Month (Estimated)
    - Custom domains/SSL
    - Auto scale
    - Staging slots
    - Daily backups
    - Traffic manager

    To check other pricing tiers, simply click on one of them and read the details provided by Azure Portal.
    
    For our scenario, we will be selecting the **S1 Standard** tier.

8. To check other pricing tiers, simply click on one of them and read the details provided by Azure Portal.

9. We will be selecting the **S1 Standard** tier.

10. Click on the **Apply** button.

11. On the **New App Service Plan** blade, locate and click on the **OK** button.

    ![New Web App Populated](/assets/2018-09-03-p1/new-web-app-populated.png)

12. Now click on the **Create** button to create the new Web App. Azure takes a few seconds to create and prepare the Web App for you.

### Verify and Test the new Web App

1. Locate and click on the new Web App. The Portal navigates you to the **Web App** blade. On that blade, you can access all the features and settings related to the Web App.

    ![Web App Section](/assets/2018-09-03-p1/web-app-section.png)

    You may use the Web App section to stop, restart, browse and perform many other operations against the Web app. 
    
2. Locate and navigate to the App URL highlighted in the figure above. The URL is  [https://anappaday.azurewebsites.net](https://anappaday.azurewebsites.net). Upon visiting this URL, Azure displays a default Web Page signaling the successfully creation of the Web App.

    ![App Up and Running](/assets/2018-09-03-p1/web-app-running.png)
    
## Create Staging Deployment Slot

Azure automatically creates a **Production Deployment Slot** once it creates a new Web App.

Think of a deployment slot as an independent copy of your web application that Azure redirects external traffic to based on the App URL.

For instance, the production deployment slot receives all HTTP requests to access the Web App via the following URL [https://anappaday.azurewebsites.net](https://anappaday.azurewebsites.net)

Whereas the staging deployment slot receives all HTTP requests to access the Web App via the following URL [https://anappaday-staging.azurewebsites.net](https://anappaday-staging.azurewebsites.net)

With the help of a deployment slot you can deploy your Web App to the staging slot. Your client would be running the app in a live environment, similar to that of a production environment, testing and verifying the features and changes on the Web App.

Once the client is satisfied and happy with the app, you can **swap** the staging to production deployment slots. This means:

- Redirecting all HTTP requests to the Web App, via the following URL [https://anappaday.azurewebsites.net](https://anappaday.azurewebsites.net), to the app files stored at the staging deployment slot.

- Redirecting all HTTP request to the Web App, via the following URL [https://anappaday-staging.azurewebsites.net](https://anappaday-staging.azurewebsites.net), to the app files stored at the production deployment slot.

For this example, we need to create a staging deployment slot to deploy our ASP.NET Core App to. Once we are happy with the app hosted on the staging deployment slot we can easily swap the staging deployment slot with the production one.

1. Locate and navigate to the Web App created above.

2. Locate and click on the **Deployment slots** menu item on the **Web App** blade left side menu.

3. Click on the **+ Add Slot** link located at the top-left side of the **deployment slots** blade.

4. To create a new slot, you need to provide a name for the new deployment slot. In this case, we will call this deployment slot as staging.

    ![New Deployment Slot](/assets/2018-09-03-p1/new-deployment-slot.png)

5. Locate and click on **OK** button to create the new deployment slot.

6. Find and navigate back to the **Deployment slots** menu item on the **Web App** blade left side menu.

7. The Azure Portal display a list of all deployment slots created under the Web App.

    ![Web App Deployment Slots](/assets/2018-09-03-p1/deployment-slots.png)

8. Click on the newly created staging deployment slot.

9. Azure Portal navigates you to the **Staging Web App** blade. You can tell right away that the staging deployment slot is a typical independent Web app characterized by its own URL.

    ![Staging Deployment Slot](/assets/2018-09-03-p1/staging-deployment-slot.png)

10. Locate and click on the Staging Web App URL. Azure redirects you to the default page that's usually displayed for a newly created Web app.

### Set Deployment Credentials

To deploy your Web app to the staging deployment slot you need to set the deployment credentials first. 

1. Navigate to the staging deployment slot blade.

1. Locate and click on the **Deployment credentials** menu item on the left side menu. 
1. Enter a value for both the *username* and *password* fields to setup the deployment credentials as shown below.

    ![Deployment Credentials](/assets/2018-09-03-p1/deployment-credentials.png)

### Set Deployment Source

The final step in configuring the staging deployment slot is to set up a deployment source. With a deployment source you choose the source where the Web app files will be retrieved or pushed from. Azure provides several options as illustrated below.

For us we will focus on the Local Git Repository. This is a Git repository fully managed by Azure.

![Deployment Option](/assets/2018-09-03-p1/deployment-option.png)

You may read more about Azure deployment sources here: [Configure deployment sources](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-app-service-configure-deployment-sources)

To configure a deployment source:

1. Navigate to the staging deployment slot blade.

2. Locate and click on the **Deployment option** menu item on the left side menu. 

1. On the *Deployment option** blade, click the **Choose Source / Configure required settings** field.

    ![Deployment Sources](/assets/2018-09-03-p1/deployment-sources.png)

2. Locate and select the **Local Git Repository** option.

3. On the **Deployment option** blade, locate and click the **OK** button.

The staging deployment slot is now fully configured:

![Staging Deployment Slot Configured](/assets/2018-09-03-p1/staging-slot-configured.png)

## Summary

Today, we've created the Web App container and prepared the app for deployment by setting up the staging deployment slot.

In the next article, we'll be creating an ASP.NET Core app using the .NET Core CLI as part of this series on creating and hosting an ASP.NET Core app on Microsoft Azure.
