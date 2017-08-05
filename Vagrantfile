Vagrant.configure(2) do |config|
    config.vm.box = "bertvv/centos72"
    config.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory = "1024"
    end
	config.vm.define "server1" do |server1|
		server1.vm.hostname = "server1"
		server1.vm.network "private_network", ip: "172.20.20.1"
		server1.vm.synced_folder "c:/", "/vagrant_data"
		server1.vm.provision "yum", type: "shell",
			inline: "sudo yum install git -y"
	end		
	config.vm.define "server2" do |server2|
		server2.vm.hostname = "server2"
		server2.vm.network "private_network", ip: "172.20.20.2"
	end
end		

