# Pipeline Catalog Templates

Pipeline Catalog Templates provide version controlled paramaterized templates for Multibranch and stand-alone Pipeline jobs. In this exercise we will use a [Pipeline Catalog Template](https://github.com/cloudbees-days/pipeline-template-catalog/tree/master/templates/nodejs-app) to create another Multibranch Pipeline project for your forked **helloworld-nodejs** repository. All you will need to do is fill in a few simple parameters and you will end up with a complete end-to-end CI/CD Pipeline for the **helloworld-nodejs** application.

## Create a Multibranch Project from a Pipeline Catalog Template
[Jenkins Configurations as Code](https://wiki.jenkins.io/display/JENKINS/Configuration+as+Code+Plugin) (JCasC) was used to [pre-configure a Pipeline Catalog on everyone's Team Masters](https://github.com/kypseli/cb-core-mm/blob/kube-workshop/quickstart/init_07_pipeline_template_global_catalog.groovy) and there is a **template-jobs** folder in your Team Master named folder that has been configured (using the [CloudBees Folders Plus folder item filtering](https://go.cloudbees.com/docs/plugins/folders-plus/#folders-plus-sect-restrict)) to only allow the creation of jobs from that Catalog of templates. Now we will create a templatized end-to-end CI/CD Pipeline Multibranch project for your forked copy of the **helloworld-nodejs** repository.

1. On your Team Master navigate to the **template-jobs** folder
2. Click on the ***New Node.js App Multibranch Pipeline*** link in the left menu <p><img src="img/catalog/template_link.png" width=800/>
3. Enter an item name of your **[GitHub username]-hello**, select **Node.js App Multibranch Pipeline**  and click the **OK** button<p><img src="img/catalog/item_form.png" width=800/>
4. Fill out the template parameters:
   1. **Repository Owner**: the GitHub Organization your created for the CloudBees Core workshop
   2. **GitHub Credential ID**: select the *username/password* credential you created for the the CloudBees Core workshop - it will show up as - [GitHub username]/******
   3. Click the **Save** button<p><img src="img/catalog/template_parameters.png" width=800/>
5. You may need to refresh the Multibranch job screen, but eventually you should have two jobs - one for the **development** branch and one for the **master** branch<p><img src="img/catalog/two_jobs.png" width=800/>

You may proceed to the next set of [**exercises**](./README.md#excercises) - when your are you ready with this excercise.
