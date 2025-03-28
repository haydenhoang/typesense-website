# Team Accounts in Typesense Cloud

In Typesense Cloud, you can share access to your clusters with members of your team. 

## Using GitHub-based Auth

When you sign up with GitHub, we create "teams" in Typesense for each GitHub organization you're a part of. 
This is automatically done when you log in the first time (if you had authorized access to your GitHub orgs).

Once you're logged in, you'll see an account switcher on the top right with your username. 
If you click on your username, you'll see your other team accounts. 

:::tip
If you don't see an account switcher when you log in to Typesense Cloud, or don't see a particular team, you most likely didn't grant access to the corresponding GitHub org when you logged in the first time.

To remedy this, you want to visit [https://github.com/settings/applications](https://github.com/settings/applications), remove Typesense Cloud from the list, then logout of Typesense Cloud and log back in.
This time, you'll see the GitHub prompt to authorize organization access, you want to enable the organization that you'd like to create clusters under.
:::

If you then provision a cluster under one of those team accounts, all members that are part of your GitHub organization will also see the cluster(s) when they log in with their own GitHub accounts, and switch to this team.

By default, all members of your team have Administrator access. 
But you can [customize access roles and permissions](./role-based-access-control-admin-dashboard.md) for each user from your team's account page.

:::tip
If you do not have a GitHub organization, you can create one for free (even if it is only to use with Typesense Cloud) from this page: [https://github.com/organizations/plan](https://github.com/organizations/plan).

Click on "Create a free organization" on the left pane, and then invite your other team members to this Github organization.
:::

## Using Email/Password-based Auth

When you sign up for Typesense Cloud with your email address and a password, you'll see the option to create a team account when you click on your email address on the top right of the screen.

This will open up the account switcher, and when you click on "+ New Team", you'll be able to create a new team and send out email invitations to other members of your team.

Clusters that you create under your team account are then shared with all members of your team. 

By default, all members of your team have Administrator access.
But you can [customize access roles and permissions](./role-based-access-control-admin-dashboard.md) for each user from your team's account page.

## Using Single Sign-on based Auth

When you sign up via [Email/Password-based Auth](#using-emailpassword-based-auth) and create a team, you can set up any SAML-based SSO platform to be the identity provider for that team. 

Read this dedicated guide about [setting up Single Sign-on](./single-sign-on.md) for more information. 