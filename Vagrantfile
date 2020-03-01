Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  # vagrant plugin install vagrant-hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_guest = true
  config.hostmanager.manage_host = true

  # vagrant plugin install vagrant-proxyconf
  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://10.0.2.2:3128/"
    config.proxy.https    = "http://10.0.2.2:3128/"
    config.proxy.no_proxy = "localhost,127.0.0.1,10.0.2.15,10.0.0.0/8,.kubevm-dev.com,192.168.50.0/24"
  end  

  config.vm.define "packstack" do |v|
    v.vm.hostname = "packstack.kubevm-dev.com"
    v.vm.network "private_network", ip: "192.168.50.14"
    v.vm.provider "virtualbox" do |vb|
      vb.name = "kubevm-dev-vm-packstack"
      # vb.memory = 12288
      vb.memory = 16384
      vb.cpus = 4

      # allow hostname resolution including /etc/host content for the host
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      # enable promiscuous mode
      vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]      
      #enable nested virtualization
      vb.customize ["modifyvm", :id, "--nested-hw-virt", "on"]      
    end

    # increase the disk size
    # vagrant plugin install vagrant-disksize 
    v.disksize.size = '150GB'

    v.vm.provision "ansible" do |ansible|
      ansible.limit = "all,localhost"
      ansible.playbook = "playbooks/packstack.yaml"
      ansible.host_vars = {
        "packstack" => {"packstack_private_net_iface" => "eth0",
                        "packstack_private_net_ipaddr" => "10.0.2.15",
                        "packstack_public_net_iface" => "eth1",
                        "packstack_public_net_ipaddr" => "192.168.50.14",
                        "packstack_public_net_netmask" => "255.255.255.0",
                        "packstack_public_net_pool_start" => "192.168.50.200",
                        "packstack_public_net_pool_end" => "192.168.50.220",
                        "packstack_public_net_gateway" => "192.168.50.1",
                        "packstack_public_net_dns" => "192.168.50.2"
                       }
      }
      # ansible.verbose = "-vvv"
    end

  end

  config.vm.define "minikube" do |v|
    v.vm.hostname = "minikube.kubevm-dev.com"
    v.vm.network "private_network", ip: "192.168.50.12"
    v.vm.provider "virtualbox" do |vb|
      vb.name = "kubevm-dev-minikube"
      # vb.memory = 4096
      vb.memory = 8192
      vb.cpus = 2

      # allow hostname resolution including /etc/host content for the host
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end

    v.vm.provision "ansible" do |ansible|
      ansible.limit = "all,localhost"
      ansible.playbook = "playbooks/minikube.yaml"
      # ansible.verbose = "-vvv"
    end
  end

end