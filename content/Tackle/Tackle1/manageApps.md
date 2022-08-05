---
title: "Managing applications"
date: 2022-05-05T10:51:42-06:00
draft: false
---
---
Managing Applications
---
Follow the procedures in this section to create, import, tag, and modify the applications in the Application inventory page of the Tackle web console.

## Creating an application
Follow the steps below to create an application on the Application inventory page of the Tackle web console.

**Procedure**
1. Open the Tackle web console.
2. Click **Application** inventory and then **Create New**.
3. Complete the following fields:
* **Name:** Name of the application.
* **Description:** Optional. Description of the application.
* **Business service:** Optional. Select a business service that describes the application.
* **Tags:** Optional. Select one or more tags.
* **Comments:** Optional. Comments about the application.
4. Click **Create**.

The new application is displayed on the Application inventory page.
Expand the application to view its tags and comments.

## Importing applications
Follow the steps below to import one or more applications into the Application inventory page of the Tackle web console by using a CSV file.

> **Note:** Do not create tags or business services by importing a CSV file. Specified tags or business services must exist in the web console before importing the applications.

The CSV file contains the following fields:
* Record Type 1:
* Describes an application or application dependencies.
* Required for all records.

The following values are allowed for Record Type 1:

* **Application:** This option has the following fields:
    * **Application Name:** Required
    * **Description:** Optional
    * **Comments:** Optional
    * **Business Service:** Optional. Must exist in the web console
    * **Tag Type <1..20>:** Optional. Must exist in the web console.
    * **Tag <1..20>:** Optional. Must exist in the web console.

> **Note:** Import up to 20 Tag Type <x> and Tag <x> fields.

* **Application dependencies:** This option requires the following fields:
    * **Application Name**
    * **Dependency:** Must be the same as the Application Name of the dependency.
    * **Dependency Direction:** Allowed values are northbound and southbound.

All other fields are empty.  See the CSV example below.

|Record Type 1|Application Name|Dependency|Dependency Direction|Description|Comments|Business Service|Tag Type 1|Tag 1|Tag Type 2|Tag 2|Tag Type 3|Tag 3|
|-|-|-|-|-|-|-|-|-|-|-|-|-|
|1|Imported-Purchasing|Vendor management|Required for purchase order processing and accounts payable|Finance and HR|Operating System|Z/OS|Database|DB2|Language|COBOL|
|2|Imported-Purchasing|Tiller|northbound|
|1|Imported-PO|||Requisitions, purchase orders, goods received|Requisition to receipt|Finance and HR|Operating System|Z/OS|Database|DB2|Language|COBOL|
|2|Imported-Payroll|Imported-GL|northbound|
|2|Imported-Payroll|Imported-HR|southbound|
|1|Imported-GL|||General Ledger||Finance and HR|Operating System|Z/OS|Database|DB2|Language|COBOL|
|1|Imported-HR|||Human Resources|Go live scheduled for Q3|Finance and HR|Operating System|RHEL 8|Database|PostgreSQL|Language|Python|

**Prerequisites**

* Valid CSV file.

Specified business services, tag types, and tags created in the web console.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Click the **Options** menu kebab in the toolbar and select **Import**.
4. Browse to the CSV file and click **Open**.
5. Click **Import**.

The imported applications are displayed on the Application inventory page.

## Managing application imports
Follow the steps below to manage application imports on the Application import page of the Tackle web console.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Click the **Options** menu kebab in the toolbar and select **Manage Imports**.

The Application import page displays a list of application imports.

4. Click the **Options** menu kebab beside an application import and select one of the following options:
* **Delete:** Deletes the application import.
* **View Error:** Report displays a table of application import errors.
* **Export Errors:** Saves the application import errors as a CSV file.

## Updating the tags assigned to an application
Follow the steps below to add or remove tags assigned to an application on the Application inventory page of the Tackle web console.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Click the **Edit** icon beside an application.
4. Add or delete tags and click **Save**.
5. Expand the application to view the updated tags.

## Updating the business service assigned to an application
Follow the steps below to update the business service assigned to an application on the Application inventory page of the Tackle web console.

**Prerequisites**

* The business service must exist on the Controls page.

**Procedure**
1. Open the Tackle web console.
2. click **Application Inventory**.
3. Click the **Edit** icon beside an application.
4. Select a business service and click **Save**.

The updated business service is displayed in the Business service column of the application.

## Managing application dependencies
Follow the steps below to add, delete, and view application dependencies in the Manage dependencies window on the Application inventory page of the Tackle web console.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Click the **Options** menu kebab beside an application and select **Manage Dependencies**.
4. Do one of the following:
* **To add dependencies:** Select applications in the northbound or southbound dependencies fields.
* **To remove dependencies:** Delete selected applications in the northbound or southbound dependencies fields.
5. Click **Close** to save and close.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/manageapps.md)
