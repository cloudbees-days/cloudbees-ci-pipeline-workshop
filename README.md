# CloudBees Core Pipeline Workshop
This workshop will provide a basic understanding of how to create and manage Jenkins CI/CD Declarative Pipelines leveraging developer focused features of CloudBees Core.

This repository contains instructions and learning materials for the workshop that is designed to teach the following key concepts:

  * How specific features of CloudBees Core on Kubernetes will accelerate your CD?
  * Why you should *mostly* use [Declarative](https://jenkins.io/doc/book/pipeline/syntax/#declarative-pipeline) vs [Scripted](https://jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline) pipelines?
  * How reusable templates for Jenkins Declarative Pipelines can increase developer productivity?
  * What are the key features of Declarative Pipelines?


To get started goto the [**Setup Instructions**](Setup.md).

# Workshop Prerequisites

In order to follow along with the hands on portion of the workshop students should have the following resources available to them:

  * Internet access to include access to https://github.com to include the ability to access and use [the GitHub File Editor](https://help.github.com/articles/editing-files-in-your-repository)
  * An account on https://github.com and a basic understanding of how to use GitHub to do things like fork a repository, edit files in the web UI, and create pull requests
  * A personal access token for your Github account ([Github-Personal-Access-Token.md](Github-Personal-Access-Token.md)) with the following permissions:
    - repo: all
    - admin:repo_hook: all
    - admin:org_hook
    - user: all
  * A basic understanding of Docker: https://docs.docker.com/get-started/
  * A basic understanding of Kubernetes: https://kubernetes.io/docs/tutorials/kubernetes-basics/
  * A basic understanding of Jenkins Pipelines: https://jenkins.io/doc/book/pipeline/getting-started/ 
   
Detailed setup instructions are available at **[Setup](Setup.md)**

# Workshop Labs

**The labs covered in this workshop are available at the following links:**

* Lab 1. [Introduction to Declarative Pipelines](./declarative-basics.md)
* Lab 2. [Stage specific agents](./stage-specific-agents.md)
* Lab 3. [Conditional Execution using the `when` directive](./conditional-when.md)
* Lab 4. [Custom Pipeline Pod Templates](./custom-pipeline-pod-templates.md)
* Lab 5. [CloudBees Pipeline Template Catalogs](./catalog-templates.md)
* Lab 6. [CloudBees Cross Team Collaboration](./cross-team-collaboration.md)
* Lab 7. [Pipeline with Interactive Input](./input.md)

**Optional Labs:**

* Lab A. [Using the `options` directive](./options-directive.md)


# Disclaimer

Although the examples and code in this repository was originally created by employees of CloudBees, Inc. to use in training customers, your use of this material is not sponsored or supported by CloudBees, Inc.

# Contributors 

* Contributors: [Cojan van Ballegooijen](https://github.com/cvanballegooijen), [Kurt Madel](https://github.com/kmadel), [Logan Donley](https://github.com/logandonley)
 
# Questions, Feedback, Pull Requests Etc.

If you have any questions, feedback, suggestions, etc. please submit them via issues or, even better, submit a Pull Request!

