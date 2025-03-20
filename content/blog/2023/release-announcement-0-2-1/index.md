---
author: savitharaghunathan
date: 2023-07-06
description: >-
  Konveyor v0.2.1 Release Announcement
featured: true
image: release-0-2-1.png
tags:
  - Konveyor
  - Kubernetes
  - AppModernization
title: Konveyor v0.2.1 Release Announcement
---
We are thrilled to announce the release of Konveyor 0.2.1! This release supports various features, like migration waves, integration with Jira, and other valuable minor updates.

Key Highlights of Konveyor 0.2.1:
* Migration waves
* Jira Integration
* Minor improvements

### Migration Waves:
We are excited to introduce a significant addition to the Konveyor project, aimed at providing enhanced value throughout the migration process. We recognized the need to address the planning aspect required to scale the migration process across entire application portfolio. When dealing with a large-scale portfolio containing hundreds or even thousands of applications, it becomes impractical to execute the migration process in one go. To tackle this challenge, we have introduced the ability for Konveyor to define migration waves, allowing for an iterative and manageable approach. By breaking down the portfolio into waves, we enable a more sophisticated process that  in the future we expect will automate the calculation of these waves based on various criteria. This feature empowers users to scale adoption efforts effectively, ensuring a seamless and efficient migration experience.

### Jira Integration:
Jira Integration is a significant development in response to a key concern raised by top managers involved in modernization and migration initiatives. Understanding the overall project progress is crucial, and recognizing that most organizations utilize their task management tools, we have enabled Konveyor to integrate seamlessly with these existing systems to leverage their capabilities. Our approach entails creating tasks or issues within the respective task/issue managers to represent the work associated with each application in the portfolio. Konveyor will then retrieve real-time information about the current status of each application, providing comprehensive project visibility. We have designed a flexible and adaptable integration layer, ensuring that it remains independent of any specific task/issue manager peculiarities on the Konveyor side. Jira is the first implementation, but others could come in the future with other popular issue managers like the ones available in GitHub and GitLab.

### Minor improvements:
These enhancements aim to refine and enhance the overall user experience of our platform. Some of the notable improvements are:

* ServiceMonitor for hub metrics: We have introduced ServiceMonitor functionality to capture and monitor hub metrics, providing valuable insights into the performance of your system.

* Expose Hub metrics: With this update, we have exposed hub metrics, allowing users to access and utilize metrics for monitoring and analysis purposes.

* Make Ingress Creation Optional: This update allows Ingress creation optional, providing flexibility and control over your deployment configurations.

* Support for Amazon Load Balancer Ingress controller: Our platform now supports the Amazon Load Balancer Ingress controller, enabling Konveyor to be easily deployed on EKS clusters.

* Provide openshift-oauth as an authentication option: As part of our ongoing efforts to enhance authentication capabilities, we now offer openshift-oauth as an additional authentication option, providing more choices to meet your specific requirements.

* Make rwx_supported false by default: This feature removes the RWX dependency enabling Konveyor to work without RWX volume by default.

These minor improvements reflect our commitment to continuously refining and enhancing our platform to deliver an optimized and user-friendly experience. We value your feedback and encourage you to explore these enhancements as you leverage our latest release. You can install Konveyor 0.2.1 from [community operators](https://operatorhub.io/operator/konveyor-operator/konveyor-0.2/konveyor-operator.v0.2.1). If you encounter any issues or have suggestions for features, we encourage you to open an issue [here](https://github.com/konveyor/operator/issues/new).

Happy Modernizing!
