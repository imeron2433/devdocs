---
layout: default
group: cloud
subgroup: 08_setup
title: Import an existing Magento project
menu_title: Import an existing Magento project
menu_order: 75
menu_node: 
version: 2.0
github_link: cloud/access-acct/first-time-setup_import.md
---
 
This topic discusses how to can start your Magento Enterprise Cloud Edition project from an existing Magento Enterprise Edition (EE) installation. 

## Prerequisites
Before you continue, make sure you have done all of the following:

*   Your existing Magento EE code must be in Git. 

    If not, you must add it to Git before continuing.
*   Complete the tasks discussed in [Set up a Magento workspace]({{page.baseurl}}cloud/before/before-workspace.html).

This is our recommended workflow:

1.  Create a new, empty Magento Enterprise Cloud Edition project from a template.

    This new project has files and directories specific to Magento Enterprise Cloud Edition.
2.  Replace the contents of this project with your code.

    This replaces not only Magento code but static and compiled assets as well.
3.  Import your Magento database into your Magento Enterprise Cloud Edition project.
4.  TBD

{% include cloud/new-project-from-template.md %}

## Prepare to import Magento files and database {#cloud-import-prepare}
This section discusses tasks you must perform in your existing Magento EE installation to prepare it to be imported into a Magento Enterprise Cloud Edition project.

Before you continue, push all pending changes to Git.

You must do the following:

*   Add Cloud-specific files and directories to Magento EE. Without these files and directories, your Magento EE code can't be imported to Cloud.
*   Dump your Magento EE database.

### Prepare Magento EE files {#cloud-import-prepare-files}
For your Magento EE code to import to a Magento Enterprise Cloud Edition project, you must have a directory and some files required by Cloud. Following is the list of those files:

*  [`.magento/routes.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_routes.html)
*  [`.magento/services.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_services.html)
*  [`.magento.app.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_magento-app.html)
*  `magento-vars.php`

#### Add required configuration files {#cloud-import-prepare-files-config}

To add required files to your Magento EE code:

1.  Go to the [Magento Enterprise Cloud Edition GitHub](https://github.com/magento/magento-cloud){:target="_blank"}.
2.  Select the branch corresponding to the Magento EE version you currently have.

    The following figure shows an example of selecting the `2.1.4` branch.

    ![Switch to your current EE branch]({{ site.baseurl }}common/images/cloud_cloud-git-214.png){:width="600px"}

    In the procedure that follows, you'll copy the contents of some of these files to your Magento EE system.
3.  Log in to your Magento EE system as, or switch to, the Magento file system owner.
4.  Enter the following commands in the order shown:

        cd <Magento installation dir>
        mkdir .magento
5.  One at a time, create the following files in your Magento EE system using the contents of the files in the Magento Enterprise Cloud Edition GitHub:

    *   `<Magento EE install dir>/.magento.app.yaml`
    *   `<Magento EE install dir>/magento-vars.php`
    *   `<Magento EE install dir>/.magento/services.yaml`
    *   `<Magento EE install dir>/.magento/routes.yaml`

    For example, to create `<Magento EE install dir>/.magento.app.yaml` from the 2.1.4 branch:

    1.  In the  Magento Enterprise Cloud Edition GitHub, click [**.magento.app.yaml**](https://github.com/magento/magento-cloud/blob/2.1.4/.magento.app.yaml){:target="_blank"}.
    2.  In the upper right, click **Raw**, as the following figure shows.

        ![View the raw version of the file]({{ site.baseurl }}common/images/cloud_cloud-git_raw.png){:width="600px"}
    3.  In your Magento EE project, open a text editor in the Magento EE installation directory (for example, `/var/www/html/magento2`).
    4.  Paste the raw contents of `.magento.app.yaml` from GitHub into the text editor.
    5.  Make sure the file is named `.magento.app.yaml` when you save the file.
    6.  Repeat these tasks for the other files.

        Make sure to create `magento-vars.php` in the Magento root directory.

        Make sure to create `routes.yaml` and `services.yaml` in the `.magento` subdirectory.

Modify these files as necessary as discussed in the following topics:

*  [`.magento/routes.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_routes.html)
*  [`.magento/services.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_services.html)
*  [`.magento.app.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_magento-app.html)

#### Add or update `auth.json` {#cloud-import-authjson}
To enable you to install and update Magento Enterprise Cloud Edition, you must have an `auth.json` file in your project's root directory. `auth.json` contains your Magento EE [authorization credentials](http://devdocs.magento.com/guides/v2.1/install-gde/prereq/connect-auth.html) for Magento Enterprise Cloud Edition.

In some cases, you might already have `auth.json` so check to see if it exists and has your authentication credentials before you create a new one. It's located in your Magento root directory.

