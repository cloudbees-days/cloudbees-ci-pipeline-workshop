# Introduction to Pipelines with CloudBees Core

In this first set exercises we will [create a GitHub Org Folder Pipeline project](), get an overview of the [basic fundamentals of the Declarative Pipeline syntax](#basic-declarative-syntax-structure), get an [introduction to Kubernetes based agents](#kubernetes-agents-with-cloudbees-core) and learn how to [add conditional logic to a Declarative Pipeline](#conditional-execution-with-when). But first we have to [add some credentials to our Team Master](#add-github-credentials-to-your-team-master) - a **Username with password** credential for the GitHub Org Folder Pipeline project.

## Add GitHub Credentials to Your Team Master

We must exit the Blue Ocean UI to the Jenkins classic UI to complete the steps in this exercise.

1. Click the ***Go to classic*** button at the top of common section of Blue Ocean’s navigation bar. <p><img src="img/intro/go_to_classic.png" width=850/>

Now, we will create a **Username and password** credential using you GitHub username and personal access token. This credential will be used to configure the *GitHub Organization* project we will create and will allow the auto-setup of a GitHub Organization level webhook:

1. Navigate to the top-level of your Team Master - this should be one level-up from where you exit the Blue Ocean UI. You should see a **Manage Jenkins** link in the left navigation menu.
2. Click on the **Credentials** link in the left navigation menu. <p><img src="img/intro/credentials_breadcrumbs_left_nav2.png" width=850/>
3. Click on the **github.com** link under **Stores scoped to Jenkins** <p><img src="img/intro/credential_scope_github.png" width=850/>
4. Click on **Add Credentials** in the left menu <p><img src="img/intro/credential_add_link2.png" width=850/> 
5. Fill out the form (**Username with password**)
  - **Username**: Your GitHub user name
  - **Password**: Your GitHub personal access token [created in setup](../Setup.md#create-a-github-personal-access-token) OR [here is the GitHub link to automatically select the required **Personal access token settings** if you haven't alreaedy done it](https://github.com/settings/tokens/new?scopes=repo,read:user,user:email,admin:org_hook,delete_repo)
  - **ID**: Create an ID for your credentials (something like **github-[Your GitHub Usename]**)
  - **Description**: Can be left blank if you want <p><img src="img/intro/credential_github_token_save2.png" width=850/>
6. Click on **OK**

## Create a GitHub Organization Folder Pipeline Project

In this exercise we are going to create a special type of Jenkins Pipeline project referred to as an [*Organization Folder*](https://jenkins.io/doc/book/pipeline/multibranch/#organization-folders) and sometimes more specifically a *GitHub Organization* project (this type of project is also [available for Bitbucket](https://plugins.jenkins.io/cloudbees-bitbucket-branch-source) and there is [unofficial support for GitLab](https://github.com/Argelbargel/gitlab-branch-source-plugin)). The Jenkins *GitHub Organization* project will scan a GitHub Organization to discover the Organization’s repositories, automatically creating **managed** [*Multibranch Pipeline* jobs](https://jenkins.io/doc/book/pipeline/multibranch/#creating-a-multibranch-pipeline) for any repository with at least one branch containing a *project recognizer* - typically **Jenkinsfile**. We will use the GitHub Organization that you created in **[Setup - Create a GitHub Organization](./Setup.md#create-a-github-organization)**. A Jenkins *GitHub Organization* project will also utilize a GitHub Organization level ***webhook it creates*** to automatically manage Jenkins jobs - both individual branch jobs and Mutlibranch Pipeline jobs associcated to repositories - when a branch or a repository is deleted from or added to the GitHub Organization.

In order to complete the following exercise you should have [forked the following repository](./Setup.md#fork-the-workshop-repository) into the Github Organization you created in **[Setup - Create a GitHub Organization](./Setup.md#create-a-github-organization)**:

* https://github.com/cloudbees-days/helloworld-nodejs  


Once that repository is forked:

1. **IMPORTANT** Navigate back to the top-level of your **Team Master** and click on the folder with the same name as your **Team Master**. This is important if you want to use [Blue Ocean](https://jenkins.io/projects/blueocean/) to visualize the Pipeline runs, because only jobs under this folder will show up in Blue Ocean.<p><img src="img/intro/org_folder_team_folder.png" width=850/> 
2. Click on **New Item** in the left navigation menu - make sure that you are in the folder with the same name as your team, and not at the root of your Team Master  <p><img src="img/intro/org_folder_bluesteel_folder.png" width=850/>
3. Enter your GitHub Organization name as the **Item Name** 
4. Select **GitHub Organization** as the item type
5. Click **Ok** <p><img src="img/intro/org_folder_item.png" width=850/>
6. Select the credentials you created above from the **Credentials** drop down 
7. Make sure that the **Owner** field matches the name of your GitHub Organization name
8. Click the **Save** button <p><img src="img/intro/org_folder_save.png" width=850/>
9. Click on the bread-crumb link to go back to your **GitHub Organization** Jenkins Pipeline project folder
10. When the scan is complete your **GitHub Organization** Jenkins Pipeline project should be **empty**!<p><img src="img/intro/org_folder_empty.png" width=850/>
11. However, when the project was created it also should have created a webhook in Github. Verify that the webhook was created in Github by checking the **Webhooks** within your GitHub Organization **Settings**. <p><img src="img/intro/org_folder_webhook.png" width=850/>
12. The reason why the scan did not find any repositories is because there were no branches in any repository with a `Jenkinsfile` in it, so let's fix that. Navigate to your forked copy of the **helloworld-nodejs** repository and click on the **Create new  file** button towards the top right of the screen. <p><img src="img/intro/org_folder_create_file.png" width=850/>
13. Name the file `Jenkinsfile` and add the following content:
```
pipeline {

}
```
14. At the bottom of the screen enter a commit message ***initial Jenkinsfile***, select the **Create a new branch for this commit and start a pull request**, name the branch **development** and click the **Propose new file** button. **IMPORTANT Do Not Create a Pull Request on the next screen after saving** <p><img src="img/intro/org_folder_jenkinsfile_commit.png" width=850/> 
15. Navigate back to your new **GitHub Organization** Jenkins Pipeline project folder on your Team Master and refresh your browser. If you do not have a new **helloworld-nodejs** Multibranch project then click on the **Scan Organization Now** link in the left menu and then refresh your browser. <p><img src="img/intro/org_folder_scan.png" width=850/>
16. You should have a new failed job based on the **development** branch you just added the `Jenkinsfile`. <p><img src="img/intro/org_folder_new_job_failed.png" width=850/>

## Basic Declarative Syntax Structure

In the previous lesson your Pipeline ran and will have failed.

In this exercise we will update the Jenkinsfile Declarative Pipeline in your forked copy of the **helloworld-nodejs** repository using the GitHub file editor so that it will run successfully, as opposed to resulting in the following syntax errors:

```
WorkflowScript: 1: Missing required section "stages" @ line 1, column 1.
   pipeline {
   ^

WorkflowScript: 1: Missing required section "agent" @ line 1, column 1.
   pipeline {
   ^

2 errors
```

[Declarative Pipelines](https://jenkins.io/doc/book/pipeline/syntax/#declarative-pipeline) must be enclosed within a `pipeline` block - which we have. But Declarative Pipelines must also contain a top-level `agent` declaration, and must contain exactly one `stages` block at the top level. The `stages` block must have at least one `stage` block but can have an unlimited number of additional `stage` blocks. Each `stage` block must have exactly one `steps` block. 

1. We will use the GitHub file editor to update the `Jenkinsfile` file in your forked **helloworld-nodejs** repository. Navigate to the `Jenkinsfile` file in the **development** branch of your forked repository and then click on the pencil icon in the upper right to edit that file. **IMPORTANT Make sure you are editing the `Jenkinsfile` on your development branch**<p><img src="img/intro/basic_snytax_edit_github.png" width=850/>
2. Replace the contents of that file with the following Declarative Pipeline:

```groovy
pipeline {
  agent any
  stages {
    stage('Say Hello') {
      steps {
        echo 'Hello World!'   
        sh 'java -version'
      }
    }
  }
}
```
<p><img src="img/intro/basic_snytax_commit_to_development.png" width=850/><p>

3. Add a commit description and then click the **Commit Changes** button with the default selection of *Commit directly to the `development` branch* selected.
4. Navigate back to the **helloworld-nodejs** *development* branch job on your Team Master and the job will complete successfully. Note some things from the log:
  
   i. The `Jenkinsfile` is being pulled from the **development** branch of your forked **helloworld-nodejs** repository.
   
   ii. The agent is being provisioned from a Kubernetes Pod Template (more on this in the next lesson):

  ```
  ...
  Agent default-jnlp-0p189 is provisioned from template Kubernetes Pod Template
  ...
  ```

   iii. Your fork of the **helloworld-nodejs** repository is being checked out, even though you did not put any steps in the `Jenkinsfile` to do so:

  ```
  ...
  Cloning repository https://github.com/cd-accel-beedemo/helloworld-nodejs.git
  ...
  ```

   iv. The agent has a Java version of `1.8.0_171`:

```
...
Running shell script
+ java -version
openjdk version "1.8.0_171"
...
```
  
> **NOTE:** You may have noticed that your Pipeline GitHub repository is being checked out even though you didn't specify that in your Jenkinsfile. Declarative Pipeline checks out source code by default without the need to explicitly include the `checkout scm` step. Furthermore, this automatic checkout will occur in every `stage` that uses a different agent.

## Kubernetes Agents with CloudBees Core

In this exercise we will get an introduction to the [Jenkins Kubernetes plugin](https://github.com/jenkinsci/kubernetes-plugin/blob/master/README.md) for running dynamic and ephemeral agents in a Kubernetes cluster - [leveraging the scaling abilities of Kubernetes to schedule build agents](https://kurtmadel.com/posts/cicd-with-kubernetes/autoscaling-jenkins-agents-with-kubernetes/).

CloudBees Core has OOTB support for Kubernetes build agents and allow Kubernetes agent templates - called Pod Templates - to be defined at either [the Operations Center level](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/agents/#_globally_editing_pod_templates_in_operations_center) or at [the Team Master level](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/agents/#_editing_pod_templates_per_team_using_masters). The Kubernetes based agent is contained in a [Kubernetes pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/), where a pod is a group of one or more containers sharing a common storage system and network. A pod is the smallest deployable unit of computing that Kubernetes can create and manage (you can read more about pods in the [Kubernetes documentation](https://kubernetes.io/docs/concepts/workloads/pods/pod/)).

>NOTE: One of the **containers** in a **Pod Template** must host the actual Jenkins build agent that communicates with the Jenkins Master (the `slave.jar` file that is used for communication between the CloudBees Team Master and the agent). By convention, this container always exists (and is automatically added to any Pod Templates that do not define a **Container Template** with the name ***jnlp*** ). Again, this special container has the ***Name*** `jnlp` and default execution of the Pipeline always happens in this `jnlp` container (as it did when we used `agent any` above) - unless you declare otherwise with a special Pipeline step provided by the Kuberentes plugin - [the `container` step](https://github.com/jenkinsci/kubernetes-plugin#pipeline-support). If needed, this automatically provided `jnlp` container may be overridden by specifying a **Container Template** with the ***Name*** `jnlp` - but that **Container Template** must be able to connect to the Team Master via JNLP with a version of the Jenkins `slave.jar` that corresponds to the Team Master Jenkins verion or the Pod Template will fail to connect to the Team Master. You can learn more about the `jnlp` container and additioal functionality in the [Jenkins Kubernetes Plugin documentation on GitHub](https://github.com/jenkinsci/kubernetes-plugin#pipeline-support).

We will use the Kubernetes plugin [Pipeline `container` block](https://jenkins.io/doc/pipeline/steps/kubernetes/#container-run-build-steps-in-a-container) to run Pipeline `steps` inside a specific container configured as part of a Jenkins Kubernetes Agent Pod template. In our initial Pipeline, we used `agent any` which required at least one Jenkins agent configured to *Use this node as much as possible* - resulting in the use of a Pod Template that only had a `jnlp` container. But now we want to use Node.js in our Pipeline. [Jenkins CasC](https://github.com/jenkinsci/configuration-as-code-plugin) was used to pre-configure the [CloudBees Kube Agent Management plugin](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/agents/#_editing_pod_templates_per_team_using_masters) to include a [Kubernetes Pod Template at the Team Master level to provide a Node.js container](https://github.com/kypseli/cb-core-mm/blob/kube-workshop/config-as-code.yml#L45). Go to the top-level of your Team Master, click on the **Manage Jenkins** link and then scroll down and click on the **Kubernetes Pod Templates** item. If not at the top, scroll down to the ***Kubernetes Pod Template*** with the **Name** **'nodejs-app'** <p><img src="img/intro/k8s_agent_nodejs_template.png" width=800/> <p>Take note of the ***Labels*** field with a value of ***nodejs-app*** and the **Container Template** ***Name*** field with a value of ***nodejs*** - both of these are important and we will need those values to configure our Pipeline to use this **Pod Template** and **Container Template** in your `Jenkinsfile`.

1. Navigate to and click on the `Jenkinsfile` file in the **development** branch of your forked **helloworld-nodejs** repository
2. Click on the **Edit this file** button (pencil)
3. First, we need to update the `agent any` directive with the following so that we will get the correct Kubernetes Pod Template - configured with the **Container Template** that includes the `node:8.12.0-alpine` Docker image:
```
  agent { label 'nodejs-app' }
```
4. Commit that change and navigate to the **Activity** view of your **helloworld-nodejs** job in Blue Ocean on your Team Master. The build logs should be almost the same as before - we are still using the default `jnlp` container. <p><img src="img/intro/k8s_agent_run_from_bo.png" width=800/> <p>
5. Let's change that by replacing the **Say Hello** `stage` with the following **Test** `stage` so the steps run in the **nodejs** `container`. Edit the `Jenkinsfile` file in the **development** branch of your forked **helloworld-nodejs** repository so the entire pipeline looks like the following:

```groovy
pipeline {
  agent { label 'nodejs-app' }
  stages {
    stage('Test') {
      steps {
        container('nodejs') {
          echo 'Hello World!'   
          sh 'java -version'
        }
      }
    }
  }
}
```

  All of the Pipeline steps within that `container` block will run in the container specified by the **Name** of the **Container Template** - and in this case that **Container Template** is using the `node:8.12.0-alpine` container image as we saw above. Commit the changes and the **helloworld-nodejs** job will run - it will result in an error because the `nodejs` container does not have Java installed (and why should it). <p><img src="img/intro/k8s_agent_java_error.png" width=800/>

>NOTE: If you waited for your job to complete in Blue Ocean before you navigated to the [Pipeline Runs Details View](https://jenkins.io/doc/book/blueocean/pipeline-run-details/#pipeline-run-details-view) you will discover a nice feature where if a particular step fails, the tab with the failed step will be automatically expanded, showing the console log for the failed step as you can see in the image above.

6. We will fix the error in the **Test** `stage` we added above by replacing the `sh 'java -version'` step with the following step and moving the `sh 'jave -version` step above the `container` block in the `Jenkinsfile` file in the **development** branch of your forked **helloworld-nodejs** repository so the entire pipeline looks like the following:
```
pipeline {
  agent { label 'nodejs-app' }
  stages {
    stage('Test') {
      steps {
        sh 'java -version'
        container('nodejs') {
          echo 'Hello World!'   
          sh 'node --version'
        }
      }
    }
  }
}
```
7. Commit the changes and the **helloworld-nodejs** job will run and it will complete successfully with the following output: <p><img src="img/intro/k8s_agent_success.png" width=800/>

>**NOTE:** If you were to add back the sh 'java -version' step before or after the `container('nodejs')` it would complete successfully as it would be using the default `jnlp` container to execute any steps not in a `container` block. Feel free to give it a try by adding back the `sh 'java -version'` step to the **Test** stage - ensuring that it is outside of the `container('nodejs')` block.

## The options Directive

The [`options` directive](https://jenkins.io/doc/book/pipeline/syntax/#options) allows configuring Pipeline-specific options from within the Pipeline itself. We are going to add the `buildDiscarder` option to limit the number of logs and artifacts retained, and the `skipDefaultCheckout` option to skip checking out code by default in every stage. We will add both to one global `options` directive after the `agent` directive in the `Jenkinsfile` file in the **development** branch of your forked **helloworld-nodejs** repository.

1. Use the GitHub file editor to update the `Jenkinsfile` file in the **development** branch of your forked **helloworld-nodejs** repository - adding the following `options` directive below the `agent` section:

```groovy 
pipeline {
  agent { label 'nodejs-app' }
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
  }
```

1. Next, we need to add an explicit checkout step - `checkout scm` to the **Test** stage, we don't want to do a checkout in any of the other stages but we do need a checkout in this `stage`:

```
    stage('Test') {
      agent { label 'nodejs-app' }
      steps {
        checkout scm
        container('nodejs') {
          echo 'Hello World!'   
          sh 'node --version'
        }
      }
    }
```
>NOTE: The `checkout scm` step is outside of the `container` block as there is no guarantee that the `nodejs` `container` has **git** installed.
3. **Commit Changes** and then navigate to the **development** branch of your **helloworld-nodejs** job in the classic UI on your **Team Master** and once the job has run at least once, the job configuation will be updated to reflect what was added to the Pipeline script. <p><img src="img/intro/options_build_discard.png" width=850/>

> **NOTE:** A Pipeline job must run in Jenkins before any type of Pipeline directive that modifies the job configuration can take effect because there is no way for the Jenkins Master to know about it until it runs on the Jenkins Master. Also, note that for Multibranch Pipeline projects - the only way to modify much of the configuration of the managed branch specific Pipeline jobs is by doing it in the Pipeline Jenkinsfile/script as those jobs are not directly configurable from the Jenkins UI.

## Conditional Execution with `when`

In this exercise we will edit the `Jenkinsfile` file in your forked **helloworld-nodejs** repository with conditional execution using the [`when` directive](https://jenkins.io/doc/book/pipeline/syntax/#when). We will accomplish this by adding a branch specific `stage` to the `Jenkinsfile` in your forked **helloworld-nodejs** repository.

1. Navigate to and open the GitHub editor for the `Jenkinsfile` file in **development** branch of your forked **helloworld-nodejs** repository
2. Insert the ***Build and Push Image*** stage after the existing **Test** stage and note the `beforeAgent true` option - this setting will result in the `when` condition being evaluated before acquiring and entering an `agent` for the `stage` so the entire `pipeline` matches the following. The `branch` condition is a built-in condition that allows executing stages only for specific branches - in this case the ***Build and Push Image*** `stage` will only execute for the **master** branch. The entire Pipeline shoud match what is below:

```
pipeline {
  agent { label 'nodejs-app' }
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
  }
  stages {
    stage('Test') {
      steps {
        checkout scm
        container('nodejs') {
          echo 'Hello World!'   
          sh 'node --version'
        }
      }
    }
    stage('Build and Push Image') {
      when {
         beforeAgent true
         branch 'master'
      }
      steps {
         echo "TODO - build and push image"
      }
    }
  }
}
```
3. Commit the changes and then navigate to the **helloworld-nodejs** job in Blue Ocean on your Team Master and the job for the **development** branch should be running or queued to run. Note that the ***Build and Push Image*** `stage` was skipped. <p><img src="img/intro/conditional_skipped_stage.png" width=800/>
4. Now we will create a [Pull Request](https://help.github.com/en/articles/creating-a-pull-request) between the **development** branch and **master** branch of your forked **helloworld-nodejs** repository. Navigate to your forked **helloworld-nodejs** repository in GitHub - click on the **New pull request** button <p><img src="img/intro/conditional_new_pull_request.png" width=800/>
5. Changed the **base repository** to the **master** branch of your forked **helloworld-nodejs** repository (not the **cloudbees-days** repository), add a comment and then click the **Create pull request** button <p><img src="img/intro/conditional_create_pull_request.png" width=800/>
6. A job will be created for the pull request and once it has completed successfully your pull request will show that **All checks have passed**. Go ahead and click the **Merge pull request** button and then click the **Confirm merge** button but DO NOT delete the **development** branch <p><img src="img/intro/conditional_merge_pull_request.png" width=800/>
7. Navigate to the **helloworld-nodejs** job in Blue Ocean on your Team Master and the job for the **master** branch should be running or queued to run. Click on the run and after it has completed notice that the ***Build and Push Image*** stage was not skipped <p><img src="img/intro/conditional_not_skipped.png" width=800/>

## Next Lesson

Before moving on to the next lesson you can make sure that your **Jenkinsfile** Pipeline script on the **master** branch of your forked **helloworld-nodejs** repository matches the one from [below](#finished-jenkinsfile-for-introduction-to-pipelines-with-cloudbees-core).

You may proceed to the next set of exercises - **[Stage Specific Agents, Inline Pipeline Pod Temaplates and Cross Team Collaboration](./cross-team-collaboration.md)** - when your instructor tells you.

### Finished Jenkinsfile for *Introduction to Pipelines with CloudBees Core*
```
pipeline {
  agent { label 'nodejs-app' }
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
  }
  stages {
    stage('Test') {
      steps {
        checkout scm
        container('nodejs') {
          echo 'Hello World!'   
          sh 'node --version'
        }
      }
    }
    stage('Build and Push Image') {
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        echo "TODO - build and push image"
      }
    }
  }
}
```