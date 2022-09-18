# Vagrant (Cheatsheet)

## Cheatsheet:

### What is Vagrant?

Vagrant is an open-source tool that helps us to automate the creation and management of Virtual Machines.

### Finding a Vagrant box

Head to [Vagrant Cloud](https://app.vagrantup.com/boxes/search) to discover vagrant boxes. Write the following `ruby` code into `Vagrantfile`. Do note that Vagrant's "global" folder is at `~/.vagrant.d`.

```rb
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_download_insecure=true
end
```

### Useful Vagrant Commands

|Commands|Purpose|
|---|---|
|`vagrant box list`| List all the boxes that are installed into Vagrant|
|`vagrant global-status --prune`| State of all Vagrant environments (not rely on cache)|
|`vagrant halt name`| Shuts down the machine|
|`vagrant package`| To create a package.box file (contains the full compressed virtual machine) in the same folder|

### Troubleshooting Vagrant

If obtained the error `A host only network interface you're attempting to configure via DHCP already has a conflicting host only adapter with DHCP enabled. The  DHCP on this adapter is incompatible with the DHCP settings. Two host only network interfaces are not allowed to overlap, and each host only network interface can have only one DHCP server. Please reconfigure your host only network or remove the virtual machine  
using the other host only network.`, add the following code to the top of the Vagrant file.

```rb
class VagrantPlugins::ProviderVirtualBox::Action::Network
  def dhcp_server_matches_config?(dhcp_server, config)
    true
  end
end
```