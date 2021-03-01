# Docker Setup For WordPress Development Using Ansible.

This is meant to be a starter template for setting up docker containers for running WordPress. It uses Ansible to create the containers instead of docker-compose.

The containers used are from [Wodby](https://wodby.com/) and the following referencess are helpful when configuring the playbook.

* [docker-compose.yml](https://github.com/wodby/docker4wordpress/blob/master/docker-compose.yml) for container configuration.
* [Available environment variables for configuration](https://github.com/wodby/docker4wordpress/blob/master/.env)
* [Wodby WordPress stack docs](https://wodby.com/docs/1.0/stacks/wordpress/)

## Notes

It's assumed that the WordPress installation will be kept in the site directory with the web root being site/web.

The settings for the environment variables referenced above are kept in the vars.yml file.

## Usage

Copy/fork this repo, update the "PROJECT_NAME" variable in the "vars" file and run:

    ansible-playbook -i inventory.bolivar --connection=local playbook.yml

Obviously using a different inventory file and potentially not specifying the "--connection" optiono if you're not creating the containers on your local machine that is named "bolivar".

The "site" directory is the docroot of the WordPress site, installation is simply just copying the files from the WordPress download to this directory and going through the [installation instructions](https://wordpress.org/support/article/how-to-install-wordpress/).

This setup assumes you've already set up the [traefik container](https://github.com/karlkedrovsky/traefik-ansible) on the machine to handle inbound traffic.

The Lets Encrypt integration assumes that your computer is accessible from the internet and port 80 is accepting requests for any sub-domain ending in bolivar.kedrovsky.com.

## Removing The Containers

If there's ever a need to stop and remove the containers just run:

    ansible-playbook -i inventory.bolivar --connection=local playbook-remove.yml

## Basic Auth

Generate a password using:

    openssl passwd -apr1

Update the basicauth and middleware labels in the web server config of the playbook with the new user and password. The default user/password in the playbook is wordpress/wordpress.

To remove the basic auth just remove the these two labels

    "traefik.http.middlewares.{{ PROJECT_NAME }}-auth.basicauth.users": "wordpress:$apr1$w/XbxD2m$mTZm7UFIOga16nuhiS87Q0"
    "traefik.http.routers.{{ PROJECT_NAME }}.middlewares": "{{ PROJECT_NAME }}-auth"