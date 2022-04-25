---
title: "Konveyor Documentation"
date: 2022-04-25T14:58:17-06:00
draft: false
---
# Konveyor Documentation

# Konveyor background
Sysadmins and Developers are tired of the words “Digital Transformation.”  They don’t want to hear about the “five keys to digital transformation” or the “seven must-haves to transform.” They are also tired of every vendor presenting their framework and methodology for digital transformation bundled with a bunch of proprietary tools.

Developers and sysadmins want to learn how to actually transform their applications and infrastructure so they can take advantage of new technologies to deliver new capabilities faster, at greater scale, and with higher quality while reducing technical debt. It is clear that one of the technologies that underpins the future of applications and infrastructure is Kubernetes - an open-source system for automating deployment, scaling, and management of containerized applications.

The Konveyor community exists to accelerate sysadmins and developers' journey to Kubernetes. Konveyor is a community of people passionate about helping others modernize and migrate their applications for Kubernetes by building tools, identifying patterns, and providing advice.

# Konveyor projects

Konveyor currently consists of five tools or projects:

Forklift is focused on migrating virtual machines to Kubernetes and provides the ability to migrate multiple virtual machines to KubeVirt with minimal downtime.  It allows organizations to rehost, or “lift and shift'' applications residing on these VMs.  While rehosting doesn’t provide the same depth of benefits as replatforming or refactoring, it’s the first step in the right direction. It’s often useful to have all the unmodified development workloads in Kubernetes as a basecamp for further transformations,  or in cases where development teams may not have the ability to change or modify code, such as with vendor provided software. Rehosting also helps teams enjoy the benefits of the new platform with less friction in improving process and culture.  

The downstream version of this tool, Migration Toolkit for Virtualization (MTV) is available to Red Hat customers interested in moving vSphere virtual machines to OpenShift Virtualization.  As of February 2021, it is available as tech preview.

Crane is another rehosting tool that meets a different use case.  It allows organizations to migrate applications between Kubernetes clusters.  There are many times when developers and operations teams want to migrate between older and newer versions of Kubernetes, evacuate a cluster, or migrate to different underlying infrastructure. In an ideal scenario, this would be a redeployment of the application, but in reality we have found that many users need a solution for migrating persistent data and the objects within Kubernetes namespaces continuously.

The downstream version of Crane, Migration Toolkit for Containers (MTC), is available for Red Hat OpenShift customers interested in moving from version 3.x to 4, as well as from different clusters of version 4.  It is fully supported and available on OpenShift Operator Hub.

Move2Kube is a project that allows customers to replatform their applications to Kubernetes orchestrated platforms.  Replatforming, or “lift, tinker, and shift”,  involves changing an underlying technology used by an application while minimizing the need for code change. One area where replatforming is taking place is in the consolidation of container orchestration platforms to Kubernetes. Due to this consolidation, the Move2Kube project was started to focus on accelerating the process of replatform to Kubernetes from platforms such as Swarm and Cloud Foundry. The project translates existing artifacts to Kubernetes artifacts to speed up the process of being able to run applications on Kubernetes.

Tackle provides a series of interrelated tools that allows users to assess, analyze, and ultimately move their applications onto a Kubernetes orchestrated platform.  Often considered the most challenging application modernization strategy, adapting applications to a containerized runtime, also offers the largest potential long term impact. This strategy involves making changes to the application and development to take advantage of cloud native capabilities. The tool helps assess the depth of the changes ranging from minimal fixes to adapt the application to containers to a full rewrite of the application in more modern container-friendly runtimes.The Tackle project provides tools that inventory an application environment and  identify which workloads are most suitable for refactoring into containers.  A common application inventory can also be generated which is then made available as a basis for code execution. The team that is catalyzing this project has experience in these areas from working on tools such as Pathfinder and Windup and will be bringing these experiences to their work on the Tackle project.

The downstream version of Tackle, Migration Toolkit for Applications (MTA), is an assembly of tools that support large-scale Java application modernization and migration projects across a broad range of transformations and use cases. MTA accelerates application code analysis, supports effort estimation, accelerates code migration, and helps users move applications to a variety of platforms including OpenShift.

Pelorus is focused around measuring the improvement that moving and modernizing actually delivers as described in the Accelerate book, the reference in DevOps. The community feels strongly that being able to measure the impact of rehosting, replatform, refactoring, and changing processes and culture is vital to proving value. Pelorus is a project focused on measuring the key metrics to software delivery performance (lead time for change, deployment frequency, mean time to restore, and change failure rate) and enabling metrics driven transformation.

Together, these five projects comprise today the Konveyor community.  Moving forward, it is inevitable that demand for each will align with evolving migration strategies and technology trends.  For example, whereas rehosting with Forklift defined use cases may reach heightened demand in the short term, this project could also very well lose traction as Virtual Machines are fully containerized across the landscape in the longer term.  New tools could then hypothetically emerge in the Konveyor community empowering users to further extend these containerized applications to edge computing environments, perhaps relying on AI/ML and event-driven architectures.  That being said, it is widely expected that there will be ebb and flow with existing project usage, as well as the introduction of entirely new projects altogether.

-Content taken from the [Konveyor Messaging Guide](https://docs.google.com/document/d/1ihwqflbCH2baS_lqGUJVbImJcwMc1-GFyP1SV1QjrDY/edit#heading=h.th9fk8bf62kx)

