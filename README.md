# odoo_v8.0
This repo is used for production for odoo v8 installations. It is also the place where we develop our own addons for
odoo. The *master* branch of this repo is always a deployable production ready branch. All branch names (=Instances) 
uploaded here have to follow this examples:

- **fix[ISSUE Number]** e.g.: *fix1234*  = FIXES, Developments, Updates - (Make github Issue first!)
- **int[customer id]** e.g.: *intdemo* = Production Instances hosted on our servers
- **ext[customer id]** e.g.: *exthof* = Production Instances hosted on external servers

If they do not follow this conventions they are considered to be test branches. To get a better idea how final Database
Names will look like on a server following this conventions consider teh following examples. REPO (o8) and BRANCH 
(= Instance Name) will be automatically added by odoo-tools.sh!

#### Examples of full Database Names: 

**[REPO]_[BRANCH]_[DATABASE]**

- o8_exthof_hof
- o8_exthof_schulung
- o8_exthof_demo
- o8_intnpodemos_demo
- o8_intnpodemos_pfo
- o8_intnpodemos_pfo
- o8_intope_erp
- o8_intdad_web


## GOALS

#### Better version and update handling
- updated requirements.txt to install with pip install -r /path/to/requirements.txt
- all third party addons as well as odoo itself are linked as submodules

#### Simple setup through odoo-tools.sh
**odoo-tools.sh** is a simple setup script that is able to
- prepare an ubuntu 14.04 LTS server for odoo
- setup new instances of odoo on the local server
- create new databases for a local instance with setup of:
    - postgres user
    - server.conf und server.init
    - database creation
    - etherpad setup
    - nginx setup (match url to local db)
    - backup and logrotate cron jobs
    - create and link customer addons into customers Instance /addons folder
    - Install push-to-deploy workflow for updating the customer addons
- ToDo: update an instance to branch master from github
- ToDo: deploy addon(s) to one or more databases on the local server

Also there is a tool called **db-tools.sh** to backup and restore local databases.


## SETUP
This setup process will only work on a fresh install of Ubuntu 14.04 LTS. Make sure timezone is correct and the server
has internet access!

```bash
# 1.) Be Root ;)
sudo su

# 2.) get odoo-tools.sh
wget -O - https://raw.githubusercontent.com/OpenAT/odoo_v8.0/master/TOOLS/odoo-tools.sh > odoo-tools.sh
chmod 755 odoo-tools.sh

# 3.) Prepare the Ubuntu Server (reboot after finish is recommended)
odoo-tools.sh prepare

# 4.) Setup a new instance:
# USAGE: odoo-tools.sh setup {TARGET_BRANCH}
odoo-tools.sh setup intdemo 

# 5.) Setup a new customer Addons repo in github:
# USAGE: Manual goto github and create a new repo if not exists already for this customer even we do not use a specific addon
Goto Gibhub  https://github.com/OpenAT/ click + New Repository give it a name like "cu_dev01" "cu_herz"
Than goto the settings of this new repo and create a webhook or click here https://github.com/OpenAT/cu_dev01/settings/hooks
use payload Url like: http://subdomain.domainname.net/cu_dev01 --> production would be the customers domain
content Type: application/x-www-from-urlencoded
and safe the hoock,
move on with newdb intallation now
after your newdb intallation you should see now a green check if you made everything correct
NOW you can Push your modules into this repo and start developing or already finished before your newdb intallation
- TODO: maybe we can automate this process too, right now i think it is a good idea to keep this in manual mode, or at least repo creation and hook creation

# 6.) Create a new Database:
# Start once with no options to see usage (look for newdb)
odoo-tools.sh newdb <...>

#For Production Instances ONLY!: you should immediately push your new branch to github!
# HINT: Dont worry *.conf *.log and *.init files and database directories o8_*/ are in .gitignore !
git push origin YOURBRANCH
```


## DEVELOPMENT

To develop with this repo use this workflow:

```bash
# 1.) Clone the repo odoo_v8 branch master locally:
git clone -b master --recurse-submodules https://github.com/OpenAT/odoo_v8.0.git ${instance_dir}

# Check if the upstream (remote) is set correctly for the master branch
git branch -vv
git --set-upstream-to=https://github.com/OpenAT/odoo_v8.0.git master    # creates remotes and origin

# 2.) Create and checkout a new branch:
git branch dev-ckeditor_advanced
git checkout dev-ckeditor_advanced

# 3.) Push your Branch to Github (so everybody knows what you are working on)
git commit
git push origin dev-ckeditor_advanced

# 4.) Do stuff and commit and push changes until ready:
git add [file or folders]    # This tells git what to include in next commit
git commit -m "[ADD] Added README.md"
git push origin dev-ckeditor_advanced

# 5.) When ready with development 
# - create pull request on github (at webpage) if wanted to discuss / review changes
# - Update master branch to latest
# - rebase dev-ckeditor_advanced on master
# - rebase dev-ckeditor_advanced submodules on master
# - merge dev-ckeditor_advanced in master with rebase
git fetch
git checkout master    # NOW IN BRANCH master
git pull
git checkout dev-ckeditor_advanced    # NOW IN BRANCH dev-ckeditor_advanced
git rebase master
git submodule update
git checkout master    # NOW IN BRANCH master
git merge dev-ckeditor_advanced
git push origin master
```

