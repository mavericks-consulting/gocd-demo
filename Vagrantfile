
SERVER_INSTALL_SCRIPT = <<-SERVER
echo "Adding GoCD to apt sources..."
echo "deb https://download.gocd.org /" | tee /etc/apt/sources.list.d/gocd.list
curl https://download.gocd.org/GOCD-GPG-KEY.asc | apt-key add -
add-apt-repository ppa:openjdk-r/ppa
echo "Updating apt..."
apt-get update -q
echo "Installing GoCD Server..."
apt-get install -qy openjdk-8-jre go-server
SERVER

SERVER_START = '/etc/init.d/go-server start'

Vagrant.configure("2") do |config|

  config.vm.define("server") do |server|
    server.vm.box = "ubuntu/xenial64"

    server.vm.provision "shell", inline: SERVER_INSTALL_SCRIPT
    server.vm.provision "shell", inline: SERVER_START

    server.vm.network "private_network", ip: "192.168.100.254"
    server.vm.network "forwarded_port", guest: 8153, host: 8153
  end
end

