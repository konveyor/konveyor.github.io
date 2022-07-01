---
title: "Managing assessments"
date: 2022-05-05T15:15:05-06:00
draft: false
---

Start, edit, review, and delete application assessments in the Application inventory page of the Tackle web console.

## Starting an assessment
Follow the steps below to start an application assessment on the Application inventory page of the Tackle web console.

**Procedure**
1. Start the Tackle web console
2. Click **Application Inventory**.
3. Select an application that does not have a Completed assessment status and click the **Assess** button in the toolbar.
4. Select individual stakeholders or stakeholder groups and then click **Next**.
5. Select responses to all questions on each page of the Application Assessment wizard and then click **Next**.

> **Important:** All questions are mandatory.

6. Click **Save** to save the assessment, or **Save and Review** to start the assessment review process.

The Assessment status of each assessed application is set to Completed.

## Editing an assessment
Follow the steps below to take an application assessment on the Application inventory page of the Tackle web console.

**Prerequisites**
An application must have a Completed assessment status.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Select an application that does not have a Completed assessment status and click the **Assess** button in the toolbar.
4. Click **Continue** to confirm editing the assessment.
5. Update entries.
6. Click **Save** to save the assessment, or **Save and Review** to start the assessment review process.

##Reviewing an assessment
Follow the steps below to review an application assessment on the Application inventory page of the Tackle web console.

**Prerequisites**

An application must have a Completed assessment status and a Not started review status.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Select an application with a Completed assessment status and a Not Started review status and click the **Review** button in the toolbar.
4. Review the assessment in the Assessment summary section on the Review page.

> **Note:** The Assessment summary table contains a Risk column that indicates the severity of the risk associated with each response.

5. Select a Proposed action and an Effort estimate.
6. Set values for Business criticality and Work priority.

**Recommended:** Enter comments in the **Comments** field.

7. Click **Submit Review**.

The Review status of each application is set to Completed.

8. Expand the application to view the review results.

## Copying and applying assessments and reviews.
Follow the steps below to:

* Copy an assessment or assessment and review from a single application and apply them to multiple applications on the Application inventory page of the Tackle web console.
* Apply assessments and reviews to groups of related applications, for example, applications written in Java or belonging to the same business service.

** Prerequisites

An application must have a Completed assessment status or Completed assessment and review statuses, depending on whether an assessment or an assessment and review is being copied.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Click the **Options** menu kebab beside an application with a Completed assessment status or Completed assessment and Review statuses.
4. Select Copy assessment or Copy assessment and review.

**Optional:** In the dialog box, click **Name** to select a filter, for example:
* Tag and select a tag.
* Java to display a filtered list of applications.

5. Select the applications to apply the copied assessment or assessment and review to.
6. If a selected application has an existing assessment or review, select the Yes, continue check box to confirm that existing assessments and reviews will be overwritten.
7. Click Copy.

The selected applications are set to a Completed assessment status or Completed assessment and review statuses.

## Deleting an assessment
Follow the steps below to delete an application assessment on the Application inventory page of the Tackle web console. Deleting an assessment deletes its review.

**Prerequisites**

An application must have a Completed assessment status.

**Procedure**
1. Open the Tackle web console.
2. Click **Application Inventory**.
3. Click he **Options** menu kebab beside an application with a Completed assessment status and select **Discard** assessment.
4. Click **Continue** to confirm the deletion.

The Assessment and Review status of the application are set to Not started.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/manageassess.md)
