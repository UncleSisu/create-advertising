# Get Started

How to configure everything for local development.

## 1. Install Local Dependencies

1. [Virtualbox](https://www.virtualbox.org/wiki/Downloads) >= 4.3.10
1. [Vagrant](https://www.vagrantup.com/downloads.html) >= 1.8.5
1. [vagrant-bindfs](https://github.com/gael-ian/vagrant-bindfs#installation) >= 0.3.1 (Windows users may skip this if not using vagrant-winnfsd for folder sync)
1. [vagrant-hostmanager](https://github.com/smdahlen/vagrant-hostmanager#installation)
1. [Ansible](http://docs.ansible.com/ansible/intro_installation.html#latest-releases-via-pip) >= 2.4 ([except for Windows users](https://roots.io/trellis/docs/windows/))
1. Ansible Galaxy roles: run `ansible-galaxy install -r requirements.yml` in your local trellis directory

## 2. Configure Site

Open `trellis/group_vars/development/wordpress_sites.yml` in your editor.

1. Replace `example.com` with your site url
1. Change `canonical: example.dev` to the development url you want
1. Change `admin_email: admin@example.dev` to your email

Open `trellis/group_vars/development/vault.yml` in your editor.

1. Enter unique values for the `vault_mysql_root_password`, `admin_password`, and `db_password` keys

[See docs for additional options](https://roots.io/trellis/docs/wordpress-sites/)


## 3. Start a Local Instance of the WordPress Site Using Vagrant

From inside the `trellis` directory run the command `vagrant up`.

This will create a virtual machine running your WordPress site based on the `Vagrantfile` in our `trellis` directory which uses Ansible provisioner to run the `dev.yml` playbook.

The first time you run this, it’ll take a 5–10 minutes. This is because Vagrant needs to download and configure all the pieces required to get the box up and running properly. After the first time, a lot of dependencies will be cached, which makes things much quicker for subsequent calls to `vagrant up`.

## 4. View the site locally

You should then be able to see the site at the dev url you configured in the `wordpress_sites.yml` file.

You can login to the WordPress dashboard by going to `<your-dev-url>/wp/wp-admin`. Please note the addition `wp` directory which keeps the core WordPress files separate from the rest of our app.

The username is the email you added to `wordpress_sites.yml` and the password is what you set in the `vault.yml` file.
