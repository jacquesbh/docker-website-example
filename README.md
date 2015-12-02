# Docker website example

I use this project to test things with Docker:

* Deployment
* Scalability
* Duplication

The idea is to have a really simple project with all the tools required for a
"good to go" project.  
I mean: we need to have the project in local, in staging, in prod…  
We need to develop on it, to deploy continuously, and so on.

Vagrant is only for development purpose.

# Requirements

## Vagrant plugins

To install a plugin run: `vagrant plugin install PLUGIN_NAME`

* `vagrant-docker-compose`: Allow to use docker-compose as provisioner
* `vagrant-hostmanager`: Allow to update your /etc/hosts
