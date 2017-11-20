# WordPress / Trellis / Bedrock Starter

## Stack

- [Trellis](https://roots.io/trellis/) - Uses Ansible to configure consistent dev/stage/production environments
- [Bedrock](https://roots.io/bedrock/)
  - Using Composer to manage all dependencies, including WordPress
  - Easier environment-specific configuration
  - Separate WP core files from our site files

## Project Structure

- **site** - Trellis Ansible files
  - **deploy-hooks**
    - `build-before.yml` - Custom commands to run before deploying
  - **group_vars** - Environment variables for Ansible
    - **all** -
    - **development** -
    - **production** -
    - **staging** -
  - **hosts** -
  - **lib** -
  - **roles** -
  - `dotfiles` - Various configs
- **site** - Bedrock-based WordPress site
  - **config** - WordPress configuration files
    - **environments** - Environment specific configs
    - `applicaiton.php` - Primary WP config file (wp-config.php equivalent)
  - **vendor** - Composer packages (never edit)
  - **web** - Web root (vhost document root)
    - **app** - wp-content equivalent
      - **mu-plugins** - Must use plugins
      - **plugins** - General Plugins
      - **themes** - Themes
      - **uploads** - Uploads
    - **wp** - WordPress core (never edit)
    - `index.php` - WordPress view bootstrapper
    - `wp-config.php` - Required by WP (never edit)
  - .env - Automatically configured by Trellis
  - `composer.json` - Manage versions of WordPress, plugins & dependencies

## Get Started

### 1. Install Local Dependencies

1. [Virtualbox](https://www.virtualbox.org/wiki/Downloads) >= 4.3.10
1. [Vagrant](https://www.vagrantup.com/downloads.html) >= 1.8.5
1. [vagrant-bindfs](https://github.com/gael-ian/vagrant-bindfs#installation) >= 0.3.1 (Windows users may skip this if not using vagrant-winnfsd for folder sync)
1. [vagrant-hostmanager](https://github.com/smdahlen/vagrant-hostmanager#installation)
1. [Ansible](http://docs.ansible.com/ansible/intro_installation.html#latest-releases-via-pip) >= 2.4 ([except for Windows users](https://roots.io/trellis/docs/windows/))
1. Ansible Galaxy roles: run `ansible-galaxy install -r requirements.yml` in your local trellis directory

### 2. Configure Site

Open `trellis/group_vars/development/wordpress_sites.yml` in your editor.

1. Replace `example.com` with your site url
1. Replace `example.dev` with the development url you want
1. Replace `admin@example.dev` with your email

Open `trellis/group_vars/development/vault.yml` in your editor.

1. Enter unique values for the `vault_mysql_root_password`, `admin_password`, and `db_password` keys

[See docs for additional options](https://roots.io/trellis/docs/wordpress-sites/)


### 3. Start a Local Instance of the WordPress Site Using Vagrant

Enter the trellis directory `cd trellis`.

Run the command `vagrant up`.

This will create a virtual machine running your WordPress site based on the `Vagrantfile` in our `trellis` directory which uses Ansible provisioner to run the `dev.yml` playbook.

The first time you run this, it’ll take a 5–10 minutes. This is because Vagrant needs to download and configure all the pieces required to get the box up and running properly. After the first time, a lot of dependencies will be cached, which makes things much quicker for subsequent calls to `vagrant up`.

### 4. View the site locally

You should then be able to see the site at the dev url you configured in the `wordpress_sites.yml` file.

[example.dev](http://example.dev)

You can login to the WordPress dashboard by going to `<your-dev-url>/wp/wp-admin`. Please note the addition `wp` directory which keeps the core WordPress files separate from the rest of our app.

[example.dev/wp/wp-admin](http://example.dev/wp/wp-admin)

The username is the email you added to `wordpress_sites.yml` and the password is what you set in the `vault.yml` file.

## Theme development

Custom theme development takes place inside the `site` directory.

More info to come.

### Plugins

Plugins are all managed using [Composer](http://getcomposer.org/).

[WordPress Packagist](http://wpackagist.org/) is already registered in the `composer.json` file so any plugins from the [WordPress Plugin Directory](http://wordpress.org/plugins/) can easily be required.

Check out [this blog post](https://roots.io/using-composer-with-wordpress/) for more info.

#### How to add a plugin

To add a plugin, add it under the `require` directive in `composer.json` or use `composer require <namespace>/<packagename>` from the command line. If it's from WordPress Packagist then the namespace is always `wpackagist-plugin`.

Example: `"wpackagist-plugin/akismet": "dev-trunk"`

Whenever you add a new plugin or update the WP version, run `composer update` to install your new packages.

`plugins`, and `mu-plugins` are Git ignored by default since Composer manages them. If you want to add something to those folders that isn't managed by Composer, you need to update `.gitignore` to whitelist them:

`!web/app/plugins/plugin-name`

Note: Some plugins may create files or folders outside of their given scope, or even make modifications to `wp-config.php` and other files in the `app` directory. These files should be added to your `.gitignore` file as they are managed by the plugins themselves, which are managed via Composer. Any modifications to `wp-config.php` that are needed should be moved into `config/application.php`.

## Updating

### How to update a plugin

Updating a plugin is just a matter of changing the version number in the `composer.json` file.

Then running `composer update` will pull down the new version.

### How to update WordPress

Updating your WordPress version is just a matter of changing the version number in the `composer.json` file.

Then running `composer update` will pull down the new version.

### How to update Trellis

Trellis is installed as a [git subtree](http://chrisknightindustries.com/2015/24/11/git-subtrees-for-trellis-workflow.html). To update the subtree you need to checkout the corresponding branch and pull/ merge. To avoid merging the histories use the --squash command.

From inside the root project directory run the following commands.

```
$ git checkout trellis
$ git pull
$ git checkout master
$ git merge -X subtree=trellis/ --squash trellis/master
$ git commit -m "Update trellis from trellis/master"
```

You will most likely get some merge conflicts with the files you have edited/customized which you will have to resolve.

## Remote Server Setup

### 1. Create a remote server

You need a have a private server or virtual private server (AWS, Digital Ocean, etc..) running a bar/stock version of Ubuntu 16.04 Xenial.

**You can't run trellis on a shared host** because you need to be able to connect to your server from your local computer via SSH.

For Digital Ocean create a new droplet and choose Ubuntu 16.04 x64 for the distribution, $5/mo for the size, and choose any datacenter region.

### 2. Provision the remote server

Update the `trellis/hosts/<environment>` file, replacing `your_server_hostname` with the IP address of your remote server.

Update the `trellis/group_vars/<environment>/wordpress_sites.yml` file



