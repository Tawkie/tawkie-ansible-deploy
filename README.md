# tawkie-ansible-deploy
Deploy Tawkie using [Ansible][ansible-doc] !

Uses a similar approach to [matrix-docker-ansible-deploy][matrix-deploy] and
re-uses a number of roles.

Use `Fully-Qualified Collection Names` (`ansible.builtin.copy` instead of
`copy`), see [linter docs][ansible-fqcn]. Use the [Ansible linter][ansible-linter]
to guaranty style throughout the codebase.

[ansible-doc]: https://docs.ansible.com/ansible/latest/index.html
[matrix-deploy]: https://github.com/spantaleev/matrix-docker-ansible-deploy/
[fqcn]: https://ansible.readthedocs.io/projects/lint/rules/fqcn/
[ansible-linter]: https://ansible.readthedocs.io/projects/lint/

## Usage

Update Galaxy roles :

```
ansible-galaxy install -r requirements.yml --force
```

Run playbook :

```
ansible-playbook -i hosts-staging -e @secrets-staging.enc --ask-vault-pass transverse.yml --tags setup-all -K
```

Existing tags (incomplete list, see custom roles in `roles/`):
- setup-all
- setup-postgres
- setup-ntfy
- setup-ntfy-pam
- setup-ntfy-apt

Update apt packages on all hosts :

```
ansible-playbook -i hosts-all apt-update.yml  -K
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
