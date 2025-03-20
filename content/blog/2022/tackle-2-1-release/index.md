---
author: konveyor
date: 2022-09-14T07:00:00Z
description: >-
  New features include a new report that gives details about the application data layer, easier CSV importing, bulk deletion of applications, and a more streamlined user experience.
featured: true
image: tackle-2-1-release.png
tags:
  - Tackle
title: 'Tackle 2.1 release'
---

After the major milestone that [Tackle 2.0](/blog/tackle-2-new-capabilities/) represented, the community has continued gathering feedback from the field to further polish the feature set and provide value in the context of modernization, migration and adoption projects. This has led to Tackle 2.1, available now in [operatorhub.io](https://operatorhub.io/), a minor release that has been focused on stabilizing the project and providing a more streamlined user experience, while still delivering some new tooling. The new features include the following:

- **Integration with the Tackle DiVA project:** New report available in the application analysis results providing detailed information about the application data layer, including an extensive inventory of transactions and accessed databases with detailed stack traces for each transaction.
- **Automated creation of missing entities on CSV import:** Tags and Business Services are not required to exist in Tackle prior to the import anymore. This simplifies dealing with large CSV files and the import process overall.
- **Bulk delete of applications:** Applications can now be deleted at bulk in the application inventory. This is especially useful when applications have been imported at bulk by mistake or with some errors.

Once again, I would like to congratulate all the different teams involved in the implementation, testing and documentation of this new release of Tackle. We continue building this project together in the community, so any feedback or feature request is definitely welcome. If you feel like something is missing or some feature is not behaving as expected, don’t hesitate to [open an issue in the Tackle main repository](https://github.com/konveyor/tackle2-hub/issues).

Happy modernizing!
