# ansible-vault lookup module
This is a lookup module for secrets stored in [HashiCorp Vault](https://vaultproject.io/).

The plugin is currently written for ansible 2.0 but a working version for ansible 1.9 exists under the 1.0.0 tag.

### Installation
lookup plugins can be loaded from several different locations similar to $PATH, see [docs](http://docs.ansible.com/ansible/intro_configuration.html#lookup-plugins).

### Usage
The address to the Vault server and the auth token are fetched from environment variables

    export VAULT_ADDR=http://192.168.33.10:8200/
    export VAULT_TOKEN=56f48aef-8ad3-a0c4-447b-8e96990776ff

if VAULT_TOKEN is not set, vault.py will retrieve the auth token from $HOME/.vault-token (allows for adhoc auth by users)
an exception is thrown if both VAULT_TOKEN and $HOME/.vault-token do not yield a GUID format token.

ansible-vault then works as any other lookup plugin.

```yaml
- debug: msg="{{ lookup('vault', 'secret/foo', 'value') }}"
```

```yaml
# templates/example.j2

# Generic secrets
{{ lookup('vault', 'secret/hello').value }} # world
# Specify field inside lookup
{{ lookup('vault', 'secret/hello', 'value') }} # world

# Dynamic secrets
{% set aws = lookup('vault', 'aws/creds/deploy') %}
access_key = {{ aws.access_key }} # AKSCAIZSFSYHFGA
secret_key = {{ aws.secret_key }} # 4XSLxDUS+hyXgoIHEhCKExHDGAJDHFiUA/adi
```

### What's the difference between `ansible-vault` and `hashi_vault`

- No external dependencies. hashi_vault requires hvac
- Uses the same environment variables as vault itself
- Quicker update cycle
- Supports dynamic secrets
- Supports custom fields

