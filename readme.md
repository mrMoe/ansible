# Ansible for hetzner cx* server

based on Debian9, postfix, dovecot, nginx, mariadb

wildly copied from all over the internetz. Especially

- https://wiki2.dovecot.org/HowTo/VirtualUserFlatFilesPostfix
- https://thomas-leister.de/mailserver-debian-stretch/
- https://wiki.dovecot.org/SSL/DovecotConfiguration
- https://www.postfixbuch.de/

Remark: Work in progress

## TODO

- sftp access
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
    doveadm pw -s ssha256
    doveadm auth test test@synworks.org

## nginx

## postfix

## sftp

## fail2ban

Fail2ban is activated for the ssh port in default configuration
