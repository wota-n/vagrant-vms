Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu1804"
  config.vm.network "public_network", use_dhcp_assigend_default_route: true
  config.vm.provider "virtualbox" do |node|
    node.memory = 2048
    node.cpus = 2
  end
  config.vm.provision "shell", inline: <<-EOF
    snap install microk8s --classic
    microk8s status --wait-ready
    usermod -a -G microk8s vagrant
  EOF

  #master node
  config.vm.define "master-node" do |master|
    master.vm.hostname = "master"
    master.vm.provider "virtualbox" do |master|
      master.name = "master"
    end
  end

#worker nodes
  (1..3).each do |i|
    config.vm.define "worker-#{i}" do |node|
      node.vm.hostname = "worker-#{i}"
      node.vm.provider "virtualbox" do |node|
        node.name = "worker-#{i}"
      end
    end
  end
end