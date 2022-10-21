# ansible-deploy-demo

## Install collections and roles

```shell
ansible-galaxy install -r requirements.yml
```

Basic structure:

```shell
├── ansible.cfg
├── collections
│   └── ansible_collections
│       ├── ansible
│       │   └── posix
│       ├── community
│       │   └── general
│       └── nfaction
│           └── bootstrap
├── docs
├── hosts.yaml
├── README.md
├── requirements.yml
└── roles
    └── docker
```

## Test targets

One can use Proxmox to host a test VM shown [here.](docs/Proxmox-VE-test-host.md)

```shell
ansible all -m ping
```

Run a `bootstrap` against the test host:

```shell
ansible-playbook collections/ansible_collections/nfaction/bootstrap/playbooks/bootstrap.yml

# OR

ansible-playbook playbooks/bootstrap.yml
```

## References

* [Ansible Setup](docs/Ansible-setup.md)