# -*- mode: ruby -*-
# vim: set ft=ruby:

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/bionic64"
  config.vm.box_version = "20200916.0.0"
  config.ssh.forward_agent = true
  config.vm.synced_folder '.', '/vagrant', disabled: true

  # setting to not insert random generated key in the box
  config.ssh.insert_key = false
  # config.ssh.username = "cassandra"
  # copy our private key to the box so thats the one used
  config.ssh.private_key_path = ["~/.ssh/id_rsa", "~/.vagrant.d/insecure_private_key"]

  # copy the public key into the box. 
  config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "3072"
    vb.cpus = "1"
    vb.customize [
      "modifyvm", :id,
      "--ioapic", "on",
      "--pae", "on",
      "--hwvirtex", "on",
      "--vtxvpid", "on",
      "--vtxux", "on",
      "--nestedpaging", "off",
      "--audio", "none",
      "--vrde", "off",
      "--graphicscontroller", "vmsvga"
    ]
  end

  config.vm.define "cassandra1" do |cassandra1|
    cassandra1.vm.hostname = "cassandra1.node.local"
    cassandra1.vm.network "private_network", ip: "192.168.60.10"
    
    cassandra1.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--name", "cassandra1", "--cpuexecutioncap", "100"]
    end

  end

  config.vm.define "cassandra2" do |cassandra2|
    cassandra2.vm.hostname = "cassandra2.node.local"
    cassandra2.vm.network "private_network", ip: "192.168.60.20"

    cassandra2.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--name", "cassandra2", "--cpuexecutioncap", "100"]
    end
  
  end

  config.vm.define "cassandra3" do |cassandra3|
    cassandra3.vm.hostname = "cassandra3.node.local"
    cassandra3.vm.network "private_network", ip: "192.168.60.30"

    cassandra3.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--name", "cassandra3", "--cpuexecutioncap", "100"]
    end 

  end

  config.vm.define "cassandra4" do |cassandra4|
    cassandra4.vm.hostname = "cassandra4.node.local"
    cassandra4.vm.network "private_network", ip: "192.168.60.40"

    cassandra4.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--name", "cassandra4", "--cpuexecutioncap", "100"]
    end 

  end

  $script = <<-SCRIPT

  echo "DOWNLOADING Datastax Cassandra..."
  
  > /etc/apt/sources.list.d/datastax.sources.list && echo "deb https://debian.datastax.com/enterprise stable main" | sudo tee -a /etc/apt/sources.list.d/datastax.sources.list
  curl -sL https://debian.datastax.com/debian/repo_key 2>/dev/null | sudo APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=1 apt-key add - 2>/dev/null
  
  sudo DEBIAN_FRONTEND=noninteractive apt-get update -y
  sudo DEBIAN_FRONTEND=noninteractive apt-get install -y openjdk-8-jdk unzip dse-full=6.7.8-1 dse=6.7.8-1 dse-libgraph=6.7.8-1 dse-libsolr=6.7.8-1 dse-libtomcat=6.7.8-1 dse-liblog4j=6.7.8-1 dse-libspark=6.7.8-1 dse-libcassandra=6.7.8-1 dse-libhadoop2-client-native=6.7.8-1 dse-libhadoop2-client=6.7.8-1


  echo "DOWNLOADING Datastax Cassandra labwork..."

  curl -S https://s3.amazonaws.com/datastaxtraining/files/ds210-6.0-labwork.zip --output ds210-6.0-labwork.zip 2>/dev/null

  SCRIPT

  config.vm.provision "shell", inline: $script

end

