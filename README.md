# Ghost Deploy
One command to start your lab enviroment

## Install Requirement
- [Vagrant](https://www.vagrantup.com/)
- [Virtualbox](https://www.virtualbox.org/)

## Feature
- User`ubuntu` and `root`can ssh machine with `data\key\local_key`
- As use same config OpenSSH Host Keys, ssh a rebuilded machine will not show message bellow
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
- change your `base_path` in `Vagrantfile`, **required**
- change your `base_network_segment` in `Vagrantfile`, **required**
- change your `vagrant_home_path` in `Vagrantfile`, **required**
- generate your local key by `ssh-keygen -t rsa` and paste it into `data\key\local_key` and `data\key\local_key.pub`, **required**
- change your `boot_up_message` in `Vagrantfile`, **optional**
- copy machine config template and modify your own machie name like `lab`, `Vagrantfile` has init `lab` and `elasticsearch` default machine config
```ruby
#################### machine config start ####################
  config.vm.define "lab", autostart: false do |machine|
    hostname = "lab"
    machine.vm.box = default_box
    machine.vm.box_url = default_box_url
    machine.vm.hostname = hostname
    machine.vm.post_up_message = "#{hostname}#{boot_up_message}"
    machine.vm.network "private_network", ip: "#{base_network_segment}10"

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

ssh root@172.28.128.10
```
