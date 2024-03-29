# Contents

This Vagrant box contains:

* [Terraform](https://www.terraform.io)
* [Docker](https://www.docker.com/)
* [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide)


# Installation

It's as simple as `vagrant up`, but install the vbguest plugin before.

```
vagrant plugin install vagrant-vbguest
vagrant plugin install vagrant-disksize
vagrant plugin install vagrant-multi-putty
vagrant plugin install vagrant-env
vagrant up
```


# Configuration

Put AWS credentials in `~/.aws/credentials`.


# Google Drive

This box maps `~/Google Drive/vagrant-work` on the host to `~/work` in the VM.

```bash
#.env
GIT_NAME=
GIT_EMAIL=
```