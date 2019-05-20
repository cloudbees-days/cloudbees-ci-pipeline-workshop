# Stage Specific Agents

In this exercise we will get an introduction to the [Jenkins Kubernetes plugin](https://github.com/jenkinsci/kubernetes-plugin/blob/master/README.md) for running dynamic and ephemeral agents in a Kubernetes cluster - [leveraging the scaling abilities of Kubernetes to schedule build agents](https://kurtmadel.com/posts/cicd-with-kubernetes/autoscaling-jenkins-agents-with-kubernetes/).

CloudBees Core has OOTB support for Kubernetes build agents and allow Kubernetes agent templates - called Pod Templates - to be defined at either [the Operations Center level](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/agents/#_globally_editing_pod_templates_in_operations_center) or at [the Team Master level](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/agents/#_editing_pod_templates_per_team_using_masters). The Kubernetes based agent is contained in a [Kubernetes pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/), where a pod is a group of one or more containers sharing a common storage system and network. A pod is the smallest deployable unit of computing that Kubernetes can create and manage (you can read more about pods in the [Kubernetes documentation](https://kubernetes.io/docs/concepts/workloads/pods/pod/)).

>NOTE: One of the **containers** in a **Pod Template** must host the actual Jenkins build agent that communicates with the Jenkins Master (the `agent.jar` file that is used for communication between the CloudBees Team Master and the agent). By convention, this container always exists (and is automatically added to any Pod Templates that do not define a **Container Template** with the name ***jnlp*** ). Again, this special container has the ***Name*** `jnlp` and default execution of the Pipeline always happens in this `jnlp` container (as it did when we used `agent any` above) - unless you declare otherwise with a special Pipeline step provided by the Kuberentes plugin - [the `container` step](https://github.com/jenkinsci/kubernetes-plugin#pipeline-support). If needed, this automatically provided `jnlp` container may be overridden by specifying a **Container Template** with the ***Name*** `jnlp` - but that **Container Template** must be able to connect to the Team Master via JNLP with a version of the Jenkins `agent.jar` that corresponds to the Team Master Jenkins verion or the Pod Template will fail to connect to the Team Master. You can learn more about the `jnlp` container and additioal functionality in the [Jenkins Kubernetes Plugin documentation on GitHub](https://github.com/jenkinsci/kubernetes-plugin#pipeline-support).

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

6. We will fix the error in the **Test** `stage` we added above by replacing the `sh 'java -version'` step with the `sh 'node --version'` step and moving the `sh 'jave -version` step above the `container` block in the `Jenkinsfile` file in the **development** branch of your forked **helloworld-nodejs** repository so the entire pipeline looks like the following:
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

>**NOTE:** The sh 'java -version' step before the `container('nodejs')` completed successfully this time because it used the default `jnlp` container to execute any steps not in the `container` block.

### Finished Jenkinsfile for *Stage Specific Agents*
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

You may proceed to the next lab [*Lab 3. Using the options directive*](./options-directive.md) or head back to the main list of the [**labs**](./README.md#workshop-labs) when you are ready.
