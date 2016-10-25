Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  config.vm.define "samplicator" do |samplicator|
    samplicator.vm.hostname = "samplicator"
    samplicator.vm.network "private_network", virtualbox__intnet: "front", ip: "10.0.0.2"
    samplicator.vm.network "private_network", virtualbox__intnet: "back", ip: "192.168.0.1"
    samplicator.vm.provision "shell", inline: <<-SHELL
      sudo yum update
      sudo yum install -y rsyslog nano tcpdump nmap
      sudo yum groupinstall -y "Development tools"
      sudo yum install -y automake
      sudo yum install -y git
      git clone https://github.com/sleinen/samplicator.git
      cd samplicator/ 
      sudo ./autogen.sh
      sudo ./configure
      sudo make
      sudo make install
      SHELL
    samplicator.vm.provider "virtualbox" do |vb|
      vb.name = "samplicator"
    end
  end
  

  config.vm.define "generator" do |generator|
    generator.vm.hostname = "generator"
    generator.vm.network "private_network", virtualbox__intnet: "front", ip: "10.0.0.1"
    generator.vm.provision "shell", inline: <<-SHELL
      sudo yum update
      sudo yum install -y rsyslog nano tcpdump nmap
      echo "*.* @10.0.0.2:514" >> /etc/rsyslog.conf
      systemctl restart rsyslog
      systemctl enable rsyslog
      SHELL
    generator.vm.provider "virtualbox" do |vb|
      vb.name = "generator"
    end
  end


  config.vm.define "receiver" do |receiver|
    receiver.vm.hostname = "receiver"
    receiver.vm.network "private_network", virtualbox__intnet: "back", ip: "192.168.0.2"
    receiver.vm.provision "shell", inline: <<-SHELL
      sudo yum update
      sudo yum install -y rsyslog nano tcpdump nmap
      systemctl start rsyslog
      systemctl enable rsyslog
      SHELL
    receiver.vm.provider "virtualbox" do |vb|
      vb.name = "receiver"
    end
  end

end