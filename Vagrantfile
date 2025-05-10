MACHINES = {
  :router1 => {
    :box_name => "ubuntu/focal64",
    :vm_name => "router1",
    :net => [
      {ip: '10.0.10.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
      {ip: '10.0.12.1', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
      {ip: '192.168.10.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net1"},
      {ip: '192.168.50.10', adapter: 5, netmask: "255.255.255.0"}, # Сеть для управления
    ]
  },
  :router2 => {
    :box_name => "ubuntu/focal64",
    :vm_name => "router2",
    :net => [
      {ip: '10.0.10.2', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
      {ip: '10.0.11.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
      {ip: '192.168.20.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net2"},
      {ip: '192.168.50.11', adapter: 5, netmask: "255.255.255.0"},
    ]
  },
  :router3 => {
    :box_name => "ubuntu/focal64",
    :vm_name => "router3",
    :net => [
      {ip: '10.0.11.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
      {ip: '10.0.12.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
      {ip: '192.168.30.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net3"},
      {ip: '192.168.50.12', adapter: 5, netmask: "255.255.255.0"},
    ]
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]

      # Настройка Ansible для router3, применяется ко всем хостам
      if boxconfig[:vm_name] == "router3"
        box.vm.provision "ansible" do |ansible|
          ansible.playbook = "ansible/provision.yml"
          ansible.inventory_path = "ansible/hosts"
          ansible.host_key_checking = false
          ansible.limit = "all"
        end
      end

      # Настройка сетевых интерфейсов
      boxconfig[:net].each do |ipconf|
        if ipconf[:virtualbox__intnet]
          # Для внутренних сетей с virtualbox__intnet
          box.vm.network "private_network",
                         ip: ipconf[:ip],
                         netmask: ipconf[:netmask],
                         virtualbox__intnet: ipconf[:virtualbox__intnet],
                         adapter: ipconf[:adapter]
        else
          # Для сетей без virtualbox__intnet (например, сеть управления)
          box.vm.network "private_network",
                         ip: ipconf[:ip],
                         netmask: ipconf[:netmask],
                         adapter: ipconf[:adapter]
        end
      end
    end
  end
end