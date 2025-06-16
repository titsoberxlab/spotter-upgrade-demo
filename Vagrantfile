$ansible_centos7 = <<-SCRIPT
sudo yum install -y python python-pip python-pathlib sshpass git
pip2 install --upgrade -r requirements.txt
ansible --version
SCRIPT

$ansible_centos9 = <<-SCRIPT
sudo yum install -y python3 python3-pip sshpass git
pip3 install --upgrade --user -r requirements.txt
ansible --version
SCRIPT

$mdns_debian9 = <<-SCRIPT
sudo apt-get update
sudo apt-get install -y avahi-daemon libnss-mdns

echo "vagrant:vagrant" | sudo chpasswd
sed -i -Ee "s/.*?PasswordAuthentication.*?/PasswordAuthentication=yes/g" /etc/ssh/sshd_config
systemctl restart ssh
SCRIPT

$mdns_centos7 = <<-SCRIPT
sudo yum install -y avahi avahi-dnsconfd nss-mdns mdns-scan

echo "vagrant" | sudo passwd --stdin vagrant
sed -i -Ee "s/.*?PasswordAuthentication.*?/PasswordAuthentication=yes/g" /etc/ssh/sshd_config
systemctl restart sshd
SCRIPT

$mdns_centos9 = <<-SCRIPT
sudo yum install -y avahi nss-mdns mdns-scan

echo "vagrant" | sudo passwd --stdin vagrant
sed -i -Ee "s/.*?PasswordAuthentication.*?/PasswordAuthentication=yes/g" /etc/ssh/sshd_config
systemctl restart sshd
SCRIPT

Vagrant.configure("2") do |config|
    config.vm.network "private_network", type: "dhcp"
    config.vm.synced_folder ".", "/code/", type: "rsync"

    config.vm.define "control-old" do |server|
        server.vm.hostname = "control-old"
        server.vm.box = "generic/centos7"
        server.vm.box_version = "4.1.14"
        server.vm.provider "libvirt" do |v|
            v.memory = 512
            v.cpus = 4
        end

        server.vm.provision "file", source: "requirements-2.4.txt", destination: "requirements.txt"
        server.vm.provision "shell", inline: $ansible_centos7
        server.vm.provision "shell", inline: $mdns_centos7
    end

    config.vm.define "control-new" do |server|
        server.vm.hostname = "control-new"
        server.vm.box = "generic/centos9s"
        server.vm.box_version = "4.1.14"
        server.vm.provider "libvirt" do |v|
            v.memory = 512
            v.cpus = 4
        end

        server.vm.provision "file", source: "requirements-2.14.txt", destination: "requirements.txt"
        server.vm.provision "shell", inline: $ansible_centos9
        server.vm.provision "shell", inline: $mdns_centos9
    end

    config.vm.define "worker-debian" do |server|
        server.vm.hostname = "worker-debian"
        server.vm.box = "generic/debian9"
        server.vm.box_version = "4.1.14"
        server.vm.provider "libvirt" do |v|
            v.memory = 2048
            v.cpus = 4
        end
        server.vm.provision "shell", inline: $mdns_debian9
    end

    config.vm.define "worker-centos" do |server|
        server.vm.hostname = "worker-centos"
        server.vm.box = "generic/centos7"
        server.vm.box_version = "4.1.14"
        server.vm.provider "libvirt" do |v|
            v.memory = 2048
            v.cpus = 4
        end
        server.vm.provision "shell", inline: $mdns_centos7
    end



#     # this is a no-op, but generates an ansible inventory file, which we then later use
#      config.vm.provision "ansible" do |ansible|
#          ansible.playbook_command = "echo"
#          ansible.playbook = "Vagrantfile"
#          ansible.compatibility_mode = "2.0"
#
#          # become is passed as a parameter to ansible-playbook by vagrant,
#          # but because we want to execute ansible-playbook ourselves, we can
#          # reduce the number of arguments we write ourselves by including
#          # this as a hostvar in the generated inventory
#          ansible.host_vars = {
#             "ubuntu" => { "ansible_become" => true, "ansible_become_method": "sudo" },
#             "arch" => { "ansible_become" => true, "ansible_become_method": "sudo" }
#          }
#      end
end
