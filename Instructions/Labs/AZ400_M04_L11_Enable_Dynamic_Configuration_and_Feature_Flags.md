# Lab 02: Enable Dynamic Configuration and Feature Flags

## Lab requirements

- This lab requires **Microsoft Edge** or an [Azure DevOps supported browser.](https://learn.microsoft.com/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers)

- **Set up an Azure DevOps organization:** If you don't already have an Azure DevOps organization that you can use for this lab, create one by following the instructions available at [Create an organization or project collection](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).

- Identify an existing Azure subscription or create a new one.

- Verify that you have a Microsoft account or an Azure AD account with the Contributor or the Owner role in the Azure subscription. For details, refer to [List Azure role assignments using the Azure portal](https://learn.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) and [View and assign administrator roles in Azure Active Directory](https://learn.microsoft.com/azure/active-directory/roles/manage-roles-portal).

## Lab overview

[Azure App Configuration](https://learn.microsoft.com/azure/azure-app-configuration/overview) provides a service to manage application settings and feature flags centrally. Modern programs, especially those running in a cloud, generally have many distributed components. Spreading configuration settings across these components can lead to hard-to-troubleshoot errors during application deployment. Use App Configuration to store all the settings for your application and secure their accesses in one place.

## Objectives

After you complete this lab, you will be able to:

- Exercise 1: Run CI/CD Pipelines
- Exercise 2: Manage Azure App Configuration

## Estimated timing: 60 minutes

## Architecture Diagram

  ![Architecture Diagram](images/lab11-architecture-new.png)
      
# Exercise 1: Run CI/CD Pipelines

In this exercise, you will import and run the CI pipeline, configure the service connection with your Azure Subscription and then import and run the CD pipeline.

In this task, you will create an eShopOnWeb Azure DevOps project to be used by several labs.

## Task 1: Run the CI pipeline

Let's start by importing the CI pipeline named **eshoponweb-ci.yml**.

1. From the left navigation pane go to **Pipelines > Pipelines**.Click on **New pipeline** button
   
2. Select **Azure Repos Git (Yaml)**.

   ![](images/az-400-jan25-10.png)

3. Select the **eShopOnWeb** repository.

   ![](images/10.selecteshoponwebrepo.png)

4. Select **Existing Azure Pipelines YAML File**.

   ![](images/az-400-jan25-12.png)

5. Select the **/.ado/eshoponweb-ci.yml** file then click on **Continue**.

   ![](images/az-400-jan25-13.png)

6. Click the **Run** button to run the pipeline.

   ![](images/13.runthepipeline.png)

7. Your pipeline will take a name based on the project name. Let's **rename** it for identifying the pipeline better. Go to **Pipelines>Pipelines** and click on the recently created pipeline. Click on the **ellipsis** and **Rename/move** option. Name it **eshoponweb-ci** and click on **Save**.

   ![](images/az-400-jan25-15.png)
   
   ![](images/az-400-jan25-14.png)

 ## Task 2: Import and run the CD pipeline
 
 Let's import the CD pipeline named **eshoponweb-cd-webapp-code.yml*.
 
   1. Go to **Pipelines(1)>Pipelines(2)** and Click on **New pipeline(3)** button

      ![](images/az-400-jan25-16.png)

   2. Select **Azure Repos Git (Yaml)**

      ![](images/az-400-jan25-10.png)

   3. Select the **eShopOnWeb** repository

      ![](images/az-400-jan25-11.png)

   4. Select **Existing Azure Pipelines YAML File**

      ![](images/az-400-jan25-12.png)

   5. Select the **/.ado/eshoponweb-cd-webapp-code.yml(1)** file then click on **Continue(2)**

      ![](images/33.newpipeline-4-1.png)
   
      In the YAML pipeline definition, customize:
      
      - **AZ400-EWebshop-NAME(1)** replace NAME with **<inject key="DeploymentID" enableCopy="false"/>**.
      - **YOUR-SUBSCRIPTION-ID(2)** with your Azure subscription id.
      - **az400-webapp-NAME(3)** replace NAME with **<inject key="DeploymentID" enableCopy="false"/>**.

      ![](images/34.ymlnamereplace-1.png)
         
   6. Click on **Save and Run** and wait for the pipeline to execute successfully.

      > **Note**: The deployment may take a few minutes to complete.When the pipeline is running if you get a prompt  as **Permissions Needed** click on **view** and click on permit 

       ![](images/35.pipelinesave&run.png)
       
       ![](images/36.pipelinesuccess.png)

      The CD definition consists of the following tasks:
       - **Resources**: it is prepared to automatically trigger based on CI pipeline completion. It also downloads the repository for the bicep file.
       - **AzureResourceManagerTemplateDeployment**: Deploys the Azure Web App using bicep template.

  7. Your pipeline will take a name based on the project name. Let's **rename** it for identifying the pipeline better. Go to **Pipelines(1)>Pipelines(2)** and click on the recently created pipeline. Click on the **ellipsis(3)** and **Rename/move(4)** option. Name it **eshoponweb-cd-webapp-code(5)** and click on **Save(6)**.

     ![](images/az-400-jan25-15.png)   
     
     ![](images/38.rename-2.png) 

# Exercise 2: Manage Azure App Configuration

In this exercise, you will create the App Configuration resource in Azure, enable the managed identity and then test the full solution.

>Note: This exercise doesn't require any coding skills. The website's code implements already Azure App Configuration functionalities.
If you want to know how to implement this in your application, please take a look at these tutorials: [Use dynamic configuration in an ASP.NET Core app](https://learn.microsoft.com/azure/azure-app-configuration/enable-dynamic-configuration-aspnet-core) and [Manage feature flags in Azure App Configuration](https://learn.microsoft.com/azure/azure-app-configuration/manage-feature-flags).

## Task 1: Create the App Configuration resource

1. In the Azure Portal, search for the **App Configuration** service

   ![](images/39.appconfig-1.png)
   
   ![](images/40.createappconfig.png)
   
2. Click **Create app configuration** then select:
    - Your Azure Subscription(1)
    - The Resource Group **(2)** created previously (it should be named **AZ400-EWebShop-<inject key="DeploymentID" enableCopy="false"/>**)
    - Retain the same region which is in the CD Pipeline previously **(3)**
    - Give a unique name **appcs-<inject key="DeploymentID" enableCopy="false"/>** **(4)**.
    - Select the **Free(5)** pricing tier

      ![](images/az-400-jan25-18.png)
     
3. Click on **Review + create(6)** then **Create**

   ![](images/42.createappconfig-3.png)
   
4. After creating the App Configuration service, go to **Overview(1)** and copy/save the value of the **Endpoint(2)**.

   ![](images/43.endpointurl.png)

## Task 2: Enable Managed Identity

1. Go to the **Web App(1)** deployed using the pipeline (it should be named **az400-webapp-<inject key="DeploymentID" enableCopy="false"/>**).

   ![](images/44.webapp-1.png)
   
2. In the **Settings(2)** section, click on **Identity(3)** then switch status to **On(4)** in the **System Assigned** section, click **save(5)>yes(6)** and wait a few seconds for the operation to finish.

   ![](images/az-400-jan25-19.png)
   
   ![](images/az-400-jan25-20.png)
   
3. Go back to the **App Configuration(1)** service and click on **Access control(IAM)(2)** then **+Add(3)->Add role assignment(4)**.

   ![](images/az-400-jan25-21.png)
   
4. In the **Role(1)** section, select **App Configuration Data Reader(2)** and click on **Next(3)**.

   ![](images/az-400-jan25-22.png)
   
5. In the **Members(1)** section, check **Manage Identity(2)** then click on **+ Select members(3)**. In the select manage identities tab select **Managed Identity** as **App Service (4)** of your **Web App(5)** (they should have the same name) and then click on **Select(6)**.

   ![](images/az-400-jan25-23.png)
   
6. Click on **Review + assign** twice.

   ![](images/50.roleassignment-4.png)

## Task 3: Configure the Web App

In order to make sure that your website is accessing App Configuration, you need to update its configuration.
1. Go back to your Web App.
2. In the **Settings(1)** section, click on **Configuration(2)** and select **+ New application setting(3)**.

   ![](images/51.Appsetting-1.png)

   > **Note**: If you dont see **+ New application setting** option, you will see a instruction stating **View and edit your application settings and connection strings from Environment variables. Click here to go to Environment Variables menu**, click on **go to Environment Variables** and click on **+ Add**.


4. Add two new application settings:
    - First app setting
        - **Name:** UseAppConfig
        - **Value:** true

            ![](images/az-400-jan25-24.png)
      
    - Second app setting
        - **Name:** AppConfigEndpoint
        - **Value:** *the value you saved/copied previously from App Configuration Endpoint. It should look like https://appcs-<inject key="DeploymentID" enableCopy="false"/>.azconfig.io*
      
            ![](images/az-400-jan25-25.png)
      
5. Click on **Apply** then **Confirm** and wait for the settings to be updated.
   
6. Go to **Overview(1)** and click on **Browse(2)**

   ![](images/lab-400-border.png)

## Task 4: Test the Configuration Management

1. In your website, select **Visual Studio(2)** in the **Brand(1)** drop-down list and click on the arrow button (**>(3)**).

   ![](images/58.brandvisualstudio.png)
2. You will see a message saying **THERE ARE NO RESULTS THAT MATCH YOUR SEARCH(4)**.

   ![](images/az-400-jan25-29.png)
   
The goal of this Lab is to be able to update that value without updating the website's code or redeploying it.

3. In order to try this, go back to **App Configuration(1)**. In the **Operations(2)** section, select **Configuration Explorer(3)** and Click on **+ Create(4) > Key-value(5)** then add:
    - **Key(6):** eShopWeb:Settings:NoResultsMessage
    - **Value(7):** *type your custom message*
   Click **Apply(8)** then go back to your website and refresh the webapp browser page.
   
   ![](images/az-400-jan25-26.png)
   
   ![](images/az-400-jan25-27.png)
   
4. You should see your new message instead of the old default value.

   ![](images/62.custommsg.png)
   
Congratulations! In this task, you tested the **Configuration explorer** in Azure App Configuration.

## Task 5: Test the Feature Flag

Let's continue to test the Feature manager.
1. In order to try this, go back to **App Configuration(1)**. In the **Operations(2)** section, select **Feature manager(3)**.

   ![](images/az-400-jan25-30.png)
   
2. Click on **+ Create(4)** and select **Feature flag (5)** from the dropdown then add:
    - **Enable feature flag(1):** Checked
    - **Feature flag name(2):** **SalesWeekend**
   and click **Apply(3)** then go back to your website and refresh the page.
   
      ![](images/az-400-jan25-40.png)
    
3. You should see an image with text **ALL T-SHIRTS ON SALE THIS WEEKEND**.

   ![](images/65.websiteimage.png)
   
4. You can disable this feature in App Configuration and then you would see that the image disappears.

   ![](images/az-400-jan25-41.png)
   
   ![](images/67.dissappearmsg.png)

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   - If you receive a success message, you can proceed to the next task.
   - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.
 
   <validation step="ec5155c1-0a64-4319-9b84-94137c2a9658" />

## Review

In this lab, you learned how to dynamically enable configuration and manage feature flags.

### You have successfully completed the lab.