# WordPress / Trellis / Bedrock Starter

## Stack

- [Trellis](https://roots.io/trellis/) - Uses Ansible to configure consistent dev/stage/production environments
  - Nginx (with optional FastCGI micro-caching)
  - PHP 7.0
  - MariaDB as a drop-in MySQL replacement (but better)
  - SSL support (A+ on https://www.ssllabs.com/ssltest/)
  - HTTP/2 support (requires SSL)
  - Composer
  - WP-CLI
  - sSMTP (mail delivery)
  - Memcached
  - Fail2ban
  - ferm
- [Bedrock](https://roots.io/bedrock/)
  - Using Composer to manage all dependencies, including WordPress
  - Easier environment-specific configuration
  - Separate WP core files from our site files

## Project Structure

- **site** - Trellis Ansible files
  - **deploy-hooks**
    - `build-before.yml` - Custom commands to run before deploying
  - **group_vars** - Environment variables for Ansible
    - **all**
    - **development**
    - **production**
    - **staging**
  - **hosts**
  - **lib**
  - **roles**
  - `dotfiles` - Various configs
- **site** - Bedrock-based WordPress site
  - **config** - WordPress configuration files
    - **environments** - Environment specific configs
    - `application.php` - Primary WP config file (wp-config.php equivalent)
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

## Docs

- [Get Started](get_started.md) - Configure everything for local development
- [Remote Server Setup](remote_server_setup.md) - Setup remote server for staging/production
- [Development Workflow](development_workflow.md) - Create custom site/theme
- [Deployment Workflow](deployment_workflow.md) - Deploy changes to staging/production









