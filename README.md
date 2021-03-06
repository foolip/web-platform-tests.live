# [web-platform-tests.live](http://web-platform-tests.live)

A live version of [wpt](https://github.com/web-platform-tests/wpt)

This repo contains a deployment automation for running a live instance of the
web platform tests project that people can visit and run file by file in their
favorite web browser.

## Getting Started

1. Download and install [VirtualBox](https://www.virtualbox.org/) and
   [Vagrant](https://www.vagrantup.com/)
2. Run `vagrant up` from the root of this repository
3. Visit http://172.30.1.5/ in a web browser

### Overview

This project uses Ansible to manage and apply instructions for configuration a
computer to run the web-platform-tests server. More detail on this process is
available in the `provisioning/` directory.

### Production

This project is deployed to http://web-platform-tests.live. The necessary
infrastructure (e.g. server, DNS configuration, status monitoring) is provided
by [Bocoup](https://bocoup.com). It is managed in an external project,
[infrastructure-web-platform](https://github.com/bocoup/infrastructure-web-platform/tree/master/terraform/projects/web-platform-tests-live).

To access the production system, first request a copy of the
`web_platform_test_live.pem` file from infrastructure+web-platform@bocoup.com.
Using that, the following command will deploy this project the production
server:

```
$ ansible-playbook provisioning/provision.yml -i provisioning/inventory/production --key-file=path/to/web_platform_test_live.pem
```

### Monitoring

Monitoring is provided by AWS Cloudwatch. If the index page `/` returns a non
2XX or 3XX status for more then 1 minute, an e-mail will be sent to
infrastructure+web-platform@bocoup.com noting the website has entered the Alert
status.

### Development

This project uses Vagrant for local development. Running `vagrant up` should
create a Ubuntu virtual machine and run the `playbook.yml` provisioning script.
If you need to debug the running server, you can log into it using `vagrant
ssh`.

Once connected to the development server, the following commands can be used to
alter the status of the `wpt` service.

```
## Stop the service (it will be running by default)
$ sudo systemctl stop wpt

## Start the service
$ sudo systemctl start wpt

# Check the status of the service
$ systemctl status wpt

# Check the logs emitted from the wpt service
$ sudo journalctl -f -u wpt
```

You can use the following command to re-apply the configuration to the
development system:

```
$ vagrant provision
```

To completely white out the Vagrant machine and build a new one from scratch,
you can use the following command.

```
$ vagrant destroy -f && vangrant up
```
