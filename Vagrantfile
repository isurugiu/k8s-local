##
# Mostly stolen from https://kubernetes.io/blog/2019/03/15/kubernetes-setup-using-ansible-and-vagrant/
##
## See https://github.com/chef/bento/issues/1185#issuecomment-611479227 for failing mount fix then run vagrant reload

IMAGE_NAME = "bento/ubuntu-20.04"
BOX_VERSION = "202107.07.0"
K8S_VERSION = "1.21.2"
N = 3

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    config.vagrant.plugins = ["vagrant-disksize", "vagrant-vbguest"]
    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 4
    end

    config.vm.define "k8s-master-#{K8S_VERSION}" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.box_version = BOX_VERSION
        master.vm.network "public_network", ip: "192.168.1.200", bridge: "Realtek PCIe 2.5GbE Family Controller"
        master.vm.hostname = "k8s-master-#{K8S_VERSION}"
        master.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.verbose = true
            ansible.extra_vars = {
                node_ip: "192.168.1.200",
                node_hostname: "k8s-master-#{K8S_VERSION}"
            }
        end
    end

    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 4
    end

    (1..N).each do |i|
        config.vm.define "k8s-node-#{K8S_VERSION}-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.box_version = BOX_VERSION
            node.vm.network "public_network", ip: "192.168.1.#{i + 200}", bridge: "Realtek PCIe 2.5GbE Family Controller"
            node.vm.hostname = "node-#{K8S_VERSION}-#{i}"
            node.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.verbose = true
                ansible.extra_vars = {
                    node_ip: "192.168.1.#{i + 200}",
                    node_hostname: "node-#{K8S_VERSION}-#{i}"
                }
            end
        end
    end
end
