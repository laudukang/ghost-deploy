# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  #################### common machine config start ####################
  base_path = "./"
  base_data_path = "#{base_path}data/"
  boot_up_message = ", hi laudukang"
  base_private_network_segment = "172.28.128."
  base_public_network_segment  = "10.10.201."

  default_box = "ubuntu/xenial64"
  default_box_url = "http://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-vagrant.box"
  # default_box = "ubuntu/xenial32"
  # default_box_url = "http://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-i386-vagrant.box"
  box_check_update = false
  sources_list_path = "/tmp/aliyun_sources.list"
  temp_ssh_folder_path = "/tmp/tempssh"
  temp_id_rsa_path = "/tmp/id_rsa"
  #, autostart: false
  #################### common machine config end ####################

  #################### machine config start ####################
  config.vm.define "lab", autostart: false do |machine|
    hostname = "lab"
    machine.vm.box = default_box
    machine.vm.box_url = default_box_url
    machine.vm.hostname = hostname
    machine.vm.post_up_message = "#{hostname}#{boot_up_message}"
    machine.vm.network "private_network", ip: "#{base_private_network_segment}10"
    machine.vm.network "public_network", ip: "#{base_public_network_segment}78"#, bridge: ""

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

  #################### machine config start ####################
  config.vm.define "es", autostart: false do |machine|
    hostname = "elasticsearch"
    machine.vm.box = default_box
    machine.vm.box_url = default_box_url
    machine.vm.hostname = hostname
    machine.vm.post_up_message = "#{hostname}#{boot_up_message}"
    machine.vm.network "private_network", ip: "#{base_private_network_segment}11"

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


  # NODE_COUNT = 2
  #################### batch config node start ####################
  # (1..NODE_COUNT).each do |i|
  #   config.vm.define "node#{i}" do |machine|
  #     hostname = "node#{i}"
  #     machine.vm.box = default_box
  #     machine.vm.box_url = default_box_url
  #     machine.vm.hostname = hostname
  #     machine.vm.post_up_message = "#{hostname}#{boot_up_message}"
  #     machine.vm.network "private_network", ip: "#{base_private_network_segment}#{i + 30}"
  #
  #     config.vm.provider :virtualbox do |vb|
  #       vb.name = hostname
  #     end
  #
  #     machine.vm.provision :shell, privileged: false do |s|
  #       s.inline = <<-SHELL
  #         echo "#{hostname} shell init done"
  #       SHELL
  #     end
  #   end
  # end
  #################### batch config node end ####################


  #################### global machine config start ####################
  config.vm.provider :virtualbox do |vb|
    vb.gui = false
    vb.memory = "1024"
    vb.cpus = 1
    vb.customize ["modifyvm", :id, "--nictype1", "Am79C973", '--cableconnected1', 'on']
    vb.customize ["modifyvm", :id, "--nictype2", "Am79C973", '--cableconnected2', 'on']#, "--nicpromisc2", "allow-all"
    vb.customize ["modifyvm", :id, "--nictype3", "virtio", '--cableconnected3', 'on']#, "--nicpromisc3", "allow-all"
  end

  config.vm.box_url = "#{default_box_url}"
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provision "file", source: "#{base_data_path}aliyun_sources.list", destination: "#{sources_list_path}"
  config.vm.provision "file", source: "#{base_data_path}ssh/.", destination: "#{temp_ssh_folder_path}"
  config.vm.provision "file", source: "#{base_data_path}key/local_key", destination: "#{temp_id_rsa_path}"

  config.ssh.forward_agent    = false
  config.ssh.insert_key       = false
  config.ssh.private_key_path = ["#{base_data_path}key/local_key"]

  config.vm.provision :shell, privileged: true do |s|
    ssh_key = File.readlines("#{base_data_path}key/local_key").first.strip
    ssh_pub_key = File.readlines("#{base_data_path}key/local_key.pub").first.strip

    s.inline = <<-SHELL
      echo #{ssh_pub_key} >> /home/ubuntu/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /root/.ssh/authorized_keys

      cp #{temp_id_rsa_path} /home/ubuntu/.ssh/id_rsa
      echo #{ssh_pub_key} >>  /home/ubuntu/.ssh/id_rsa.pub

      mv #{temp_id_rsa_path} /root/.ssh/id_rsa
      echo #{ssh_pub_key} >>  /root/.ssh/id_rsa.pub

      chmod 0600 /home/ubuntu/.ssh/id_rsa
      chmod 0644 /home/ubuntu/.ssh/id_rsa.pub
			chown ubuntu:ubuntu /home/ubuntu/.ssh/id_rsa*

      chmod 0600 /root/.ssh/id_rsa
      chmod 0644 /root/.ssh/id_rsa.pub
			chown root:root /root/.ssh/id_rsa*

      mv #{sources_list_path} /etc/apt/sources.list

      mv #{temp_ssh_folder_path}/* /etc/ssh/ && rm -rf #{temp_ssh_folder_path}
      chmod 600 /etc/ssh/ssh_host_*key
      chmod 644 /etc/ssh/ssh_host_*key.pub
      service ssh restart

      echo "shell init done"
    SHELL
  end

  if File.file?("#{base_data_path}user_password")
      config.vm.provision :shell, privileged: false do |s|
        user_password = File.readlines("#{base_data_path}user_password").first.strip

        s.inline = <<-SHELL
          sudo bash -c "echo #{user_password} | chpasswd"

          echo "shell init user password done"
        SHELL
      end
  end

  # default router
  # config.vm.provision :shell, run: "always", privileged: true do |s|
  #   s.inline = <<-SHELL
  #     route add default gw #{base_public_network_segment}1
  #     eval `route -n | awk '{ if ($8 =="enp0s3" && $2 != "0.0.0.0") print "route del default gw " $2; }'`
  #
  #     echo "nameserver 8.8.8.8" >> /etc/resolvconf/resolv.conf.d/base
  #     echo "nameserver 8.8.4.4" >> /etc/resolvconf/resolv.conf.d/base
  #     resolvconf -u
  #
  #     echo "shell init router done"
  #   SHELL
  # end

  #################### global machine config end ####################

end
