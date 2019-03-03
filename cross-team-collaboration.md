# Stage Specific Agents, Inline Pipeline Pod Temaplates and Cross Team Collaboration

In this exercise we will explore stage specific agents, a more advanced usage of the Kubernetes plugin by defining a Pod Template inline in our Pipeline Jenkinsfile and will demonstrate [CloudBee's Core Cross Team Collaboration feature](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/cross-team-collaboration/).

## Stage Specific Agents and Agent None

Up to this point we have had only one global `agent` defined and it is being used by all `stages` of our `pipeline`. However, we don't need an `agent` for the ***Build and Push Image*** `stage`. We will update the Pipeline to have **no** global `agent` and using the current global `nodejs-app` `agent` just for the Test `stage`.

1. Open the GitHub editor for the `Jenkinsfile` file in the **master** branch of your forked **helloworld-nodejs** repository.
2. Replace the global `agent` section with the following:
```
  agent none
```

3. Next, in the **Test** `stage` add the following `agent` section right above the `steps` section:
```
    agent { label 'nodejs-app' }
```
4. You may be asking yourself how the `steps` are able to run in the `stages` where there is no `agent`. Every Pipeline script runs on the Jenkins Master using a **flyweight executor** (i.e. Java thread). However, certain Pipeline `steps` require a heavyweight executor - that is an executor on an `agent` ([more info on flyweight vs heavyweight executors](https://support.cloudbees.com/hc/en-us/articles/360012808951-Pipeline-Difference-between-flyweight-and-heavyweight-Executors)). One such step is the `sh` step. We will add such a step to the ***Build and Push Image*** `stage` to illustrate this. Add an `sh` step to the ***Build and Push Image*** `stage` after the `echo` step so the stage looks like the following:
```
    stage('Build and Push Image') {
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        echo "TODO - build and push image"
        sh 'java -version'
      }
    }
```
5. Commit the changes and navigate to the **helloworld-nodejs** job in Blue Ocean on your Team Master and the job for the **master** branch should be running or queued to run. The job will fail with the following error: <p><img src="img/cross-team/stage_agent_master_fail.png" width=800/>
6. Open the GitHub editor for the `Jenkinsfile` file in the **master** branch of your forked helloworld-nodejs repository
7. Remove the `sh 'java -version'` step from the ***Build and Push Image*** `stage` and commit the changes to the **master** branch.
8. The commit will trigger the **helloworld-nodejs** **master** branch job again and it will complete successfully.

## Kubernetes Pod Templates Defined in Pipeline Script

In this exercise we will add another Docker **container** for executing tests. We also want to use a different vesion of the **node** Docker image than the one provided by the CJOC *Kubernetes Shared Cloud* which is `node:8.12.0-alpine`. So far we have been using the **nodejs-app** [Kubernetes *Pod Template* defined for us at our Team Master level](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/agents/#kubernete-agents). In order to be able to control what `containers` and what Docker `image` version we use in our Pipeline we will update the **Jenkinsfile** Pipeline script with an [*inline* Kubernetes Pod Template definition](https://github.com/jenkinsci/kubernetes-plugin#declarative-pipeline).

1. Open the GitHub editor for the **Jenkinsfile** Pipeline script in the **master** branch of your forked **helloworld-nodejs** repository.
2. Replace the `agent` section of the **Test** `stage` with the following:

```
      agent {
        kubernetes {
          label 'nodejs-app-inline'
          yamlFile 'nodejs-pod.yaml'
        }
      }
```
3. The [Kubernetes plugin allows you to use standard Kubernetes Pod yaml configuration](https://github.com/jenkinsci/kubernetes-plugin#using-yaml-to-define-pod-templates) to define Pod Templates directly in your Pipeline script.
4. Note the `yamlFile` parameter. The Jenkins Kubernetes Plugin allows you to define Pod Templates using the Kuberenetes specificaiton for the yaml representation of a Pod. The `yamlFile` parameter value is a repository relative path to a yaml file representing the Pod spec you want to use as an agent Pod Template. So we must create the `nodejs-pod.yaml` file in the **master** branch of your forked **helloworld-nodejs** repository. At the top-level of your forked copy of the **helloworld-nodejs** repository click on the **Create new  file** button towards the top right of the screen. 
5. Name the file `nodejs-pod.yaml` and add the following content:
```
kind: Pod
metadata:
  name: nodejs-app
spec:
  containers:
  - name: nodejs
    image: node:10.10.1-alpine
    command:
    - cat
    tty: true
  - name: testcafe
    image: 946759952272.dkr.ecr.us-east-1.amazonaws.com/kypseli/testcafe:alpha-1
    command:
    - cat
    tty: true
```
6. At the bottom of the screen enter a commit message, leave ***Commit directly to the master branch** selected and click the **Commit new file** button<p>
7. Commit the changes and then navigate to the **master** branch of your **helloworld-nodejs** job in Blue Ocean on your Team Master. The job will queue indefinitely, but why? 
8. The answer is provided by the [CloudBees Kube Agent Management plugin](https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/agents/#monitoring-kubernetes-agents). Exit to the classic UI on your Team Master and navigate up to the **helloworld-nodejs** Multibranch folder. On the bottom left of of the screen there is a dedicated widget that provides information about the ongoing provisioning of Kubernetes agents. It also highlights failures, allowing you to determine the root cause of a provisioning failure. Click on the link for the failed or pending pod template. <p><img src="img/cross-team/pipeline_pod_template_failure.png" width=800/>
9. You will see that the **nodejs** container has an error - it looks like there is not a **node** Docker image available with that tag. If you go to [Docker Hub and look at the tags availalbe for the **node** image](https://hub.docker.com/r/library/node/tags/) you will see there is a **10.10.0-alpine** but not a **10.10.1-alpine** tag for the **node** image: <p><img src="img/cross-team/pipeline_pod_template_containers_error.png" width=800/> 
10. Abort the current run (or it will keep going forever) and open the GitHub editor for the **nodejs-pod.yaml** Pipeline script in the **master** branch of your forked **helloworld-nodejs** repository. Update the `image` for the **nodejs** `container` to be `node:10.10.0-alpine`.
11. Commit the changes and then navigate to the **master** branch of your **helloworld-nodejs** job in Blue Ocean on your Team Master. The job will run successfully. Also, note the output of the `sh 'node --version'` step - it is `v10.10.0` instead of `v8.12.0`: <p><img src="img/cross-team/pipeline_pod_template_node_version.png" width=850/>

## Cross-Team Master Events

We already have a job on the **beedemo-ops** Team Master that will publish an event. <p><img src="img/cross-team/cross_team_pub_event_pipeline.png" width=850/>

That event will be published **across Team Masters** via the CloudBees Operations Center event router. 

The Cross Team Collaboration feature has a configurable router for routing events and you will need to configure the Notification router on your Team Master before you will be able to receive the event published by the **beedemo-ops** Team Master.

1. Before the **hello-api** Pipeline's `hello-api-push-event` can trigger our **helloworld-nodejs** Pipeline job we must listen for the event. We do that by adding a `trigger` to your **Jenkinsfile** Pipeline script.
2. Open the GitHub editor for the **Jenkinsfile** file in the **master** branch of your forked **helloworld-nodejs** repository.
3. Add the following `trigger` block just above the top-level `stages` block:

```groovy
  triggers {
    eventTrigger simpleMatch('hello-api-push-event')
  }
```

4. Commit the changes and then navigate to the **master** branch of your **helloworld-nodejs** job in Blue Ocean on your Team Master. 

>**NOTE:**After first adding a new `trigger` you must run the job at least once so that the `trigger` is saved to the Jenkins job configuration (similar to what was necessary for the `buildDiscarder` and `preserveStashes` `options` earlier). 

<p><img src="img/cross-team/cross_team_trigger_configured.png" width=850/>

5. Now I will run the **hello-api** job and everyone should see the **master** branch of their **helloworld-nodejs** job triggered. <p><img src="img/cross-team/cross_team_triggered_by_event.png" width=850/>

After you have completed the above exercises, you can make sure that your **Jenkinsfile** Pipeline script is correct by comparing to or copying from [below]().

Please check out the [CloudBees Core for K8s CD/Jenkins X Workshop](https://github.com/cloudbees-days/jenkins-x-workshop) and the [CloudBees DevOptics Workshop](https://github.com/cloudbees-days/devoptics-workshop).

### Finished Jenkinsfile for *Pipeline Pod Temaplates and Cross Team Collaboration*
```

```
