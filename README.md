# Simple mail server setup playbook

This Ansible playbook sets up and configures a simple mail server using:

* [Postfix](https://www.postfix.org/) for sending mail
* [Dovecot](https://www.dovecot.org/) for administering email inboxes
* [SpamAssassin](https://spamassassin.apache.org/) for filtering spam email
* [OpenDKIM](http://www.opendkim.org/) for authenticating sent mail (this is
important to enable sending mail to most large email providers)

Tested on Ubuntu server

## Prerequisites

* Python 3
* Ansible (local machine only)
* SSH
  * Generate an SSH key on your local machine
    * `$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
    * The SSH key should be on the local machine as `~/.ssh/id_rsa`
  * Copy your SSH key to the server
    * `$ ssh-copy-id root@SERVER.IP.ADDRESS` (and enter the root password)

## Instructions

### Change the parameters in the config files to point Ansible at your server

* `./inventory`: Change the server IP address (line 3) from
`SERVER.IPV4.ADDRESS` to the IP address of your server
* `./vars/main.yml`: Change the domain name to the domain name for your email
server. If your mail will be accessed at `mail.mydomain.xyz` then the line
should read `domain: mydomain.xyz`

### Setup Nginx sites

This stage must be done manually for now!

* Run the playbook with the `--tags web` option
  * `$ ansible-playbook -i ./inventory ./main.yml --tags web`
* SSH to the server and configure a site `mail.mydomain.xyz`
* Setup `A` and `AAA` DNS records for your mail subdomain
  * Allow some time for your ISP's DNS servers to update (this can take up to
  several hours)
* On the server, run `$ certbot --nginx` and follow the prompts to generate a
certificate for your mail site

### Run the playbook

* Open a terminal in this directory
* `$ ansible-playbook -i ./inventory ./main.yml`
  * You may have to specify `--ask-become-pass` if you get a permission error

### Setup DNS records

You will need to setup 3 DNS TXT records for your mail to be routed correctly.
These are placed in `/root/dns_TXT` by default, one per line
