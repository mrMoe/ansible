# Ansible for hetzner cx* server

copied from all over the internetz. Especially

- https://wiki2.dovecot.org/HowTo/VirtualUserFlatFilesPostfix
- https://thomas-leister.de/mailserver-debian-stretch/
- https://wiki.dovecot.org/SSL/DovecotConfiguration
- https://www.postfixbuch.de/

Remark: Work in progress

## dovecot

A private namespace configured for maildir storage to hold the mailboxes and a public namespace configured for maildir storage with filesystem layout (/dir/subdir) and per user index-information. The index will be stored in the public dir under the home directories. This allows individual /SEEN information for the public namespace.
Per domain flat-files containing the virtual user's specific parameters, stored in a single passwd-like file. User logins are expected to be in full-qualified e-mail address format: user@domain.tld . Additional parameters may be used to override defaults, such as individual quotas or mailbox formats.
The auth service is configured to run in the doveauth user context. Therefore the directory auth.d/ and its content will be owned by this user, while mails / ACLs / Sieve-Scripts, will be accessed using the vmail context specified in the passwd-file. To keep directory permissions simple these will be stored seperately under the mails/ tree.

  /var/vmail/auth.d/<domain>/passwd
  <user>@<domain>:{SSHA}xxxx:5000:5000::/var/vmail/<domain>/<user>::userdb_quota_rule=*:storage=5G userdb_acl_groups=PublicMailboxAdmins
  doveadm auth test test@synworks.org
  doveadm pw -s ssha256
  openssl s_client -starttls imap -ssl3 -connect imap.example.com:143

## nginx

## postfix

## sftp