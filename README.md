# Use-Azure-Synapse-Link-for-SQL
# Use Azure Synapse Link for SQL

This document outlines the steps to configure and use Azure Synapse Link for SQL to synchronize data from an Azure SQL Database to a dedicated SQL pool in Azure Synapse Analytics.

## Prerequisites

* An Azure subscription with administrative-level access.

## Steps

### 1. Provision Azure Resources

Provision an Azure SQL Database and an Azure Synapse Analytics workspace using the provided PowerShell script.

1.  Sign in to the [Azure portal](https://portal.azure.com).
2.  Open **Cloud Shell** by clicking the **[\>_]** button. Select **PowerShell** if prompted.
3.  Clone the repository:
    ```powershell
    rm -r dp-203 -f
    git clone [https://github.com/MicrosoftLearning/dp-203-azure-data-engineer](https://github.com/MicrosoftLearning/dp-203-azure-data-engineer) dp-203
    ```
4.  Navigate to the lab directory and run the setup script:
    ```powershell
    cd dp-203/Allfiles/labs/15
    ./setup.ps1
    ```
5.  If prompted, choose your subscription and enter a password for your Azure SQL Database. **Remember this password!**
6.  Wait for the script to complete (approximately 15 minutes).

### 2. Configure Azure SQL Database

Prepare your Azure SQL Database for Synapse Link.

1.  In the Azure portal, navigate to the `dp203-xxxxxxx` resource group and select your `sqldbxxxxxxxx` Azure SQL server. **Ensure you select the SQL server, not the Synapse SQL pool.**
2.  **Enable Managed Identity:**
    * In the left-hand pane, under **Security**, select **Identity**.
    * Under **System assigned managed identity**, set **Status** to **On**.
    * Click **🖫 Save**.
3.  **Configure Firewall Rules:**
    * In the left-hand pane, under **Security**, select **Networking**.
    * Ensure the exception **Allow Azure services and resources to access this server** is enabled.
    * Click **＋ Add a firewall rule**.
    * Configure the rule with the following settings:
        * **Rule name**: `AllClients`
        * **Start IP**: `0.0.0.0`
        * **End IP**: `255.255.255.255`
    * Click **Save**. **Note:** In a production environment, restrict access to specific IP ranges.

### 3. Explore the Transactional Database

Examine the `AdventureWorksLT` database on your Azure SQL server.

1.  On the **Overview** page of your Azure SQL server, at the bottom, select the **AdventureWorksLT** database.
2.  In the **AdventureWorksLT** database page, select the **Query editor** tab.
3.  Log in using SQL server authentication with the following credentials:
    * **Login**: `SQLUser`
    * **Password**: The password you specified during the setup script.
4.  In the query editor, expand the **Tables** node and observe the tables, particularly those in the `SalesLT` schema (e.g., `SalesLT.Customer`).

### 4. Configure Azure Synapse Link

Set up the link connection in your Synapse Analytics workspace.

1.  In the Azure portal, close the query editor for your Azure SQL database and return to the `dp203-xxxxxxx` resource group.
2.  Open your `synapsexxxxxxx` Synapse workspace.
3.  Open **Synapse Studio**.
4.  Go to the **Manage** page and select the **SQL pools** tab.
5.  Select the row for the `sqlxxxxxxx` dedicated SQL pool and click the **▷** (Start) icon to resume it. Confirm when prompted.
6.  Wait for the SQL pool status to become **Online**. Refresh if needed.

### 5. Create the Target Schema

Create a schema in your dedicated SQL pool to match the source schema.

1.  In Synapse Studio, go to the **Data** page and select the **Workspace** tab.
2.  Expand **SQL databases** and select your `sqlxxxxxxx` database.
3.  In the **...** menu for the `sqlxxxxxxx` database, select **New SQL script** > **Empty script**.
4.  In the new SQL script pane, enter the following code:
    ```sql
    CREATE SCHEMA SalesLT;
    GO
    ```
5.  Click the **▷ Run** button to execute the script. Wait for it to complete successfully.

### 6. Create a Link Connection

Establish the connection between your Synapse workspace and the Azure SQL Database.

1.  In Synapse Studio, go to the **Integrate** page.
2.  Click the **＋** dropdown menu and select **Link connection**.
3.  Configure a new linked connection with the following settings:
    * **Source type**: `Azure SQL database`
    * **Source linked service**: Click **New** and configure the new linked service:
        * **Name**: `SqlAdventureWorksLT`
        * **Description**: `Connection to AdventureWorksLT database`
        * **Connect via integration runtime**: `AutoResolveIntegrationRuntime`
        * **Version**: `Legacy`
        * **Connection String**: `Selected`
        * **From Azure subscription**: `Selected`
        * **Azure subscription**: Select your Azure subscription
        * **Server name**: Select your `sqldbxxxxxxxx` Azure SQL server
        * **Database name**: `AdventureWorksLT`
        * **Authentication type**: `SQL authentication`
        * **User name**: `SQLUser`
        * **Password**: The password you set during the setup script
        * Click **Test Connection** to verify the settings.
        * Click **Create**.

The next steps in the lab would involve creating a Link connection to specify which tables to synchronize and then exploring the synchronized data in your dedicated SQL pool. This README covers the initial setup and configuration required to reach that stage.

**Important:** Remember to follow the remaining steps in the lab to fully explore Azure Synapse Link for SQL. After completing the entire lab, remember to delete the resource group `dp203-xxxxxxx` in the Azure portal to avoid incurring unnecessary costs.
