# Approval Demo

This repository seeks to show a potential workflow for a group of organisations who have the requirement to keep repositories Private and then seek to inner-source these repositories at a point-in-time.

At the point in time for approval, reffered to a promoting in the code the setup of this demo shows a way that we can use GitHub Actions to automate switching these repositories through a centralised approval system/workflow represented by this repository.

Invoking an approval to promote a repository can be achieved by raising an issue, although other mechanisms could be used to kick off the approval cycle. In this example we use a Issue Form to collect information about the repository to be promoted from private to internal. Data collected in this example is:
* Organisation
* Repository
* Justification.

In this example we are allowing any organisation to potentially allow its repositories to be promoted. This requires some work on the organisations owner to install a GitHub app which will allow security for the actions, therefore to simulate this offline workflow the organisation is in a list in the form only listing those who have gone through these steps. The repositroy name is the repo to be switched in that organisation from `private` to `internal` and the Justification we collect in the form as an example of other datas we could collect, however in this sample we don't do anything with it (perhaps it could be used in the sign-off process).

## Setup
To setup the Approval Demo you will require a GitHub Application created so that you can request temporary credentials on behalf of the workflows that run against the organisations enlisting.

__Requirements:__
* Your own repo with these file cloned/copied in
* A new GitHub Application with the access scopes required for our workflows
* Secrets set for your newly created AppId its Private Key.
* The GitHub Application installed on the organisations that will be enlisiting to have the Approvals Demo promote repositories

### Clone/Copy the repo
Clone this repo into your own (in your own organisation)

### Creating a GitHub Application
You will need to have a GitHub Application that is scoped with the necessary permission required to run the scenario (In this example the `Repository:Administration` permission)

In your organisation you will need to create a GitHub Application. To do this you can follow the steps available at https://docs.github.com/en/developers/apps/creating-a-github-app

The important configuration details for the application are:
* `GitHub App name` a human readable application name that is unique within GitHub.com
* `Description` some details about your application and what you intend to use it for
* `Homepage URL` needs to be set to something as long as it is a URL
* `Expire user authorization tokens` should be checked so as to expire any tokens that are issued
* `Webhook` `Active` checkbox should be unchecked
* `Repository / Administration permissions` should be set to `Read/Write`. Details on the permission can be found at https://docs.github.com/en/rest/reference/permissions-required-for-github-apps#permission-on-administration
* `Where can this GitHub App be installed?` should be scoped to your desired audience (the current account, or any account). If you wish to promote repositories in organisations outside the current one where this App is created you will want to select `Any Account`

Once the application has been created you will be taken to the `General` settings page for the new application.
The GitHub Application will be issued an `App ID` which you can see in the `About` section, take note of this for later 
use in the Actions workflow.

On the `General` settings page for the application, at the bottom there is a `Private keys` section that you can use to 
generate a private key that can be utilized to authenticate as the application.
Generate a new private key and store the .pem file downloaded for later use.

_Note: the private keys can and should be rotated periodically to limit the risks of them being exposed in use._

### Setup Secrets for the Approval workflows
In this demo I use an environment as it provides an oppotunity to add protection rules for a manual sign-off on any approval. Therefore for your repo create a new Environment called `Production` from the setting menu in your repo. Inside this set two new secrets and set their values to the information you stored in the previous section. These secrets should be:
- `APPLICATION_ID` - The value for this should be the AppID you noted down earlier
- `APPLICATION_PRIVATE_KEY` - The value for this should be the contents of the Private Key `.pem` file you downloaded earlier. _Note: Simply `Nano your_pem_file.pem` and copy/paste out the key will work great.


### Install the GitHub Applications
You now have everything setup for your repository. All thats left is to install the new GitHub Application on any organisations that may have their repositories the target for promotion/approval. Details for installing GitHub apps can be found at https://docs.github.com/en/developers/apps/managing-github-apps/installing-github-apps. For each organisation you add you will want to amend the issue in [the issue YAML file](./.github/ISSUE_TEMPLATE/promote_internal.yaml) to have in place the currrent list of organisations with the GitHub App installed and ready as a target for repo promotion/approval.

_Note: Every organisation needs the GitHub App install top participate - including the one that your repo resides in or any orther oganisations participating.


## Invoking an approval workflow

Now your installed you can invoke the Approval workflow by creating an issue in your repository. The templates cloned in the `.github` folder will guide you to a issue form collecting the relevant fields which will invoke a workflow that will change your `Private` repo status to `Internal`.

