# Ansible for hetzner cx* server

based on Debian9, postfix, dovecot, nginx, mariadb

wildly copied from all over the internetz. Especially

- https://wiki2.dovecot.org/HowTo/VirtualUserFlatFilesPostfix
- https://thomas-leister.de/mailserver-debian-stretch/
- https://wiki.dovecot.org/SSL/DovecotConfiguration
- https://www.postfixbuch.de/

Remark: Work in progress

## import_tasks: virtual-domain.yml switches

|                 |                                                                                                                                              |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| db_name         | if db_name is given a database scheme with the given name is created in the mariadb server                                                   |
| db_pass         | if db_name is given a user named after the db_name variable and the given db_pass is created with full access to the formerly created scheme |
| domain          | domain for which a virtual_host configration should be generated                                                                             |
| mail_forward    | all mails for an alias (not mailbox) domain are forwarded to the single given mail addresss                                                  |
| mailbox         | instead of creating a alias postfix configation a mailbox with a imap server config is created                                               |
| redirect_domain | creates a http redirect nginx configuration with a permanent redirect to the given domain                                                    |

## TODO

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

## SFTP

    ansible all -i localhost, -m debug -a "msg={{ 'mypassword' | password_hash('sha512', 'mysecretsalt') }}"

## dovecot

A private namespace configured for maildir storage to hold the mailboxes and a public namespace configured for maildir storage with filesystem layout (/dir/subdir) and per user index-information. The index will be stored in the public dir under the home directories. This allows individual /SEEN information for the public namespace.
Per domain flat-files containing the virtual user's specific parameters, stored in a single passwd-like file. User logins are expected to be in full-qualified e-mail address format: user@domain.tld . Additional parameters may be used to override defaults, such as individual quotas or mailbox formats.
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

### /var/vmail/auth.d/<domain>/passwd

    <user>@<domain>:{SSHA}xxxx:5000:5000::/var/vmail/<domain>/<user>::userdb_quota_rule=*:storage=5G userdb_acl_groups=PublicMailboxAdmins
    doveadm pw -s ssha256 -p testpass
    doveadm auth test test@synworks.org

### mail migration

    larch -a -d -x -f imap://mail.source.com -u sourceUser -p sourcePass -t imap://localhost -U localUser -P localPass

## nginx

## postfix

## sftp

## fail2ban

Fail2ban is activated for the ssh port in default configuration
