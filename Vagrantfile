M = 3
W = 1

NODE_NETWORK="192.168.20.0"
POD_NETWORK="10.244.0.0/16"

################
IMAGE_NAME = "centos/8"
if (M > 1) then
L = 2
else
L =0
end if

IP_RANGE=NODE_NETWORK.split(".")[0...-1].join(".")

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vagrant.plugins = "vagrant-hosts"

  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 1
    v.linked_clone = true
  end

  (1..L).each do |i|
    config.vm.define "lb-#{i}" do |lb|
      lb.vm.provider "virtualbox" do |v|
        v.memory = 512
      end
      lb.vm.box = IMAGE_NAME
      lb.vm.network "private_network", ip: "#{IP_RANGE}.#{10+i}"
      lb.vm.hostname = "lb-#{i}"
    end
  end


  (1..M).each do |i|
    config.vm.define "k8s-master-#{i}" do |master|
      master.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
      end
      master.vm.box = IMAGE_NAME
      master.vm.network "private_network", ip: "#{IP_RANGE}.#{20+i}"
      master.vm.hostname = "k8s-master-#{i}"
    end
  end


  (1..W).each do |i|
    config.vm.define "k8s-worker-#{i}" do |w|
      w.vm.provider "virtualbox" do |v|
        v.memory = 1024
      end
      w.vm.box = IMAGE_NAME
      w.vm.network "private_network", ip: "#{IP_RANGE}.#{30+i}"
      w.vm.hostname = "k8s-worker-#{i}"
    end
  end

  config.vm.provision :hosts do |provisioner|
    provisioner.autoconfigure = true
    provisioner.sync_hosts = true
    provisioner.add_host "#{IP_RANGE}.10", ['lb']
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provision/site.yaml"
      ansible.extra_vars = {
        lb_ip: "#{IP_RANGE}.10",
        ip_range: "#{IP_RANGE}",
        m: "#{M}",
        POD_NETWORK: "#{POD_NETWORK}"
      }
    ansible.groups = {
      "lb" => ["lb-[1:2]"],
      "k8s_masters" => ["k8s-master-[1:#{M}]"],
      "k8s_workers" => ["k8s-worker-[1:#{W}]"],
    }
  end

end

