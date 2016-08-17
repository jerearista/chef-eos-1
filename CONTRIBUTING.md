# eos Cookbook for Arista EOS

The eos cookbook simplifies management of [Arista](https://www.arista.com/) EOS network devices.  Arista EOS uses the standard CentOS 32-bit Chef client.  By including the eos::default recipe in your runlist performs the following actions needed on EOS:
- Relocate /etc/chef to /petsist/sys/chef with a symlink back to /etci/chef 
- Enable eAPI (‘management api http-commands’) with unix-sockets as the transport in the running-config
- Adds/enhances several ohai plugins

# Requirements

This cookbook is designed and tested with Chef 12 and EOS 4.15. Other versions
are likely to work but are not fully tested at this time.

  - Arista EOS 4.15 or greater
  - Chef client 32-bit RPM for RedHat/CentOS
  - Arista Ruby client for eAPI (rbeapi) rubygem and dependencies:
    - rbeapi 0.6.0 or greater which requires:
      - netaddr
      - net_http_unix
      - inifile
  - TestKitchen requires Vagrant and VirtualBox

# Development Environment

- Download and install the [ChefDK](https://downloads.chef.io/chef-dk/)
- `eval "$(chef shell-init bash)"`
- `git clone ...`
- `cd chef-eos`

## Guard

In a separate window, you can run Guard to analyze file changes on-write.

- `cd <path/to>/chef-eos`
- `chef gem install guard-foodcritic`
- `chef exec guard`

# Testing

Verify your environment by running the unit and kitchen tests before making changes.

## Style checks

- `rake style`

## Unit tests

- `rake unit`

## System tests - TestKitchen

- Download Vagrant VirtualBox image of vEOS from [Arista Software Download](https://www.arista.com/en/support/software-download) (Free login required).  Navigate to vEOS --> vEOS-lab-<version>-virtualbox.box
- Add the box to your local inventory
  ```
  vagrant box add --name vEOS-4.16.7M ~/Downloads/vEOS_4.16.7M_virtualbox.box
  ```
- Add the vEOS version to .kitchen.yml
  ```
  platforms:
    - name: vEOS-4.16.7M
      driver:
        vagrantfiles:
          - vagrantfiles/veos.rb
  ```
- Verify TestKitchen config
  ```
  $ kitchen list
  Instance         Driver   Provisioner  Verifier  Transport  Last Action
  veos-vEOS-4167M  Vagrant  ChefZero     Busser    Ssh        <Not Created>
  ```
- [Download the Chef client](https://downloads.chef.io/chef-client/redhat/) for RedHat/CentOS (i386)
  wget --no-check-certificate -O chef-12.13.30-1.el6.i386.rpm https://packages.chef.io/stable/el/6/chef-12.13.30-1.el6.i386.rpm
  NOTE: This is a temporary workaround until the following 2 PRs get released:
    - https://github.com/chef/mixlib-install/pull/127
    - https://github.com/chef/omnitruck/pull/192
  - vagrantfiles/veos.rb will handle copying the rpm to the switch.
- Run TestKitchen
  - `kitchen create [4167M]`
  - Optional for debugging: `kitchen login [4167M]`
  - `kitchen converge [4167M]`
  - `kitchen verify [4167M]`
  - `kitchen destroy [4167M]`

# Contributing

Community contributions are welcome.  Please ensure all pull-requests include spec tests.

# Authors & Support

For support, please open a GitHub issue.  This module is maintained by Arista [EOS+ Consulting Services](mailto://eosplus-dev@arista.com). Commercial support options are available upon request.

# License

All files in this package are covered by the included [license](LICENSE) unless otherwise noted.