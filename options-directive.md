# The options Directive

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

2. Next, we need to add an explicit checkout step - `checkout scm` to the **Test** stage, we don't want to do a checkout in any of the other stages but we do need a checkout in this `stage`:

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


### Finished Jenkinsfile for *The options Directive*
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
  }
}
```

You may head back to the related lab [*Lab 2. Stage specific agents*](./stage-specific-agents.md) or proceed to the main list of the [**labs**](./README.md#workshop-labs) if you like.