[Get a sample `auth.json`](https://raw.githubusercontent.com/magento/magento-cloud/master/auth.json.sample){:target="_blank"}

To create a new `auth.json` in the event you don't have one:

1.  Use a text editor to create a file named `auth.json` in your Magento root directory.
3.  Replace `<public-key>` and `<private-key>` with your Magento EE authentication credentials.

    See the following example:

        "http-basic": {
           "repo.magento.com": {
              "username": "<public-key>",
              "password": "<private-key>"
            }
        }
3.  Save your changes to `auth.json` and exit the text editor.

### Edit `composer.json` {#cloud-import-composer}
Before you push code to the Magento Enterprise Cloud Edition Git repository, you must change your `composer.json` so it meets Cloud requirements.

[View a sample `composer.json`](https://raw.githubusercontent.com/magento/magento-cloud/master/composer.json){:target="_blank"}

To edit `composer.json`:

1.  If you haven't done so already, log in to your Magento Enterprise Cloud Edition server as, or switch to, the Magento file system owner.
2.  In a text editor, open `composer.json` in the project root directory.
3.  Substitute the following value in the `require` section:

        "magento/product-enterprise-edition": "<version>",

    with

        "magento/magento-cloud-metapackage": "<version>",
    
    <div class="bs-callout bs-callout-info" id="info" markdown="1">
    Both `<version>` values _must be the same_. In other words, if your current EE version is 2.1.3, your `magento-cloud-metapackage` version must also be 2.1.3.
    </div>

4.  Update the `"files"` directive in the `autoload` section to refer to `app/etc/NonComposerComponentRegistration.php` as follows:

        "autoload": {
        "psr-4": {
            "Magento\\Framework\\": "lib/internal/Magento/Framework/",
            "Magento\\Setup\\": "setup/src/Magento/Setup/",
            "Magento\\": "app/code/Magento/"
        },
        "psr-0": {
            "": "app/code/"
        },
        "files": [
            "app/etc/NonComposerComponentRegistration.php"
        ]
    }
5.  Save your changes to `composer.json` and exit the text editor.

6.  When you're done, commit the changes to GitHub:

        cd <Magento EE install dir>
        git add -A && git commit -m "Add Cloud files" && git push origin <branch name>

### Prepare the Magento EE database  {#cloud-import-prepare-db}
Create a dump of the database you want to import using mysqldump. 

The following example shows how to compress the dump so it doesn't significantly interfere with traffic from in live site. 

In the example, the dump file is named `db.sql.gz`, but it is a good idea to include the date in the filename if you do multiple dumps over time.

Because the database dump can be large, we recommend you create it in a directory not tracked by Git.

Command syntax follows:

    mysqldump -h <db-host> -P <db-port> -p -u <db-user> <db-name> --single-transaction --no-autocommit --quick | gzip > ~/db.sql.gz

Example if your database is on localhost with the default port (3306) and the database user name is `magento`:

    mysqldump -p -u magento --single-transaction --no-autocommit --quick | gzip > ~/db.sql.gz

## Import files and Magento code {#cloud-import-files-and-db}
This section discusses how to import code from your existing Magento EE project to your Magento Enterprise Cloud Edition's Git repository `master` branch.

<div class="bs-callout bs-callout-warning" id="warning" markdown="1">
The procedure discussed in this topic replaces your new Magento Enterprise Cloud Edition project with the contents of your existing Magento installation. Any data, websites, stores, and so on will be lost.

Before you continue, make sure there is nothing in your Magento Enterprise Cloud Edition project you want to keep.
</div>

### Step 1: Create a remote Git reference {#cloud-import-ref}
This section discusses how to create a remote Git reference from your Cloud Git repository to the repository in which your Magento EE installation is located.

Before you continue, make sure you know the SSH or HTTPS URL for your Magento EE installation Git repository.

To create a remote Git reference:

1.  Log in to your local Magento Enterprise Cloud Edition development machine as, or switch to, the Magento file system owner.
2.  Make a copy of `composer.json` _in a non-tracked directory_ so it doesn't get overwritten.

        cp composer.json ../composer.json.cloud
3.  Rename your Cloud Git remote from `origin` to `cloud-project` to make it clear which repository is which:

        git remote rename origin cloud-project
4.  Add a remote upstream for your existing Magento EE installation:

        git remote add prev-project <git url>
5.  Confirm what you've done so far.

        git remote -v

    Results are displayed as follows.

    <pre class="no-copy">cloud-project   ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (fetch)
    cloud-project   ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (push)
    magento ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (fetch)
    magento ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (push)
    prev-project    git@github.com:mygitusername/myeereponame.git (fetch)
    prev-project    git@github.com:mygitusername/myeereponame.git (push)</pre>
6.  Make sure you're on the Cloud project `master` branch.

        magento-cloud environment:checkout master
7.  Make sure this `master` branch is set up to import code to the Cloud project.

        git branch -u cloud-project/master

### Step 2: Import your Magento EE code to your Cloud project {#cloud-import-imp}
Before you continue, make sure you've completed all tasks discussed in the preceding section.

To import your Magento EE code to Cloud:

1.  Fetch the Magento EE branch.

        git fetch prev-project
3.  Reset your Cloud `master` branch to contain the code and the commit history of your Magento EE branch:

        git reset --hard prev-project/<branch name>
4.  Push code from your Magento EE project to your Magento Enterprise Cloud Edition project, overwriting the previous contents and commit history with that of your project:

        git push -f cloud-project master





Let's say you want to import your existing codebase into the master branch of your cloud environment. In the same repository create two remote references, one for the remote repository for your cloud server, and the other for the remote repository of the project you want to import. Then use git-reset to push a branch from your existing project onto a branch of the cloud project. 

In this example, we are going to take code from the "develop" branch of an existing repo, and push it to the "master" branch of our cloud project. The project could be an integration environment, staging, or technically production (although obviously you would not directly import to a live production site). For the purpose of this demonstration, the kind of project you are importing code to does not matter.







https://github.com/magento-cloud/magento-cloud-template/blob/master/README.md

What kinds of things will be different in the imported project?

*   `composer.json` and `composer.lock`, with additional packages
*   Additional file resources used by third party extensions and libraries
*   Any third party extensions and themes that you installed without using Composer

    Those themes and extensions are located in the `app/code` and `app/design` directories




### Getting access information

First we need to find some information about the Cloud environment and the database there. We'll need the SSH url, and the name and password of the database for the environment.

#### SSH url

 * For your integration environments (the ones you can manage through the web interface), go the the project management page, select your project on the left hand side, click Access Site, and copy the URL from the "SSH Access" field.
    
 * For staging and production environments, the url will be provided by the Cloud team when your environment is provisioned.
#### Database access

The name of the database can be found in the `$MAGENTO_CLOUD_RELATIONSHIPS` environment variable. Display the variable with the following command. The database name is stored under `databases->path`. The password is found under `databases->password`.

  echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp

### Creating a database dump



### Importing the database dump

Now that you have created the dump, move it to the var directory of the application you are importing into:

  rsync db.sql <ssh url>:var/db.sql

SSH into the cloud environment and empty the existing database, if it is populated. If you have done any work you would like to refer to later that's been done in the Cloud environment, then make a backup of that first. Drop and recreate the database.

Now import the dump into the cloud environment's database:

  gunzip var/db.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h <db-host> -P <db-port> -p -u <db-user> <db-name>

### Update environment-specific configurations

You'll need to update the base_url configuration (web/[secure|unsecure]/base_url) for the default configuration scope and for each store view, store, and website scope.

Additionally, in order to be able to decrypt encrypted data from your imported database, copy your encryption from your old `env.php` file into the `env.php` file of your new application. `env.php` contains a nested PHP array storing configuration data. Place the encryption key in the path: `crypt->key`.

If `env.php` does not exist, create it with the following contents:

{% highlight php startinline=true %}
return array (
  'crypt' =>
  array (
    'key' => '331724b9bf288722d3da80b56e38b440',
  ),
);
{% endhighlight %}

### Import media
Now copy over media files inside pub/media. On your existing project, from the application's root directory, create a tarball of media assets (product images, etc) on your existing project.

  tar -czf media.tgz pub/media

Copy the media tarball to the application's var directory on the cloud environment

  rsync media.tgz <ssh url>:var/media.tgz

On the cloud environment, clear existing media files:

  rm -rf pub/media/*

Unpack the media tarball into the pub/media directory:

  tar -xzf var/media.tgz pub/media

### Cleanup
On the cloud environment, flush the cache:

  bin/magento cache:flush



--- EXISTING TOPIC BELOW THIS LINE TBD ---

## Before you push your imported project {#cloud-import-before}
Before you push your Magento project code, you *must* add `auth.json` to the project root directory and you *should* add configuration files as well.

### Add `auth.json`
Because the Composer repository that contains Magento Enterprise Cloud Edition requires authentication, you must add a file named `auth.json` to your project's root directory. This file contains your authentication keys. Without `auth.json`, the Magento software won't download.

Add `auth.json`, replacing the sample values with your public and private keys.

{% highlight json %}
{
   "http-basic": {
      "repo.magento.com": {
         "username": "<your public key>",
         "password": "<your private key>"
      }
   }
}
{% endhighlight %}

### Configuration files
Magento Enterprise Cloud Edition enables you to specify information like the Magento docroot, details about services (like the database, Redis, and so on), and routes in `yaml` files.

These files must be created before you push to your repository for Magento to be properly deployed.

<!-- https://docs.platform.sh/drupal_migrate/guides/configuration/drupal_application.html 
  https://docs.platform.sh/drupal_migrate/guides/type/php/drupal/migrate/import-database.html
  https://docs.platform.sh/drupal_migrate/guides/type/php/drupal/migrate/import-files.html -->

#### More information

*	[`.magento.app.yaml`]({{page.baseurl}}cloud/project/project-conf-files_magento-app.html)
*	[`routes.yaml`]({{page.baseurl}}cloud/project/project-conf-files_routes.html)
*	[`services.yaml`]({{page.baseurl}}cloud/project/project-conf-files_services.html)

#### Next steps
* [Set up an environment]({{ page.baseurl }}cloud/access-acct/set-up-env.html)
* [Set Magento Admin environment variables]({{ page.baseurl }}cloud/access-acct/set-up-env.html)