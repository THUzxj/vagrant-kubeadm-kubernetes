NUM_WORKER_NODES=2
IP_NW="10.0.0."
IP_START=10

Vagrant.configure("2") do |config|
  config.vm.provision "shell", env: {"IP_NW" => IP_NW, "IP_START" => IP_START}, inline: <<-SHELL
      sudo sed -i "s@http://.*archive.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
      sudo sed -i "s@http://.*security.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
      apt-get update -y
      echo "$IP_NW$((IP_START)) master-node" >> /etc/hosts
      echo "$IP_NW$((IP_START+1)) worker-node01" >> /etc/hosts
      echo "$IP_NW$((IP_START+2)) worker-node02" >> /etc/hosts
  SHELL

  config.vm.box = "generic/ubuntu2004"
  config.vm.box_check_update = true
  config.vm.synced_folder ".", "/vagrant", type: "rsync"

  config.vm.define "master" do |master|
    # master.vm.box = "bento/ubuntu-18.04"
    master.vm.hostname = "master-node"
    master.vm.network "private_network", ip: IP_NW + "#{IP_START}"
    master.vm.provider "virtualbox" do |vb|
        vb.memory = 8192
        vb.cpus = 16
    end
    master.vm.provision "file", source: "./apt-key.gpg", destination: "$HOME/apt-key.gpg"
    master.vm.provision "shell", path: "scripts/common.sh"
    master.vm.provision "shell", path: "scripts/master.sh"
  end

  (1..NUM_WORKER_NODES).each do |i|

  config.vm.define "node0#{i}" do |node|
    node.vm.hostname = "worker-node0#{i}"
    node.vm.network "private_network", ip: IP_NW + "#{IP_START + i}"
    node.vm.provider "virtualbox" do |vb|
        vb.memory = 8192
        vb.cpus = 16
    end
    node.vm.provision "file", source: "./apt-key.gpg", destination: "$HOME/apt-key.gpg"
    node.vm.provision "shell", path: "scripts/common.sh"
    node.vm.provision "shell", path: "scripts/node.sh"
  end

  end
end 
