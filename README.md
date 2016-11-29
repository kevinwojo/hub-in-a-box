# Preliminary Readings
The current deployment system is a little daunting to newbies. In the near future it is my goal to simplify the workflow by reducing deployment
to a series of `git checkout` operations instead of merging of branches and maintaining separate histories for each hub.

Hubzero's Git Flow is outlined here: [Link to Hubzero Git Deployment Google Doc](https://docs.google.com/document/d/1WqFhxDUvVxK6ULge4VC2KVM5IrbYk8qDcft6rjdnLGw/edit#heading=h.5wemay9hqxq7)  

External contributions will be handled as such: [Link to External Contributions Google Doc](https://docs.google.com/document/d/12douK6PPGdOayDSmVPCTGv2TDld-9qhoQOOXl3jMDmM/edit?usp=sharing)

# QA-testing Git
This is the approach we will use to update QA / Stage machines up until the release. This should be ran daily and contain only fixes. 
(Still writing these)

# Production Push
This is the procedure we will use for *the* monthly release.
0. (first time only) `git clone gitolite@hubzero.org:hubzero-cms`
1. Checkout the stable branch:   
`git checkout 2.1.0`
2. Update the stable branch:  
`git pull --rebase 2.1.0`
3. Checkout the stable production branch:  
`git checkout 2.1.0-production`
4. Update the stable production branch (just in case):  
`git pull --rebase 2.1.0-production`
5. Merge the stable branch into production:  
`git merge 2.1.0` This will have you write a commit message, I usually just keep the default.
6. Push the changes:  
`git push`
7. In the current flow: update dev, update stage, update production.  

(*) Note: This will change as we move to hosting the `app` directory on Gitlab.

# General Procedure
This is current method for updating production hubs(*). 

For each supported customer, *if* they have a dev,stage,production machine, the production repository is hosted on stage.
This means that when you complete Step #6 in the 'Production Push' section, you 
merge in the stable production branch with the production repository on stage.  

If the customer only has a dev and production machine, the repository lives on dev.


## non-HIPPA deployments

From lando.aws.hubzero.org

1. Update the dev-hubs group:  

`ansible-playbook update-dev.yml`

2. Update the stage-hubs group:  

`ansible-playbook update-stage.yml`

3. Update the prod-hubs group:  

`ansible-playbook update-production.yml`

## HIPPA Hubs
From control.hubzero.org, repeat steps above. It is possible to do this all from 
control.hubzero.org, however Pascal needs to have the hubadmin keys straightend out
to allow ansible to login as hubadmin from control.hubzero.org.

# The Hosts / Inventory File
The `hosts` file in this repository contains a listing of hubs and their group. To add a new hub, simply add their FQDN to the appropriate group. This should be self-explanatory.

# Ansible Vault
Ansible Vault is used to store deployment secrets such as tokens, credentials, or other variables which should not appear in plain-text in the Ansible Playbook.

# Permissions and Access
Ansible runs as the `hubadmin` user on each host. In to run these plays you must
1. Send your public key to Pascal to add to each `hubadmin` account's authorized_keys file. (This is currently done by Ogre)
2. Have Pascal add your account to control.hubzero.org
3. Send your public key to Nick, Dave, or Kevin to add to lando.aws.hubzero.org and create the appropriate account.

In the future, another mechanism (Ansible Tower) will unify these two processes.


