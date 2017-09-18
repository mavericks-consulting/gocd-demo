# OPTIONS
NO_OF_CLIENTS = 2
INSTALLER_LOCATION = 'installers/'
JAVA_INSTALL_PKG = 'openjdk-8-jre_8u131-b11-2ubuntu1.16.04.3_amd64.deb'

# INSTALL SCRIPTS
SERVER_INSTALL_PKG = "go-server_17.10.0-5380_all.deb"
SERVER_INSTALL_SCRIPT = {
  online: [
    'echo "Adding GoCD to apt sources..."',
    'echo "deb https://download.gocd.org /" | tee /etc/apt/sources.list.d/gocd.list',
    'curl https://download.gocd.org/GOCD-GPG-KEY.asc | apt-key add -',
    'add-apt-repository ppa:openjdk-r/ppa',
    'echo "Updating apt..."',
    'apt-get update -q',
    'echo "Installing GoCD Server..."',
    'apt-get install -qy openjdk-8-jre go-server',
  ],
  offline: [
    'echo "Installing OpenJDK 8 JRE..."',
    "dpkg -iG $HOME/#{JAVA_INSTALL_PKG}",
    'echo "Installing GoCD Server..."',
    "dpkg -iG $HOME/#{SERVER_INSTALL_PKG}"
  ]
}
SERVER_OFFLINE_INSTALL = false # Not sure if this works right now
  # File.exists?("#{INSTALLER_LOCATION}/#{JAVA_INSTALL_PKG}") &&
  # File.exists?("#{INSTALLER_LOCATION}/#{SERVER_INSTALL_PKG}")
SERVER_START = '/etc/init.d/go-server start'

CLIENT_INSTALL_PKG = "go-agent_17.10.0-5380_all.deb"
CLIENT_INSTALL_SCRIPT = {
  online: [
    'echo "Adding GoCD to apt sources..."',
    'echo "deb https://download.gocd.org /" | sudo tee /etc/apt/sources.list.d/gocd.list',
    'curl https://download.gocd.org/GOCD-GPG-KEY.asc | sudo apt-key add -',
    'echo "Updating apt..."',
    'apt-get update -q',
    'echo "Installing GoCD Agent..."',
    'apt-get install -qy openjdk-8-jre go-agent',
  ],
  offline: [
    'echo "Installing OpenJDK 8 JRE..."',
    "dpkg -iG $HOME/#{JAVA_INSTALL_PKG}",
    'echo "Installing GoCD Agent..."',
    "dpkg -iG $HOME/#{CLIENT_INSTALL_PKG}"
  ]
}
CLIENT_OFFLINE_INSTALL = false # Not sure if this works right now
  # File.exists?("#{INSTALLER_LOCATION}/#{JAVA_INSTALL_PKG}") &&
  # File.exists?("#{INSTALLER_LOCATION}/#{CLIENT_INSTALL_PKG}")
CLIENT_START = '/etc/init.d/go-agent start'

Vagrant.configure("2") do |config|

  config.vm.define("server") do |server|
    server.vm.box = "ubuntu/xenial64"

    server.vm.network "private_network", ip: "192.168.100.254"
    server.vm.network "forwarded_port", guest: 8153, host: 8153

    if SERVER_OFFLINE_INSTALL
      server.vm.provision "file", source: "#{INSTALLER_LOCATION}/#{JAVA_INSTALL_PKG}", destination: "$HOME/#{JAVA_INSTALL_PKG}"
      server.vm.provision "file", source: "#{INSTALLER_LOCATION}/#{SERVER_INSTALL_PKG}", destination: "$HOME/#{SERVER_INSTALL_PKG}"
      server.vm.provision "shell", inline: SERVER_INSTALL_SCRIPT[:offline].join("\n")
    else
      server.vm.provision "shell", inline: SERVER_INSTALL_SCRIPT[:online].join("\n")
    end

    server.vm.provision "shell", inline: 'echo Sleeping for a bit to let things calm down...'
    sleep 3
    server.vm.provision "shell", inline: SERVER_START
  end

  (1..NO_OF_CLIENTS).each do |i|
    config.vm.define("client_#{i}") do |client|
      client.vm.box = "ubuntu/xenial64"

      client.vm.network "private_network", ip: "192.168.100.#{i + 1}"

      if CLIENT_OFFLINE_INSTALL
        client.vm.provision "file", source: "#{INSTALLER_LOCATION}/#{JAVA_INSTALL_PKG}", destination: "$HOME/#{JAVA_INSTALL_PKG}"
        client.vm.provision "file", source: "#{INSTALLER_LOCATION}/#{CLIENT_INSTALL_PKG}", destination: "$HOME/#{CLIENT_INSTALL_PKG}"
        client.vm.provision "shell", inline: CLIENT_INSTALL_SCRIPT[:offline].join("\n")
      else
        client.vm.provision "shell", inline: CLIENT_INSTALL_SCRIPT[:online].join("\n")
      end

      client.vm.provision "shell", inline: 'echo Sleeping for a bit to let things calm down...'
      sleep 3
      client.vm.provision "shell", inline: CLIENT_START
    end
  end
end

