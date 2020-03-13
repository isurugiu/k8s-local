##
# Mostly stolen from https://kubernetes.io/blog/2019/03/15/kubernetes-setup-using-ansible-and-vagrant/
##

IMAGE_NAME = "bento/ubuntu-16.04"
N = 3

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    config.vagrant.plugins = ["vagrant-disksize", "vagrant-vbguest"]

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end

    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "public_network", ip: "192.168.1.200", bridge: "Intel(R) Ethernet Connection (2) I218-V"
        master.vm.hostname = "k8s-master"
        master.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.verbose = true
            ansible.extra_vars = {
                node_ip: "192.168.1.200",
            }
        end
    end

    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 2
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "public_network", ip: "192.168.1.#{i + 200}", bridge: "Intel(R) Ethernet Connection (2) I218-V"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.verbose = true
                ansible.extra_vars = {
                    node_ip: "192.168.1.#{i + 200}",
                }
            end
        end
    end
end