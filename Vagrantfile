Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.provision "docker"
  config.vm.provision "shell", path: "kubeadm.sh"
  config.vm.provision "shell", inline: <<-SHELL
  apt-get install -y avahi-daemon libnss-mdns
SHELL

  config.vm.define "master" do |subconfig|
    subconfig.vm.hostname = "MASTER"
    subconfig.vm.network :private_network, ip: "192.168.188.10"
    subconfig.vm.provision "shell",
      inline: "sudo cp /vagrant/{db-deployment.yml,db-service.yml,web-deployment.yml,web-service.yml,kube-flannel.yml,kubernetes-dashboard.yml,dashboard-role.yml} ."
  end

  config.vm.define "node1" do |subconfig|
    subconfig.vm.hostname = "NODE1"
    subconfig.vm.network :private_network, ip: "192.168.188.11"
  end

  config.vm.define "node2" do |subconfig|
    subconfig.vm.hostname = "NODE2"
    subconfig.vm.network :private_network, ip: "192.168.188.12"
  end
end
