# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  ## common config
  base_path = "D:/git/ghost-deploy"
  data_base_path = "D:/git/ghost-deploy/data"
  default_box = "ubuntu64"
  # default_box = "ubuntu/xenial64"
  default_box_url = "https://vagrantcloud.com/ubuntu/boxes/xenial64/versions/20171028.0.0/providers/virtualbox.box"
  box_check_update = false
  sources_list_path = "/tmp/aliyun_sources.list"
  temp_ssh_folder_path = "/tmp/tempssh"
  temp_id_rsa_path = "/tmp/id_rsa"
  boot_up_message = ", hi laudukang"
  # , autostart: false

  config.vm.define "kibana" do |machine|
    hostname = "kibana"
    machine.vm.box = default_box
    machine.vm.box_url = default_box_url
    machine.vm.hostname = hostname
    machine.vm.post_up_message = "#{hostname}#{boot_up_message}"
    machine.vm.network "private_network", ip: "172.28.128.10"


    config.vm.provider :virtualbox do |vb|
      vb.name = hostname
    end

    machine.vm.provision :shell, privileged: false do |s|
      s.inline = <<-SHELL
        echo "#{hostname} shell init done"
      SHELL
    end
  end

  config.vm.define "elasticsearch" do |machine|
    hostname = "elasticsearch"
    machine.vm.box = default_box
    machine.vm.box_url = default_box_url
    machine.vm.hostname = hostname
    machine.vm.post_up_message = "#{hostname}#{boot_up_message}"
    machine.vm.network "private_network", ip: "172.28.128.11"


    config.vm.provider :virtualbox do |vb|
      vb.name = hostname
    end

    machine.vm.provision :shell, privileged: false do |s|
      s.inline = <<-SHELL
        echo "#{hostname} shell init done"
      SHELL
    end
  end

  # global config start
  config.vm.provider :virtualbox do |vb|
    vb.gui = false
    vb.memory = "1024"
    vb.cpus = 1
  end

  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provision "file", source: "#{data_base_path}/aliyun_sources.list", destination: "#{sources_list_path}"
  config.vm.provision "file", source: "#{data_base_path}/ssh/.", destination: "#{temp_ssh_folder_path}"
  config.vm.provision "file", source: "#{data_base_path}/key/local_key", destination: "#{temp_id_rsa_path}"

  config.ssh.forward_agent    = true
  config.ssh.insert_key       = false
  config.ssh.private_key_path =  ["#{data_base_path}/key/local_key"] #"C:/Users/lau/.vagrant.d/insecure_private_key",

  config.vm.provision :shell, privileged: false do |s|
    ssh_key = File.readlines("#{data_base_path}/key/local_key").first.strip
    ssh_pub_key = File.readlines("#{data_base_path}/key/local_key.pub").first.strip
    s.inline = <<-SHELL
      echo #{ssh_pub_key} >> /home/ubuntu/.ssh/authorized_keys
      sudo bash -c "echo #{ssh_pub_key} >> /root/.ssh/authorized_keys"

      sudo bash -c "cp #{temp_id_rsa_path} /home/ubuntu/.ssh/id_rsa"
      sudo bash -c "echo #{ssh_pub_key} >>  /home/ubuntu/.ssh/id_rsa.pub"

      sudo bash -c "mv #{temp_id_rsa_path} /root/.ssh/id_rsa"
      sudo bash -c "echo #{ssh_pub_key} >>  /root/.ssh/id_rsa.pub"

      sudo bash -c "chmod 0600 /home/ubuntu/.ssh/id_rsa"
      sudo bash -c "chmod 0644 /home/ubuntu/.ssh/id_rsa.pub"
			sudo bash -c "chown ubuntu:ubuntu /home/ubuntu/.ssh/id_rsa*"

      sudo bash -c "chmod 0600 /root/.ssh/id_rsa"
      sudo bash -c "chmod 0644 /root/.ssh/id_rsa.pub"
			sudo bash -c "chown root:root /root/.ssh/id_rsa*"

      sudo bash -c "mv #{sources_list_path} /etc/apt/sources.list"

      sudo bash -c "mv #{temp_ssh_folder_path}/* /etc/ssh/ && rm -rf #{temp_ssh_folder_path}"
      sudo bash -c "chmod 600 /etc/ssh/ssh_host_*key"
      sudo bash -c "chmod 644 /etc/ssh/ssh_host_*key.pub"
      sudo service ssh restart

      echo "shell init done"
    SHELL
  end
  # global config end

end
