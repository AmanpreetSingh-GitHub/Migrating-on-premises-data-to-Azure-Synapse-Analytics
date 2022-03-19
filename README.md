# Migrating on-premises SQL Data Marts to Azure Synapse Analytics Dedicated SQL pool #

<br />

## Overview ##

In this article we will be migrating our on-premises SQL Data Warehouse to Dedicated SQL pool in Azure Synapses Analytics. We will do this process in three stages -

- Migrating the metadata using Azure Synapse Pathway
- Making any changes to table design and code
- Migrating the data using integration capabilities of Azure Synapse

<br />

## Prerequisites ##

<br />

### Setup sample Data Warehouse locally ###

<br />

- Download backup of <i>AdventureWorksDW2019.bak</i> from https://docs.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms

    <img src="Images/1.PNG" style="border:1px solid darkgrey">

<br />

- Restore the backup locally

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/2.PNG' width="50%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/3.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/4.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/5.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/6.PNG' width="50%" style='border:1px solid darkgrey'>

<br />

### Install Azure Synapse Pathway locally ###

<br />

- Download and install <i>Azure Synapse Pathway</i> from https://www.microsoft.com/en-us/download/details.aspx?id=103061


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/7.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/11.PNG' width="50%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/12.PNG' width="50%" style='border:1px solid darkgrey'>

<br />

### Create Azure Synase Workspace ###

<br />

- Register <i>Microsoft.Synapse</i> provider for your subscription

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/24.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

- Create <i>Azure Synapse Analytics</i> service

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/25.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select your <i>subscription</i>
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Create a new <i>resource group</i> for the demo
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Provide <i>workspace name</i>
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select <i>region</i>
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Provide <i>Data Lake Storage Gen2</i> account and check <i>assign myself contributor role</i> checkbox

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/26.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Create <i>SQL Sever admin login</i> and <i>SQL password</i> to login

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/27.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Leave rest of the configuration as default and hit <i>Review + create</i>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/28.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/32.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/35.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Open and check your <i>workspace</i>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/36.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

<br />

## Migrating the metadata using Azure Synapse Pathway ##

<br />

- First step is to generate scripts for all the database objects
from on-premises <i>AdevntureWorksDW2019</i> data warehouse

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/14.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/15.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select only <i>Tables</i> for generting scripts  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/16.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/17.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/18.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/19.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/20.PNG' width="70%" style='border:1px solid darkgrey'>

<br />

- Now open <i>Azure Synapse Pathway</i> tool installed locally and follow below steps

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Translation type</i> - select <i>Microsoft SQL Server</i>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Input directory</i> - select the directory where generated scripts are stored 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Output directory</i> - select folder where you want to store the output of translation

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Click <i>Translate</i>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/21.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/22.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

<br />

## Making any changes to table design and code ##

<br />

- In this step, we can redesign the tables so as to achieve optimized performance on Dedicated SQL pools
    - Microsoft has provided guidance on how to design distributed tables - https://docs.microsoft.com/en-gb/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute

<br />

- We also resolve any errors that we got from Azure Synapse Pathway as some code might need to be changed due to it being not compatible with Dedicated SQL pools
    - A list of those incompatibilities is listed here - https://docs.microsoft.com/en-gb/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-develop-stored-procedures#limitations

<br />

<br />

## Migrating the data using integration capabilities of Azure Synapse ##

<br />

We will use Azure Synapse Analytics <i>Integrate</i> hub to create integration pipeline for copying data from on-premises SQL Data Warehouse to the Dedicated SQL pool

<br />

- In your Azure Synapse Anaytics Workspace, go to <i>Integrate</i> hub and select <i>Copy Data tool</i> by clicking on <i>+</i> sign. It will open up <i>Copy Data tool</i> wizard

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/39.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- Select <i>Built in copy task</i> and <i>Run once now</i> radio button to create the pipeline

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/40.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- For <i>Source type</i> select <i>SQL Server</i> and create a new connection between on-premises environment and Azure using self-hosted integration runtime

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Click on <i>+ New</i> under <i>Connect via integration runtime</i> to create a self-hosted integration runtime

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/41.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/42.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Provide a name for your integration runtime

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/43.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Use <i>Option 1: Express Setup</i> to download and install integration runtime on your Windows machine

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/44.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/45.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/46.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- After creating the integration runtime, provide below setting to connect to your on-premises SQL Data Warehouse

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Server name</i>: name of your local SQL instance
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Database name</i>: AdventureWorksDW2019
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Authentication type</i>: SQL authentication
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>User name</i> and <i>Password</i>: credentials with access rights to the DataWarehouse

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Click on <i>Test connection</i> and <i>Apply</i> settings

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/47.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- Now we will setup <i>configuration</i>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select the tables as the source data for the copy task. We will select <i>dbo.DimAccount</i> for our demo

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/49.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Preview the data

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/50.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- Select <i>Destination data store</i> now

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Create a <i>New connection</i> and select <i>Azure Synapse Analytics</i>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/51.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/52.PNG' width="50%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select the Dedicated SQL pool created in Azure Synapse Analytics workspace

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select your Azure </i>subscription

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Server name</i>: select your Synapse workspace

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Database name</i>: select the Dedicated SQL pool

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- <i>Authentication type</i>: select <i>SQL authentication</i> along with <i>User name</i> and <i>Password</i> you created while setting up your Synapse workspace

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/55.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/56.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- Now select <i>Configuration</i> you want to apply

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/57.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Update any <i>Column mappings</i> for each table. We will uncheck <i>Type conversion</i> checkbox

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/58.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- Provide final <i>Settings</i> for your pipeline

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Provide <i>Task name</i>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Check <i>Enable staging</i> checkbox to first move your data to a staging area and then to Dedicated SQL pool
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Setup <i>Staging settings</i> by providing a <i>Storage Path</i> to the Data Lake account


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select <i>Copy method</i> as <i>PolyBase</i>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/59.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/60.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- <i>Review</i> the pipeline and deploy the pipeline

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/61.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/62.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/63.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Click on <i>Monitor</i> to check the <i>Pipeline runs</i>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/64.PNG' width="80%" style='border:1px solid darkgrey'>

<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/65.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/66.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

- Once the pipeline has run successfully, you can go to <i>Data</i> tab and select your Dedicated SQL pool under <i>Workspaces</i> to check the copied data for <i>dbo.DimAccount</i> table

<br />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src='Images/67.PNG' width="80%" style='border:1px solid darkgrey'>

<br />

## Conclusion ##

<br />

Here you saw how you can migrate your on-premises SQL Data Warehouse to Dedicated SQL pool in Azure Synapse Analytics.