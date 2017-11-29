# Remote Server Setup

How to setup remote server for staging/production.

## 1. Create a remote server

You need a have a private server or virtual private server (AWS, Digital Ocean, etc..) running a bar/stock version of Ubuntu 16.04 Xenial.

**You can't run trellis on a shared host** because you need to be able to connect to your server from your local computer via SSH.

### Digital Ocean

For Digital Ocean create a new droplet and choose Ubuntu 16.04 x64 for the distribution, $5/mo for the size, choose any datacenter region, and **include your SSH key**.

Confirm that you can SSH into the server

```
ssh root@<server-ip-address>
```

Add an admin user

```
# adduser admin --ingroup sudo
<Add password (remember for Step 2.4)>
<Leave user info blank>

# gpasswd -a admin sudo
# su - admin
$ mkdir .ssh
$ chmod 700 .ssh
$ nano .ssh/authorized_keys
<paste id_rsa.pub inside>
<control+0 to write file>
<control+X to exit>

$ chmod 600 .ssh/authorized_keys
$ exit
# service ssh restart
```

## 2. Configure Trellis

### 2.1. Tell Trellis where the remote server is

Update the `trellis/hosts/<environment>` file, replacing `your_server_hostname` with the IP address of your remote server.

### 2.2. Configure site

Update the `trellis/group_vars/<environment>/wordpress_sites.yml` file.

1. Replace `example.com` with your site url
1. Change `canonical: staging.example.com` to the staging url (you should have DNS access to setup this domain for your private server)
1. Change `repo: git@github.com:example/example.com.git` to the GitHub repo for your site's source code

[See docs for additional options](https://roots.io/trellis/docs/wordpress-sites/#remote-servers)

### 2.3. Add GitHub Keys

Open `trellis/group_vars/all/users.yml`.

Uncomment the GitHub URLs, and edit them to reflect your GitHub username.

Confirm you can SSH into GitHub

```
ssh -T git@github.com
```

### 2.4. Add security settings

Open the `trellis/group_vars/<environment>/vault.yml`.

- Replace `example.com` with your site url
- Add unique values for `vault_mysql_root_password` and `db_password`
- Update `password: example_password` with the `admin` user password you set up on the remote server in Step 1
- Generate your keys using [https://roots.io/salts.html](https://roots.io/salts.html) and replace all instances of "generateme"

If setting up the production environment you will want to encrypt the `vault.yml` file using [Ansible Vault](https://roots.io/trellis/docs/vault/) so you are not committing plain text credentials to the repo.

## 3. Provision the remote server

Now that the proper configuration is in place, we need to let Trellis provision the production server by running Ansible which will run through a series of commands that will download, install, and configure the server.

From inside the `trellis` directory run the following command where `<environment>` is either `development` or `production`.

```
ansible-playbook server.yml -e env=<environment>
```

This will take about 5–10 minutes, after which you will have a properly configured server we can deploy the site to.

At this point we will have a properly configured server with PHP, Nginx, Database, etc... but it doesn't have our files on it.

## 4. Deploy files to remote server

To deploy the site's files run the following command from inside the `trellis` directory. Where `<domain>` is the site url you added to the `<environment>/wordpress_sites.yml` file in Step 2.2.

```
./bin/deploy.sh <environment> <domain>
```

`deploy.sh` is a very simple Bash script which just runs the actual command: `ansible-playbook deploy.yml -e "site=<domain> env=<environment>"`. You can always use this command itself if you need to use any additional `ansible-playbook` options.

This will clone our repo and download our files to the remote server then install our composer dependencies (including WordPress)

You can now view your site at the `canonical` url you added to the `wordpress_sites.yml` file in Step 2.2.

Trellis only manages files and does not automatically install WordPress on the remote server so you will just see the default WordPress install screen.

## 5. Sync Wordpress data

Install the WP Sync DB plugin by running the following command from inside the `site` directory.

```
composer require wp-sync-db/wp-sync-db:dev-master@dev
```
