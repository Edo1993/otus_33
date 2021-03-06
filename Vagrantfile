# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :consul => {
             :box_name => "centos/7",
             :net => [
                      {ip: '192.168.10.150', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :pg1 => {
             :box_name => "centos/7",
             :net => [
                      {ip: '192.168.10.161', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :pg2 => {
             :box_name => "centos/7",
             :net => [
                      {ip: '192.168.10.162', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :pg3 => {
             :box_name => "centos/7",
             :net => [
                      {ip: '192.168.10.163', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
}


hosts_file="127.0.0.1\tlocalhost\n"

MACHINES.each do |hostname,config|  
  config[:net].each do |ip|
    if ip[:virtualbox__intnet]=="internal-net"
      hosts_file=hosts_file+ip[:ip]+"\t"+hostname.to_s+"\n"
    end
  end
end

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s
        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
        box.vm.provider "virtualbox" do |v|
          v.memory = 512
        end
        box.vm.provision "shell" do |shell|
          shell.inline = 'echo -e "$1" > /etc/hosts'
          shell.args = [hosts_file]
        end
        box.vm.provision "ansible" do |ansible|
          ansible.verbose = "v"
          ansible.playbook = "playbook.yml"
          ansible.tags = "all"
        end
    end
  end
end

