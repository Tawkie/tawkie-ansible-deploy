# tawkie-ansible-deploy
Deploy Tawkie using Ansible !

## Usage

Update Galaxy roles :

```
ansible-galaxy install -r requirements.yml --force
```

Run playbook :

```
ansible-playbook -i hosts-staging -e @secrets-staging.enc --ask-vault-pass transverse.yml --tags setup-all -K
```

Existing tags :
- setup-all
- setup-postgres
- setup-ntfy
- setup-ntfy-pam
- setup-ntfy-apt

Update apt packages on all hosts :

```
ansible-playbook -i hosts-all apt-update.yml  -K
```

### SSH

To avoid providing a password for each key, you can use `ssh-agent` :

```
ssh-agent bash        # or another shell
ssh-add ~/.id/rsa     # or another key
ansible-playbook ...
```
