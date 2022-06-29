---
title: "Assessing and analyzing applications"
date: 2022-06-14T14:59:11-06:00
draft: false
---
Tackle core functions are assessing and analyzing the applications for migration and are performed on the Application inventory page.
![](/Tackle2/AppAssessAnalyze/ApplicationInventory.png)

## Assessing applications
Follow the steps below to facilitate discussion of application migration.

**Procedure**
1. Click **Application inventory** in the left menu in the **Development** view.
![](/Tackle2/AppAssessAnalyze/AnalysisSelect.png)
2. Click the checkbox to the left of the application being assessed.

**Note:** Only one application can be assessed at a time.

3. Click the **Assess** button.
![](/Tackle2/AppAssessAnalyze/AssessDetails.png)
4. Select the **Stakeholders** and **Stakeholder groups** from the drop-down lists to track who contributed to the assessment for future reference.

**Note:** Additional list options can be added in the Stakeholder Groups or Stakeholders tab on the Controls page in the Developer view.

5. Click the **Next** button.
6. Click the radio button next to the option that best answers the questions in each category and click **Next** to go to the next section when complete.
7. Click **Save and Review** to view the risks that should be taken into account.

### Applying assessments to other applications
Follow the steps below to apply an application assessment to similar applications being migrated.

**Procedure**
1. Click **Application inventory** in the left menu in the **Development** view.
2. Click the checkbox of the application with the completed assessment to copy.
![](/Tackle2/AppAssessAnalyze/CopyAssessSelect.png)
3. Click the menu kebab at the right of the selected application.
4. Select **Copy assessment** or **Copy assessment and review**.
![](/Tackle2/AppAssessAnalyze/CopyAssessAssign.png)
5. Click the checkbox of the application(s) to copy the assessment or assessment and review to.
6. Click the **Copy** button.

## Running application analysis
Follow the steps below to analyze an application for migration.

**Procedure**
1. Click **Application inventory** in the left menu in the **Development** view.
2. Click the **Analysis** tab.
3. Click the checkbox to the left of the application being analyzed.

**Note:** More than one application can be analyzed at a time.

![](/Tackle2/AppAssessAnalyze/SelectManageCred.png)

4. Check the credentials assigned to the application.
5. Click the **Analyze** button.

![](/Tackle2/AppAssessAnalyze/AnalysisMode.png)

6. Select source for analysis from the drop-down list.
7. Click the **Next** button.
8. Set the target to one or more of the transformation targets.
* Application server migration to…
    * Containerization
    * Quarkus
    * OpenJDK
    * OpenJDK 11
    * Linux
    * Camel
9. Click the **Next** button.
10. Click a radio button to select one of the following scope options to narrow down and focus the analysis.
    * Application and internal dependencies only
    * Application and all dependencies, including known Open Source libraries
    * Select the list of packages to be analyzed manually (Type the file name and click Add.)
    * Exclude packages (Type the package name and click Add.)
11. Click the **Next** button.
12. Set custom rules by typing a name or searching and clicking the **Add Rules** button.  Consider the following rules:
    * Target
    * Source
    * Exclude tags: Rules with these tags are not processed.

 **Note:** Analysis engines use standard rules for a comprehensive set of migration targets, but if the target is not included or customized frameworks custom rules can be added.  Custom rules files will be validated.

13. Click the **Next** button.
14. Add or remove targets and sources to narrow the analysis.
15. Exclude rules tags as necessary.
16. Click the **Next** button.
17. Verify the analysis parameters.
18. Click the **Run** button.

Analysis status will show Scheduled as it downloads the image for the container to execute. When that is complete, it will show In-progress until complete.

**Note:** The analysis will take minutes to hours to run depending on the size of the application and the cluster capacity and resources.

Tackle relies on Kubernetes scheduling capabilities to determine how many analyzer instances are created based on cluster capacity. If several applications are selected for analysis, by default, only one analyzer can be provisioned at a time.  With more cluster capacity, more analysis processes can be executed in parallel.
19. Expand the application and click the **Report** link to the right of Analysis when completed.

### Reviewing the Analysis Report
Follow the steps below to review the analysis report.  For more information see [Chapter 3. Reviewing the reports of the CLI Guide Migration Toolkit For Applications 5.3](https://access.redhat.com/documentation/en-us/migration_toolkit_for_applications/5.3/html-single/cli_guide/index#review_reports_cli-guide).

**Procedure**
1. Click **Application inventory** in the left menu in the **Development** view.
2. Expand the application with a completed analysis.
3. Click the **Report** link.
4. Click the dependencies or source links.
5. Click the tabs to review the report.  

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Tackle/Tackle2/assessanalyze.md)
