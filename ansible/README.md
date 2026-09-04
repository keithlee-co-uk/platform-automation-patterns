## Ansible usage

Assuming the base machine has been installed with a compatable version of linux, e.g. Debian  
and we have a user `keith` and the key as a authrozed_key with key access, we can configure ansible with the following command from the ansible.   
`ANSIBLE_CONFIG=ansible-bootstrap.cfg ansible-playbook --ask-become-pass bootstrap.yaml`
