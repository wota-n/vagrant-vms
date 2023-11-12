Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu1804"
  config.vm.network "public_network", use_dhcp_assigned_default_route: true
  config.vm.provider "virtualbox" do |node|
    node.memory = 2048
    node.cpus = 2
  end
  config.vm.provision "shell", inline: <<-EOF
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg jq -y
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    sudo groupadd docker
    sudo usermod -aG docker $USER
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
    sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
    sudo apt install -y kubeadm kubelet kubectl
    sudo apt-mark hold kubeadm kubelet kubectl
    sed -i 's/ExecStart.*/& --exec-opt native.cgroupdriver=systemd/' /lib/systemd/system/docker.service
    sudo systemctl daemon-reload
    sudo systemctl restart docker
  EOF

  #master node
  config.vm.define "master-node" do |master|
    master.vm.hostname = "master"
    master.vm.provider "virtualbox" do |master|
      master.name = "master"
    end
  end

#worker nodes
  # (1..1).each do |i|
  #   config.vm.define "worker-#{i}" do |node|
  #     node.vm.hostname = "worker-#{i}"
  #     node.vm.provider "virtualbox" do |node|
  #       node.name = "worker-#{i}"
  #     end
  #   end
  # end
end