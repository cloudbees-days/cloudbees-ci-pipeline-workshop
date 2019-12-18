# Workshop Setup

## CloudBees Core Team Master Set-up
Setup a work environment for the lessons provided in this workshop.  Ask the instructor for the URL of the server you will be using during the workshop.

Today's URL for the CloudBees Core environment is https://workshop.cb-sa.io/cjoc/

The URL for the GitHub Repository with these instructions and the exercises is **https://github.com/cloudbees-days/cloudbees-core-workshop**

### Create a Jenkins Account

1. Goto to the Workshop URL provided by the instructor;
2. Click on the **create an account** link in the middle above the **Username** input.<p><img src="img/setup/setup-create-an-account.png" width=350/>
3. Complete the **Create an account!** form (all fields are required) and click the **Create account** button;<p><img src="img/setup/setup-create-account.png" width=350/>
4. You should see a **Success** page - click on **the top page** link;<p><img src="img/setup/setup-create-account-success.png" width=550/>

### Create a Team Master

Next, everyone will get their own Jenkins masters referred to as a Team Master.

1. If not in CloudBees Team UI, click on the **Teams** link in the left menu; <p><img src="img/setup/setup-classic-ui-Teams-link.png" width=400/>
2. Click on the **Create team** button in the center of your screen;<p><img src="img/setup/setup-create-a-team.png" width=400/>
3. **Name this team** - enter a name for your team - **IMPORTANT: to ensure uniqueness, use your GitHub username** and then click **Next**;<p><img src="img/setup/setup-name-this-team.png" width=450/>
4. **Choose an icon for this team** to help uniquely identify your team - select an icon and color for your team and then click **Next**;
5. **Add people to this team** - your user will show up as a **Team Admin** and we won't be adding any additional users right now, but feel free to look around and then click **Next**;
6. **Select team master creation recipe** - click on the drop-down to see the options, but select the top option: **Workshop Default** recipe;<p><img src="img/setup/setup-create-team-recipe-2.png" width=450/>
7. Finally, click the **Create team** button. <p><img src="img/setup/setup-create-team.png" width=450/>
8. While your master is being  created (**it takes anywhere from 2-3 minutes to provision your Team Master**), move onto the next section **Create a GitHub.com user account**

## Create a GitHub.com user account
Setup a GitHub.com user account that will be used later in this workshop. If you have an existing GitHub.com account you will be able to use it if you are comfortable using that account to create a GitHub Organization later in the workshop.

1. Visit https://github.com/join and fill in the required fields to create a user account.
2. Select "Unlimited public repositories for free" when choosing your plan.
3. Verify your email account to ensure you account is activated.  An activated account will be **required** in the next few exercises.

## Create a GitHub Personal Access Token
The following instructions cover how to create a Github Personal Access Token that you will use within Jenkins to connect Pipelines, Multibranch Pipelines, and Github Organization Projects to your Github repositories.

1. Click on [this link to automatically select the required **Personal access token settings**](https://github.com/settings/tokens/new?scopes=repo,read:user,user:email,admin:repo_hook,admin:org_hook)
2. Click on **Generate Token**
3. As the success message says: **Make sure to copy your new personal access token now. You won’t be able to see it again!**  

## Create a GitHub Organization

Create a Github organization to use for this workshop:

1. On Github navigate to **Organizations**: https://github.com/settings/organizations (after logging in) 
2. Click on **New Organization** <p><img src="img/setup/setup-github-new-org.png" width=550/>
3. Fill in the **Organization Name**, **Billing Email**, and click on **Create Organization**<p><img src="img/setup/setup-create-org.png" width=550/>
4. On the **Invite organization members** - just click the **Continue** button. On the next page **Enter Organization Details** either click **Submit** button or **skip this step** to finish creating the organization.

>NOTE: Even though you have to provide an email for billing, you will not be charged anything as long as you choose the free option.

## Fork the Workshop Repository

The workshop utilizes the **helloworld-nodejs** repository from the [CloudBees Days GitHub Organization](https://github.com/cloudbees-days). Fork the **helloworld-nodejs** repository into the GitHub Organization that you created above (if you are not sure how to fork a repository - see this [GitHub Guide on forking](https://guides.github.com/activities/forking/)):

* https://github.com/cloudbees-days/helloworld-nodejs

<p><img src="img/setup/setup-fork-repo.png" width=600/>

## Finished Setup
You should see the following **Create a new Pipeline** screen for your Team:
<p><img src="img/setup/setup-success.png" width=600/>

You may proceed to the first lab [*Lab 1 - Introduction to Declarative Pipelines with CloudBees Core*](./declarative-basics.md) or head back to the main list of the [**labs**](./README.md#workshop-labs) when you are ready.
