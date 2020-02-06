# Testing our Deployment Pipeline

## 1) Syntax error

In this section, we will introduce a syntax error into our Terraform project (on the 'dev' branch), and try to merge this into Staging (via a 'Pull Request').

Terraform Cloud should automatically check if this new code could be deployed to the staging environment, and let GitHub know if there is an issue. Since we're introducing a syntax error, we expect there to be an issue, and this Pull Request to be "unable to merge".

1. In GitHub, open your forked repository, and make sure you're on the 'dev' branch.
  - If you're not on the 'dev' branch, click the grey **Branch: master ˅** or **Branch: staging ˅** button, then select **dev**.

1. Click on any of the \*.tf files (e.g. `instances.tf`)

1. Click the ✏️ **pencil icon**, to the right of the buttons laballed **Raw**, **Blame**, **History**.

1. At the end of the file, add a line that says: `kill_all_humans()`

This syntax/command is not yet supported in Terraform v0.12, and thus will cause an error during the `terraform plan` stage.

5. Click the green **Commit changes** button at the bottom of the page.

1. Click on **Pull requests** in the navigation bar at the top.

1. Click either the green **Compare & pull request** button or the green **New pull request** button.

1. Set:
  - **base repository:** (if shown) to _your_ (forked) repo (e.g. `<your username>/dpg-init`)
  - **head repository:** (if shown) to _your_ (forked) repo (e.g. `<your username>/dpg-init`)
  - **base:** to `staging`
  - **compare:** to `dev`.

1. (Optional) Give your PR a name, and add a description.

1. Click the green **Create pull request** button.

1. Click the green **Create pull request** button again, if necessary.


Now that we have a Pull Request, terraform Cloud will jump into action and run a `terraform plan` to see if the new code could be deployed.  
This is known as a "speculative plan", and cannot be found through the Terraform Cloud UI, but only by following the link in GitHub. (This often leads to confusion among new users.)

In the "Conversation thread" on the Pull Request page, GitHub will say **Checking for ability to merge automatically** while Terraform Cloud is running the Plan. After the Plan has failed, this message will change to **All checks have failed**.

We can see details about the checks below this message. (Note that "atlas" is the legacy name of Terraform Cloud.)  
To the right of the line that says "Terraform plan errored", you can click on **Details** to see the _speculative plan_.

## 2) Run-time error

In this section, we will introduce an error which does not violate the Terraform syntax, but which will cause issues at run-time (i.e. once the code has been deployed).

Terraform Cloud will again automatically check if this new code could be deployed to the staging environment, and let GitHub know if there is an issue. With this error, Terraform Cloud cannot know that there is a problem
