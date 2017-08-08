#SCRIPT for Server1 configuration. First download github, then make direktory for repo. Cloneing repo and show test1.txt
$script = <<SCRIPT
sudo yum install git -y
sudo mkdir -p /home/vagrant/epam_devops/
sudo git config --global user.name "simonovmaks"
sudo git config --global user.email "simonov.max.al@gmail.com"
sudo cd /home/vagrant/epam_devops/
sudo git clone --progress --branch Task1 https://github.com/simonovmaks/epam_devops.git /home/vagrant/epam_devops/
cd /home/vagrant/epam_devops/
sudo cat task1.txt
echo "192.168.10.20 server2" | sudo tee -a /etc/hosts
echo "192.168.10.10 server1" | sudo tee -a /etc/hosts 
SCRIPT
#Edding dns name server's to file hosts
$script2 = <<SCRIPT
echo "192.168.10.10 server1" | sudo tee -a /etc/hosts
echo "192.168.10.20 server2" | sudo tee -a /etc/hosts
SCRIPT

Vagrant.configure(2) do |config|
    config.vm.box = "bertvv/centos72"
    config.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory = "1024"
    end
#Server1 configure	
	config.vm.define "server1" do |server1|
		server1.vm.hostname = "server1"
		server1.vm.network "private_network", ip: "192.168.10.10" 
		server1.vm.network "forwarded_port", guest: 80, host: 15001
		server1.vm.provision "shell",
			inline: $script
	end
#Server2 configure	
	config.vm.define "server2" do |server2|
		server2.vm.hostname = "server2"
		server2.vm.network "private_network", ip: "192.168.10.20"
		server2.vm.network "forwarded_port", guest: 80, host: 15002
		server2.vm.provision "shell",
			inline: $script2
	end
end		