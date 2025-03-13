---
title: "Seeding Instances"
date: 2022-06-14T14:59:46-06:00
draft: false
---
Tackle instances have key parameters that are configured in the Controls window prior to migration by the project architect and can be added and edited as needed.  
![](/Tackle2/Instances/ControlsMain.png)
These parameters define applications and individuals, teams, verticals or areas within an organization affected or participating in the migration.
* Stakeholders
* Stakeholder groups
* Job functions
* Business services
* Tag types
* Tags

## Seeding Tackle instance
The steps to creating and configuring a Tackle instance can be performed in any order, but the suggested order below is the most efficient for creating stakeholders and tags.

Stakeholders
1. Create stakeholder groups
2. Create job functions
3. Create stakeholders

Tags
1. Create tag types
2. Create tags

Tackle stakeholders and defined by:
* Email
* Name
* Job function
* Stakeholder groups

## Creating a new stakeholder group
Follow the steps below to create a new stakeholder group.  There are no default stakeholder groups defined.

**Procedure**
1. Click **Controls** in the left menu of the **Developer** view.
2. Click the **Stakeholder groups** tab.
3. Click the **Create new** button.
![](/Tackle2/Instances/NewStakeholderGroup.png)
4. Enter the following information:
    * Name
    * Description
    * Member(s)
5. Click the **Create** button.

## Creating a new job function
Tackle uses the job function attribute to classify stakeholders and provides a list of default values that can be expanded. Follow the steps below to create a new job function not included in the default list.

**Procedure**
1. Click **Controls** in the left menu of the **Developer** view.
2. Click the **Job functions** tab.
3. Click the **Create new** button.
![](/Tackle2/Instances/NewJobFunction.png)
4. Enter a **job function title** into the **Name** text box.
5. Click the **Create** button.

## Creating a new stakeholder
Follow the steps below to create a new migration project stakeholder.

**Procedure**
1. Click **Controls** in the left menu of the **Developer** view.
2. Click the **Stakeholders** tab.
3. Click the **Create new** button.
![](/Tackle2/Instances/NewStakeholder.png)
4. Enter the following information:
    * Email
    * Name
    * Job function: Addition beyond the standard can be created
    * Stakeholder group
5. Click the **Create** button.

## Business services
Tackle uses the business services attribute to define the departments within the organization that use the application and will be affected by the migration.

**Important:** Business services must be created prior to creating or importing applications.

## Creating a new business service
Follow the steps below to create a new business service. There are no default business services defined and must be created prior to creating applications.

**Procedure**
1. Click **Controls** in the left menu of the **Developer** view.
2. Click the **Business services** tab.
3. Click the **Create new** button.
![](/Tackle2/Instances/NewBusinessService.png)
4. Enter the following information:
    * Name
    * Description
    * Owner
5. Click the **Create** button.

## Creating new tag types
Tackle uses tags to classify applications in multiple categories. Follow the steps below to create a new tag type not included in the default list.

**Procedure**
1. Click **Controls** in the left menu of the **Developer** view.
2. Click the **Tags** tab.
3. Click the **Create tag type** button.
![](/Tackle2/Instances/NewTagType.png)
4. Enter the following information:
    * Name
    * Rank - order the tags appear on applications
    * Color
5. Click the **Create** button.

## Creating new tags
Follow the steps below to create a new tag not included in the default list.

**Procedure**
1. Click **Controls** in the left menu of the **Developer** view.
2. Click the **Tags** tab.
3. Click the **Create tag** button.
![](/Tackle2/Instances/NewTag.png)
4. Enter the following information:
    * Name
    * Tag Type
5. Click the **Create** button.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Konveyor/instances.md)
