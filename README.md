# tawkie-ansible-deploy
Deploy Tawkie using Ansible !

## Usage

Update Galaxy roles :

```
ansible-galaxy install -r requirements.yml --force
```

Run playbook :

```
ansible-playbook -i hosts-staging -e @secrets-staging.enc --ask-vault-pass transverse.yml --tags setup-all
```
