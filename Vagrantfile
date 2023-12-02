Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu1804"
  # config.vm.network "public_network", use_dhcp_assigned_default_route: true
  config.vm.provider "virtualbox" do |node|
    node.memory = 2048
    node.cpus = 2
  end
  config.vm.provision "shell", inline: <<-EOF
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg -y
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
    sudo swapoff -a
    sudo modprobe overlay
    sudo modprobe br_netfilter
    sudo sysctl -w net.ipv4.ip_forward=1
    sudo sysctl -w net.bridge.bridge-nf-call-ip6tables=1
    sudo sysctl -w net.bridge.bridge-nf-call-iptables=1
    sudo sysctl --system
  EOF

  #master node
  config.vm.define "master" do |master|
    master.vm.hostname = "master"
    master.vm.provision "shell", inline: <<-EOF
      sudo rm /etc/containerd/config.toml
      sudo systemctl restart containerd
      sudo kubeadm init --apiserver-advertise-address 192.168.50.10 --control-plane-endpoint 192.168.50.10
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
      kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
    EOF
    master.vm.provider :virtualbox do |master|
      master.name = "master"
    end
    master.vm.network "public_network", ip: "192.168.50.10"
  end

  worker_ip="192.168.50."
  # worker nodes
  # requires kubeadm join command to combine into cluster with master
  (1..1).each do |i|
    config.vm.define "worker-#{i}" do |worker|
      worker.vm.hostname = "worker-#{i}"
      worker.vm.provision "shell", inline: <<-EOF
        sudo rm /etc/containerd/config.toml
        sudo systemctl restart containerd
      EOF
      worker.vm.provider "virtualbox" do |worker|
        worker.name = "worker-#{i}"
      end
      worker.vm.network "public_network", ip: worker_ip + "#{i+20}"
    end
  end
end