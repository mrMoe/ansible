# Ansible for hetzner cx* server

based on Debian9, postfix, dovecot, nginx, mariadb

wildly copied from all over the internetz. Especially

- https://wiki2.dovecot.org/HowTo/VirtualUserFlatFilesPostfix
- https://thomas-leister.de/mailserver-debian-stretch/
- https://wiki.dovecot.org/SSL/DovecotConfiguration
- https://www.postfixbuch.de/

Remark: Work in progress

## import_tasks: playbook-virtual-domain.yml switches

|                 |                                                                                                                                              |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| db_name         | if db_name is given a database scheme with the given name is created in the mariadb server                                                   |
| db_pass         | if db_name is given a user named after the db_name variable and the given db_pass is created with full access to the formerly created scheme |
| mail_forward    | all mails for an alias (not mailbox) domain are forwarded to the single given mail addresss                                                  |
| mailbox         | instead of creating a alias postfix configation a mailbox with a imap server config is created                                               |
| mydomain        | postfix domain origin                                                                                                                        |
| myhostname      | postfix hostname identity                                                                                                                    |
| redirect_domain | creates a http redirect nginx configuration with a permanent redirect to the given domain                                                    |
| virtual_domain  | domain for which a virtual_host configration should be generated                                                                             |

## TODO

- umlaut domains
- monitoring, alerting
- spamschutz
- roundcube
- postfix spf
- ssl
  - sudo apt-get install certbot python-certbot-nginx
  - certbot certonly --standalone -d mail.mydomain.com
  - https://scaron.info/blog/debian-mail-postfix-dovecot.html
  - dovecut ssl
  - postfix ssl
- firewall
- vault

## ansible

|                                 |                                                               |
| ------------------------------- | ------------------------------------------------------------- |
| ansible -m setup HOSTNAME       | a list of all of the ansible_ variables                       |
| ansible-playbook -i HOSTNAME    | specify inventory host path or comma separated host list      |
| ansible-playbook --step         | one-step-at-a-time: confirm each task before running          |
| ansible-playbook --syntax-check | perform a syntax check on the playbook, but do not execute it |

## dovecot

A private namespace configured for maildir storage to hold the mailboxes and a public namespace configured for maildir storage with filesystem layout (/dir/subdir) and per user index-information. The index will be stored in the public dir under the home directories. This allows individual /SEEN information for the public namespace.
Per domain flat-files containing the virtual user's specific parameters, stored in a single passwd-like file. User logins are expected to be in full-qualified e-mail address format: user@virtual_domain.tld . Additional parameters may be used to override defaults, such as individual quotas or mailbox formats.
The auth service is configured to run in the doveauth user context. Therefore the directory auth.d/ and its content will be owned by this user, while mails / ACLs / Sieve-Scripts, will be accessed using the vmail context specified in the passwd-file. To keep directory permissions simple these will be stored seperately under the mails/ tree.

    openssl s_client -starttls imap -connect localhost:143
    CAPABILITY
    1 LOGIN user@example.com PASS
    2 LIST [flags] [folder separator] [search term]
    3 STATUS [mailbox] [flags]
    4 SELECT [mailbox]
    5 FETCH [first]:[last] flags
    6 FETCH [mail number] body[header]
    7 FETCH [mail number] body[text]
    9 LOGOUT

### /var/vmail/auth.d/<virtual_domain>/passwd

    doveadm pw -s ssha256 -p testpass   # gemerate hashed password
    <user>@<virtual_domain>:{SSHA}hashed-password:5000:5000::/var/vmail/<virtual_domain>/<user>::userdb_quota_rule=*:storage=5G userdb_acl_groups=PublicMailboxAdmins
    doveadm auth <user> <user>@<virtual_domain> # test authentication

### mail migration

    larch -a -d -x -f imap://mail.source.com -u sourceUser -p sourcePass -t imap://localhost -U localUser -P localPass

## nginx

## postfix

## SFTP

Create password for SFTP user

    ansible all -i localhost, -m debug -a "msg={{ 'mypassword' | password_hash('sha512', 'mysecretsalt') }}"

## fail2ban

Fail2ban is activated for the ssh port in default configuration
