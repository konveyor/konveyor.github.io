---
title: "Using Move2Kube UI"
date: 2022-08-04T19:10:11-06:00
draft: false
weight: 4
---
Similar to the command line tool, the Move2Kube Web-UI can also perform the transformation with all the capabilities that are in the command line tool. This document explains the steps to bring up the UI and backend using Docker and use it for transformation.

## Prerequisites

1. Install [Docker](https://www.docker.com/get-started).

1. Use the [language-platforms](https://github.com/konveyor/move2kube-demos/raw/main/samples/language-platforms) sample. The language-platforms file has a combination of multiple applications in different languages (Java, Go, Python, Ruby, etc.) which need to be containerized and then put into Kubernetes.

## Using the UI to perform a transformation

1. Download the language platforms sample as a zip.
    ```console
    $ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/language-platforms -r move2kube-demos -z
    $ ls
    language-platforms.zip
    ```

1. Run `docker run --rm -it -p 8080:8080 quay.io/konveyor/move2kube-ui`.  
    ```console
    $ docker run --rm -it -p 8080:8080 quay.io/konveyor/move2kube-ui
    INFO[0000] Starting Move2Kube API server at port: 8080
    ```

This starts a container using the Move2Kube UI image on port `8080`.

    ```console
    # Optionally if you need persistence then mount the current directory:
    $ docker run --rm -it -p 8080:8080 -v "${PWD}/move2kube-api-data:/move2kube-api/data" quay.io/konveyor/move2kube-ui:latest
    # And if you also need more advanced features of Move2Kube then mount the docker socket. This will allow Move2Kube to run container based transformers:
    $ docker run --rm -it -p 8080:8080 -v "${PWD}/move2kube-api-data:/move2kube-api/data" -v //var/run/docker.sock:/var/run/docker.sock quay.io/konveyormove2kube-ui:latest
    ```

3. Open [http://localhost:8080](http://localhost:8080) in a browser.

1. Click the **New Workspace** button to create a new workspace named `Workspace 1`.
    
1. Scroll down and click the **New Project** button to create a new project named `Project 1`.

1. Scroll down to the **Project inputs** section and upload the `language-platforms.zip` file downloaded earlier in this tutorial and wait for it to finish uploading.

1. Scroll down to the **Plan** section and click on the **Start Planning** button.

> **Note:** Generating the plan takes about three to five minutes to generate the plan in YAML format.

8. Scroll to view the different services.

> **Important:** Click **Save** after all edits.

9. Scroll down to **Outputs** section and click on the **Start Transformation** button.

1. Move2Kube will ask some questions to aid in the transfomation process.

    For the container registry question, specify the container registry where you want to push the images.
    
    Same for the container registry namespace question.

    If your container registry requires authentication for pulling images, specify that in the container registry login question.

    For the ingress host question, specify the hostname of the Kubernetes cluster. If you are deploying to Minikube then specify `localhost` as the hostname and leave the TLS secret blank.
   
    For all other questions, click the `Next` button to accept the default answers.

1. Click on the output to download the generated artifacts as a zip file (here `workspace-1-project-1-output-bcad1e64-23d0-4ea1-ad47-9d060e870b4f.zip`), extract it, and browse them. The applications can now be deployed to Kubernetes using these generated artifacts.

1. Build and push the container images and deploy to Kubernetes using the downloaded output.  The steps for doing that are same as the CLI tutorial.  

## Conclusion

This tutorial shows how easy it is to performa a transformation using the UI. The UI can be hosted on a common server and used by different teams using different workspaces and also has authentication and authorization capabilities to restrict access to particular workspaces.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/Tutorials/usingUI.md