Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"

  config.vm.define "server" do |srv|
    srv.vm.hostname = "server"
    srv.vm.network "public_network", bridge: "eth0"   # Host Network Card
    srv.vm.network "private_network",
      ip: "192.168.57.10",
      virtualbox__intnet: "intnet"
    srv.vm.provision "shell", path: "server.sh"
  end

  config.vm.define "c1" do |c1|
    c1.vm.hostname = "c1"
    c1.vm.network "private_network",
      type: "dhcp",
      virtualbox__intnet: "intnet"
    c1.vm.provision "shell", path: "client.sh"
  end

  config.vm.define "printer" do |p|
    p.vm.hostname = "printer"
    p.vm.network "private_network",
      mac: "080027AABB01",
      type: "dhcp",
      virtualbox__intnet: "intnet"
    p.vm.provision "shell", path: "client.sh"
  end
end