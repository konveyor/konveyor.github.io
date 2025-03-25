---
title: "Migrating Enterprise Scale Cloud Foundry Apps to Kubernetes"
date: 2022-08-12T12:06:32-06:00
draft: false
---
This tutorial steps through the entire workflow of migratinging a Cloud Foundry application with several micro-services to run on Kubernetes.

We will be using the [enterprise-app](https://github.com/konveyor/move2kube-demos/tree/main/samples/enterprise-app) which is a retail website for shopping online. The website shows some products for sale and you can create orders by adding things to cart and checking out.

This application consists of five different services:
- **Frontend:** Website content written using React and Patternfly meant to be run on an Nginx server.
- **Gateway:** Portal to all the API servers that aggregates the orders and customer information and acts as a circuit breaker in case one of the API servers start to fail. (Written using the Java Spring Boot and PostGreSQL stack.)
- **Customers:** Manages everything related to customers. (Runs using Tomcat and PostGreSQL for the database.)
- **Orders:** Manages everything related to orders. (Written using Spring Boot and PostGreSQL for the database.)
- **Inventory:** Manages everything related to products. (Written using Spring Boot and PostGreSQL for the database.)

This tutorial will go through the workflow for containerizing this application using Move2Kube to get it running on Kubernetes. This tutorial is split into sections to make it easy to skip around.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/Tutorials/CFappsToK8/_index.md)