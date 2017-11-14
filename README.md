# Ghost Deploy
One command to start your lab enviroment

## Install Requirement
- [Vagrant 2.0.0+](https://www.vagrantup.com/)
- [Virtualbox 5.1](https://www.virtualbox.org/), [VirtualBox 5.2.0 is not supported for Vagrant 2.0.0](https://github.com/hashicorp/vagrant/issues/9075), [Virtualbox 5.2 support](https://github.com/hashicorp/vagrant/pull/8955)

## Feature
- user`ubuntu` and `root`can ssh machine with `data\key\local_key`
- set user's password in `data\user_password`, pattern `username:password`, default `root:root` and `ubuntu:ubuntu`
- machine with aliyun source
- as use same config OpenSSH Host Keys, ssh a rebuilded machine will not show message bellow
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:1e/0f6/Is1kjnpfjM/m8qFVWfKIgEcWoOXvZZHUZayI.
Please contact your system administrator.
Add correct host key in /home/lau/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/lau/.ssh/known_hosts:24
ECDSA host key for es.io has changed and you have requested strict checking.
Host key verification failed.
```

## Config Guidance
- ~~update `base_path` in `Vagrantfile`, **required**~~
- update `base_private_network_segment` in `Vagrantfile`, **optional**
- update `base_public_network_segment` in `Vagrantfile`, **optional**
- ~~update `vagrant_home_path` in `Vagrantfile`, **required**~~
- generate local key by `ssh-keygen -t rsa` and paste it into `data\key\local_key` and `data\key\local_key.pub`, **required**
- update `boot_up_message` in `Vagrantfile`, **optional**
- config user password in `data\user_password`, pattern `username:password` each line, **optional**
- copy machine config template and modify your own machie name like `lab`, `Vagrantfile` has init `lab` and `elasticsearch` default machine config, **optional**
```ruby
#################### machine config start ####################
  config.vm.define "lab", autostart: false do |machine|
    hostname = "lab"
    machine.vm.box = default_box
    machine.vm.box_url = default_box_url
    machine.vm.hostname = hostname
    machine.vm.post_up_message = "#{hostname}#{boot_up_message}"
    machine.vm.network "private_network", ip: "#{base_network_segment}10"
    # machine.vm.network "public_network", ip: "#{base_public_network_segment}78" #, bridge: "Killer Wireless-n/a/ac 1535 Wireless Network Adapter"

    config.vm.provider :virtualbox do |vb|
      vb.name = hostname
    end

    machine.vm.provision :shell, privileged: false do |s|
      s.inline = <<-SHELL
        echo "#{hostname} shell init done"
      SHELL
    end
  end
#################### machine config end ####################
```

## Usefull Commands
```shell
vagrant up lab

vagrant destroy -f

vagrant suspend lab

vagrant resume lab

vagrant halt

ssh root@172.28.128.10
```
