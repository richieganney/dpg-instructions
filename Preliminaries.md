# Preliminary Instructions

## 1) Have a GitHub account

If you don't have a GitHub account already, head over to https://github.com and create one for free!

## 2) Have a Terraform Cloud account

If youy don't have a Terraform Cloud account already, head over to https://app.terraform.io and create one for free!

You will need to create an **Organization** within Terraform Cloud, but this can be a personal one (e.g. same name as your username, if available).

## 3) Connect Terraform Cloud to your GitHub account

Usually right after you create your Organization in Terraform Cloud, Terraform will redirect you to the page to **Create a new Workspace**.  
If this is not the case, select your Organization from the top-left drop-down menu, then click the button **+ New workspace** in the top-right corner.

Under **Connect to a version control provider**, select **GitHub ˅**, then click on **GitHub.com**.

⚠️ You may need to allow pop-ups / disable pop-up blockers for this step, and manually allow blocked pop-ups to display.

Follow instructions in the pop-up to install Terraform Cloud in your GitHub account, and grant it access to your GitHub account.  
(If you are not happy to grant Terraform Cloud access to all of your GitHub repos, you can come back to this step after you have forked the sample Terraform project repo, and grant Terraform Cloud access to that repo only.)
