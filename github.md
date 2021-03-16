# GitHub Guide

If you have managed servers at Exonet then you have (or can get) read-only access to a repository with the Ansible playbooks for these servers. You also have the option to make changes to these playbooks which (after approval from us) will be deployed to the server. Since you are not able to directly commit to the repository you will need to fork our repository to your own GitHub account, make a branch and create a pull request to make any changes. The following is a basic guide on how to do this. 

First download and install the [GitHub Desktop client](https://desktop.github.com/). You can also use a different git client but for the purpose of this guide we are assuming you are using this client.

### Fork

Go to the GitHub URL that you received from us in your browser. If you haven't received an URL yet or don't have access to a repository yet please open a ticket with our support.

Create a fork of our repository on your personal GitHub account by clicking on the `Fork` button.

### Clone

In the GitHub Desktop client make a local clone of the repository by going to `File → Clone Repository`. Select your fork of our repository, a local path to save it and click on `Clone`. You will be asked how you are planning to use this fork. Choose `To contribute to the parent project` and click on `Continue`.

Now the files in the repository are available on your local filesystem.

### Branch

Ensure you are on the master branch of your fork and click on `Fetch origin`. This will pull in all our latest changes to your local clone of the repository. Afterwards you should create a new branch so that the master branch can always pull in the lastest changes in the future. Go to `Branch → New Branch` in the menu. Enter a short descriptive name of the changes you want to make (for example: `firewall`).

### Editor

Open the files on your local filesystem in your favorite editor. Make sure this editor has support for [EditorConfig](https://editorconfig.org/) and enable the EditorConfig plugin if needed. This plugin will automatically fix common style mistakes like indentation.

Now you can make the changes that are needed and save the files afterwards.

### Commit

Back in the GitHub Desktop client you will now see an overview of the changes that you made. Make sure the branch you created earlier is still selected.

You can now commit these changes to the branch you created. Enter a summary of the changes (for example: `Add new address to firewall`) and click on `Commit to branchname`.

### Pull request

Now we are able to publish this branch and create a Pull Request with these changes. Click on `Publish branch` in the GitHub Desktop client. Next go to `Branch → Create Pull Request` in the menu.

This will open the pull request form on GitHub in your browser. Make sure the base fork contains exonet instead of your own GitHub username and optionally enter a comment. Then click on `Create pull request`.

### Approval

That's it! You've now made your first Pull Request. Our engineers will now review your changes and deploy them on the server after our approval (we use a comment with a :rocket: to start the deployment) or we will request that you make changes if something is not according to our standards.
