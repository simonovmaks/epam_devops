$script1 = <<SCRIPT
yum install -y httpd
systemctl enable httpd
systemctl start httpd
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
cp /vagrant/mod_jk.so /etc/httpd/modules/
touch /etc/httpd/conf/workers.properties
echo "worker.list=balance, status
worker.balance.type=lb
worker.balance.balance_workers=node1, node2
worker.node1.type=ajp13
worker.node1.port=8009
worker.node1.host=tomcat1
worker.node1.lbfactor=1
worker.node2.type=ajp13
worker.node2.port=8009
worker.node2.host=tomcat2
worker.node2.lbfactor=1
worker.status.type=status" | tee -a /etc/httpd/conf/workers.properties
echo "\n LoadModule jk_module modules/mod_jk.so
JkWorkersFile conf/workers.properties
JkShmFile /tmp/jk.shm
JkLogFile logs/mod_jk.log
JkLogLevel info
#Add the test folder mount point
JkMount /test/* balance
# Add the jkstatus mount point
JkMount /jkmanager/* status" | tee -a /etc/httpd/conf/httpd.conf
echo "192.168.10.21 tomcat1\n 192.168.10.22 tomcat2" | tee -a /etc/hosts
systemctl restart httpd
SCRIPT

$script2 = <<SCRIPT
yum install -y tomcat tomcat-webapps tomcat-admin-webapps
systemctl enable tomcat
systemctl start tomcat
systemctl stop firewalld
mkdir -p /usr/share/tomcat/webapps/test/
echo "<html><head Node1/><body>Node1</body></html>" >> /usr/share/tomcat/webapps/test/index.html
sed 's/<Engine name="Catalina" defaultHost="localhost">/<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat1">/' /etc/tomcat/server.xml
sed 's/<Context>/<Context distributable="true">/' /etc/tomcat/context.xml
SCRIPT

$script3 = <<SCRIPT
yum install -y tomcat tomcat-webapps tomcat-admin-webapps
systemctl enable tomcat
systemctl start tomcat
systemctl stop firewalld
mkdir -p /usr/share/tomcat/webapps/test/
echo "<html><head Node1/><body>Node2</body></html>" >> /usr/share/tomcat/webapps/test/index.html
sed 's/<Engine name="Catalina" defaultHost="localhost">/<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat2">/' /etc/tomcat/server.xml
sed 's/<Context>/<Context distributable="true">/' /etc/tomcat/context.xml
SCRIPT

Vagrant.configure(2) do |config|
	config.vm.box = "bertvv/centos72"
	config.vm.provider "virtualbox" do |vb|
		vb.gui = true
		vb.memory = "1024"
	end
	config.vm.define "server" do |server|
		server.vm.hostname = "server"
		server.vm.network "private_network", ip: "192.168.10.10" 
		server.vm.network "forwarded_port", guest: 80, host: 15001
		server.vm.provision "shell",
			inline: $script1
	end
	config.vm.define "tomcat1" do |tomcat1|
		tomcat1.vm.hostname = "tomcat1"
		tomcat1.vm.network "private_network", ip: "192.168.10.21"
		tomcat1.vm.provision "shell",
			inline: $script2
	end
	config.vm.define "tomcat2" do |tomcat2|
		tomcat2.vm.hostname = "tomcat2"
		tomcat2.vm.network "private_network", ip: "192.168.10.22"
		tomcat2.vm.provision "shell",
			inline: $script3
	end
end