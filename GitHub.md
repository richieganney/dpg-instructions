# GitHub Instructions

## 1) **GitHub:** Clone provided repository

In this section, you are going to create new repository which contains the (pre-written) Terraform code to provision our web app.

The easiest way to achieve this is to fork the following GitHub repository onto your GitHub account

1. Navigate to the existing repository: https://github.com/JeffHemmen/dpg-init.

1. Click on the **Fork** button (top right). (You may need to then select your own GitHub username.)

You now have your own copy of our sample Terraform project in your GitHub account. This currently has only one branch, `master`. We will set up more branches soon.

## 2) **Terraform Cloud:** Create New Workspace (prod)

We are going to start by creating a *production* workspace, which is going to deploy our production infrastructure and webapp.  
This can be thought of as the 'live' environment, or the one your end-customers will see (as opposed to ones used for testing and QA).  
In our case, the production environment corresponds to the 'master' branch.

1. Click on **New Workspace** in your Organization

ℹ️ If you haven't connected Terraform Cloud to your GitHub account yet, you will need to do so now.

2. Select **GitHub** as your version control provider

1. Select the repository you forked (probably `dpg-init`).  
(You may need to select your own GitHub username in the drop-down list first.)

1. Name the Workspace `2048-prod`.

1. Click **Create workspace**.

## 3) **Terraform Cloud:** Configure your Workspace (prod)

1. Wait for the Terraform configuration to be loaded from GitHub.  
(This may be instant, or take a few seconds.)

2. Click the purple **Configure variables** button, or the link **Variables** in the navigation bar, top-right.

!!!! sensitive, key/value

3. Set the following **Terraform Variables**:
  - `animal` to the animal you have been assigned for tonight's playground;
  - `env` to `prod`.


4. Set the following **Environment Variables**:
  - `AWS_ACCESS_KEY_ID` as TBC
  - `AWS_SECRET_ACCESS_KEY` as TBC


5. In the navigation bar, top-right, click **Settings ˅**, then **General**.

6. As **Apply Method**, set **Auto apply**.

!!!! save settings

## 4) **Terraform Cloud:** Deploying your project (prod)

1. In the top-right corner, click **Queue plan**.

1. As your reason for queueing plan, enter `Initial deployment` (this is optional).

1. Click the purple **Queue plan** button.

You can now see Terraform Cloud run a `terraform plan`, where it reads our project configuration from GitHub and the variables we have set, maps out its dependency tree, and installs the necessary providers and modules.  
After this is done, it displays a list of resources it plans on creating, and should end its output with the following summary:
```
Plan: 4 to add, 0 to change, 0 to destroy.
```

After Terraform Cloud is done with the Plan phase, it automatically runs a `terraform apply`, where it takes the plan generated above, and makes all the API calls (to the AWS API) which effect our resources being created.  
Note that, had we not selected 'Auto apply' in step **3) 6.** above, Terraform Cloud would have asked us to manually confirm the Plan before proceeding to the Apply phase.)

At the end of the Apply phase, Terraform prints any Outputs defined in our project. Here we have one output, `url`, the URL under which we can reach our webapp.  
You can copy-paste the URL into a new tab in your browser and see the webapp in action!

(You can expand and collapse the different phases in this view. You may need to expand the Apply phase by clicking on **Apply finished**.)

ℹ️ You may also need to wait up to a minute for the DNS record to become available after your apply finished.

## 5) **GitHub:** Create a *staging* branch

Go back to GitHub.com and go to the repository you have forked before.

1. In your forked GitHub repo, click the grey **Branch: master** button (left, above the list of files).

1. Enter `staging`.

1. Click **Create branch: staging from 'master'**

You now have a branch called 'staging'. This is commonly used to host code we consider ready to be deployed, but haven't tested to the desired level of confidence yet.

In the next few steps, we will set up a *staging environment*, which we can use to test the code in our staging branch.

## 6) **Terraform Cloud:** Create New Workspace (staging)

A different environment will typically correspond to a new *Workspace* in Terraform Cloud.

1. Click on **New Workspace** in your Organization

1. Select **GitHub** as your version control provider

1. Select the repository you forked (probably `dpg-init`).  
(You may need to select your own GitHub username in the drop-down list first.)

1. Name the Workspace `2048-staging`.

1. Expand the **˅ Advanced options** menu.  
⚠️ This is an extra step, compared to the 'prod' workspace above.

1. Set **VCS branch** to `staging`. This is the branch we created under step **5)** above.  
⚠️ This is an extra step, compared to the 'prod' workspace above.

1. Click **Create workspace**.

## 7) **Terraform Cloud:** Configure your Workspace (staging)

1. Wait for the Terraform configuration to be loaded from GitHub.  
(This may be instant, or take a few seconds.)

2. Click the purple **Configure variables** button, or the link **Variables** in the navigation bar, top-right.

3. Set the following **Terraform Variables**:
  - `animal` to the animal you have been assigned for tonight's playground;
  - `env` to `staging`.


4. Set the following **Environment Variables**:
  - `AWS_ACCESS_KEY_ID` as TBC
  - `AWS_SECRET_ACCESS_KEY` as TBC


5. In the navigation bar, top-right, click **Settings ˅**, then **General**.

6. As **Apply Method**, set **Auto apply**.

## 8) **Terraform Cloud:** Deploying your project (staging)

1. In the top-right corner, click **Queue plan**.

1. As your reason for queueing plan, enter `Initial deployment` (this is optional).

1. Click the purple **Queue plan** button.

Same as for production above, Terraform will output the URL under which we can reach our webapp. Make a note of this, as we will need it for step **9)** below.

## 9) **GitHub:** Create a *dev* branch

1. In your forked GitHub repo, click the grey **Branch: staging** button (left, above the list of files).

1. Enter `dev`.

1. Click **Create branch: dev from 'staging'**

You now have a branch called 'dev'. This is commonly used to host code that is not yet ready to be deployed, but is using by developers to work on new features or fixes.

Though one might want to depending on the situation, we will not set up a staging environment.

## 10) **GitHub:** Set up a GitHub Action

1. In your forked GitHub repo, click the grey **Branch: dev** button (left, above the list of files).

1. Click **master** to switch to the master branch.

1. Click the grey **Create new file** button, to the right.

1. Type `.github/workflows/prod-PR.yml` where it says 'Name your file…'.  
Note how the text field changes to display the path whenever you enter a slash character.

1. Enter the following as the file content, replacing `<staging url>` with the URL for your staging environment (cf. step **8)**):

```
name: prod-PR

on:
  pull_request:
    branches:
      - master

jobs:
  staging-healthcheck:
    runs-on: ubuntu-latest
    steps:
      - name: Healthcheck the staging endpoint
        run: curl <staging url>


```

6. Click the green **Commit new file** button.  
Leave 'Commit directly to the `master` branch' selected. (It is generally not best practice to commit directly to master, but acceptable in an initial setup phase such as this.)

We have now set up a GitHub Action which will be triggered whenever a pull request into the *master branch* is opened.  
The only job defined, 'staging-healthcheck', will simply check if the staging environment is reachable. If it is not, that means that the production environment likely won't be reachable either once this new code is deployed, and thus GitHub will veto this code merge.
