Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu1804"

  config.vm.network "public_network"
  
  (1..3).each do |i|
    config.vm.define "node_vm#{i}" do |node|
      node.vm.provider "virtualbox" do |node|
        node.name = "node_vm#{i}"
      end
    end
  end
end