Adding new Submodules to the repo:
```bash
# This is an example how to add a submodule:
git submodule add -b master https://github.com/ether/etherpad-lite.git etherpad-lite

# Ubdate all submodules
git submodule update --rebase --remote --recursive
```


## UPDATE OF AN INSTANCE (and its DBs)

This is for now only a placeholder but will describe the update process of a customer instance. Keep tuned ;)
```bash

# Update Master
git checkout master                             
git pull                                        
git submodule update

# Update Branch (Code Repo for a server in this case)
git checkout intdadi                            
git rebase master
git submodule update
git push origin intdadi
```


## Update of all Submodules of branch master
```bash
git checkout master
git pull
git submodule update --remote --rebase --recursive
git commit -am "[UPDATE] all submodules updated"
# then you have to push master branch back to origin/master
```

## DOCUMENTATION

#### odoo v8
- [Latest Dev Docu](https://www.odoo.com/documentation/master/howtos/website.html)
- [odoo v8 api guidelines](http://odoo-new-api-guide-line.readthedocs.org/en/latest/)
- [Technical Memento](https://www.odoo.com/files/memento/OpenERP_Technical_Memento_latest.pdf)
- [eval many2many write](https://doc.odoo.com/v6.0/developer/2_5_Objects_Fields_Methods/methods.html/#osv.osv.osv.write)
- [WebApps Tutorial HBEE](https://www.hbee.eu/en-us/blog/archive/2014/9/17/odoo-web-apps/)
- [Forum how to's](https://www.odoo.com/forum/how-to)

#### Configuration (res.config) related
- https://www.odoo.com/forum/help-1/question/how-can-i-save-load-my-own-configuration-settings-30123
- https://www.odoo.com/forum/help-1/question/how-can-i-create-own-config-for-my-custom-module-41981
- https://www.odoo.com/forum/help-1/question/is-it-possible-to-set-database-default-configuration-values-507
- https://doc.odoo.com/6.0/developer/5_16_data_serialization/xml_serialization/
- http://stackoverflow.com/questions/9377402/insert-into-many-to-many-openerp/9387447#9387447

#### Other odoo Tools and Docs
- [carddav for odoo](https://github.com/initOS/openerp-dav)
- http://odoohub.wordpress.com/2014/08/15/where-is-the-odoo-documentation/
- http://djpatelblog.blogspot.in/2014/09/odoo-new-api-recordsets.html
- [server.conf db_filter= parameter](https://www.odoo.com/forum/help-1/question/domain-based-db-filter-6583)

#### XMLRPC, ErpPeek, Connector ...
- [XMLRPC and erppeek by wirtel](http://wirtel.be/posts/en/2014/06/13/using_erppeek_to_discuss_with_openerp/)
- [erppeek](http://erppeek.readthedocs.org/en/latest/index.html)
- [oerplib](https://github.com/osiell/oerplib)
- [oerlib docu](https://pythonhosted.org/OERPLib/#supported-openerp-odoo-server-versions)
- [xmlrpc lib docu](https://docs.python.org/2/library/xmlrpclib.html)
- [odoo connector](http://odoo-connector.com)

#### git, git workflow and github
- [Github Rebase Workflow](http://mettadore.com/2011/09/07/the-ever-deployable-github-workflow/)
- [Git Submodules](http://git-scm.com/docs/git-submodule)
- [Github Using Pull Requests](https://help.github.com/articles/using-pull-requests/)
- [Adding an existing project to github](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)
- [Push to a Remote](https://help.github.com/articles/pushing-to-a-remote/)
- [README.md Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

#### Odoo Setup in Ubuntu 14.04 LTS
- [nginx and odoo](http://wirtel.be/posts/en/2011/11/02/nginx-proxy-openerp/)
- [odoo v7 setup scripts](https://github.com/OpenAT/odoo-tools/tree/7.0)
- [odoo 8 setup script by Andre Schenkel](https://github.com/lukebranch/odoo-install-scripts/blob/master/odoo-saas4/ubuntu-14-04/odoo_install.sh)
- [odoo setup ubuntu 14 lts](https://www.odoo.com/forum/help-1/question/how-to-install-odoo-from-github-on-ubuntu-14-04-for-testing-purposes-only-ie-not-for-production-52627)

#### Python, PIP, VirtualEnv
- [PYTHON](https://www.python.org)
- [Python Quick Ref](http://rgruet.free.fr/#QuickRef)
- [PIP Docu](http://pip.readthedocs.org/en/latest/user_guide.html#requirements-files)
- [argparse](https://docs.python.org/2.7/library/argparse.html#other-utilities)

#### BASH Scripting
- [if conditions](http://www.tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html)
- [sed](http://wiki.ubuntuusers.de/sed)

#### Java Script
- https://developer.mozilla.org/de/docs/Web/JavaScript