# Ghost Deploy
One command to start your lab enviroment

## Install Requirement
- [Vagrant](https://www.vagrantup.com/)
- [Virtualbox](https://www.virtualbox.org/)

## Feature
- User`ubuntu` and `root`can ssh machine with `data\key\local_key`
- As use config OpenSSH Host Keys, ssh a rebuilded machine will not show message bellow
```
The authenticity of host 'i.io (172.28.128.10)' can't be established.
ECDSA key fingerprint is SHA256:nP2kLqSWnL4Hp6l6fpFPCke+rnYNLcP9U+PNfD52CYs.
Are you sure you want to continue connecting (yes/no)? yes
```

## Config Guidance
- change your `base_path` in `Vagrantfile`, **required**
- change your `base_network_segment` in `Vagrantfile`, **required**
- change your `vagrant_home_path` in `Vagrantfile`, **required**
- generate your local key by `ssh-keygen -t rsa` and paste it into `data\key\local_key` and `data\key\local_key.pub`, **required**
- change your `boot_up_message` in `Vagrantfile`, **optional**
- machine config template
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
