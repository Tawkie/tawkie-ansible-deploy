# tawkie-ansible-deploy
Deploy Tawkie using [Ansible][ansible-doc] !

Uses a similar approach to [matrix-docker-ansible-deploy][matrix-deploy] and
re-uses a number of roles.

Use `Fully-Qualified Collection Names` (`ansible.builtin.copy` instead of
`copy`), see [linter docs][ansible-fqcn]. Use the [Ansible linter][ansible-linter]
to guaranty style throughout the codebase.

[ansible-doc]: https://docs.ansible.com/ansible/latest/index.html
[matrix-deploy]: https://github.com/spantaleev/matrix-docker-ansible-deploy/
[ansible-fqcn]: https://ansible.readthedocs.io/projects/lint/rules/fqcn/
[ansible-linter]: https://ansible.readthedocs.io/projects/lint/

## Usage

Update Galaxy roles :

```
ansible-galaxy install -r requirements.yml --force
```

Run playbook :

```
ansible-playbook -i hosts-staging -e @secrets-staging.enc --ask-vault-pass transverse.yml --tags setup-all
```

Existing tags (incomplete list, see custom roles in `roles/`):
- setup-all
- setup-postgres
- setup-ntfy
- setup-ntfy-pam
- setup-ntfy-apt

To run on production, use `hosts-prod` AND `secrets-prod.enc`.

## A few examples

Update apt packages on all hosts :

```
ansible-playbook -i hosts-all apt-update.yml  -K
```

Deploy `matrix-signup` service on `staging`, without restarting services :

```
ansible-playbook -i hosts-staging -e @secrets-staging.enc --ask-vault-pass transverse.yml --tags setup-matrix-signup
```

You can then ssh into the server and restart the service with `service tawkie-matrix-signup restart`
and check its logs with `journalctl -xfu tawkie-matrix-signup`.

Restart all services on `staging` :

```
ansible-playbook -i hosts-staging -e @secrets-staging.enc --ask-vault-pass transverse.yml --tags start-all
```

## Editing variables

Hosts information is in `hosts-*` files. Most variables are included in `group_vars/*.yml`.

Secrets are stored using ansible vault. Edit the vault for staging using :

```
ansible-vault edit secrets-staging.enc
```

### SSH

To avoid providing a password for each key, you can use `ssh-agent` :

```
ssh-agent bash        # or another shell
ssh-add ~/.id/rsa     # or another key
ansible-playbook ...
```

### Sending emails

Emails can be a pain. We currently use [Scaleway's transactional emails][scaleway-emails].

The API key should be created for an application with an IAM policy with permissions for sending
mail. If you're having trouble with authentication, you can debug with curl :

```
curl smtps://smtp.tem.scw.cloud:465 --ssl-reqd -v \
   --mail-from "no-reply@tawkie.fr" \
   --mail-rcpt "dest@tawkie.fr" \
   --user "project-id:api-key-private" \
   -T <(echo -e "From: no-reply@tawkie.fr\nTo: dest@tawkie.fr\nSubject: Your Subject\n\nYour email body.")
```

The Scaleway Transaction Email panel can be found [here][scaleway-email-panel].

[scaleway-emails]: https://www.scaleway.com/en/docs/managed-services/transactional-email/how-to/generate-api-keys-for-tem-with-iam/
[scaleway-email-panel]: https://console.scaleway.com/transactional-email/domains
