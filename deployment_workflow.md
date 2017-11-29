# Deployment Workflow

How to deploy changes to staging/production.

## Commit and push your changes to GitHub


## Deploy changes to remote server

To deploy the updated files run the following command from inside the `trellis` directory. Where `<domain>` is the site url you added to the `<environment>/wordpress_sites.yml` file in Step 2.2.

```
./bin/deploy.sh <environment> <domain>
```

`deploy.sh` is a very simple Bash script which just runs the actual command: `ansible-playbook deploy.yml -e "site=<domain> env=<environment>"`. You can always use this command itself if you need to use any additional `ansible-playbook` options.
