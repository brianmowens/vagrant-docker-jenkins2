Vagrant.configure("2") do |config|
    
    # Sync host working directory to virtual machine
    config.vm.synced_folder '.', '/vagrant'

    # SSH settings - Uses vagrant insecure key
    config.ssh.insert_key = false

    $install_docker = <<-SCRIPT
    apt-get update
    apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io
    SCRIPT

    $start_jenkins = <<-SCRIPT
    docker run -d -v jenkins_home:/var/jenkins_home -v "$HOME":/home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
    SCRIPT

    # VirtualBox.
    config.vm.define "jenkins2" do |virtualbox|
      virtualbox.vm.hostname = "jenkins2"
      virtualbox.vm.box = "ubuntu/xenial64"
      virtualbox.vm.box_version = "20190419.0.0"
      virtualbox.vm.box_check_update = true
      virtualbox.vm.define "jenkins2"
      virtualbox.vm.network "private_network", type: "dhcp",
          virtualbox__intnet: "NatNetwork"
      virtualbox.vm.network "forwarded_port", guest:8080, host:8081
      config.vm.provider :virtualbox do |v|
        v.gui = false
        v.memory = 4096
        v.cpus = 4
        v.name = "jenkins2"
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        v.customize ["modifyvm", :id, "--ioapic", "on"]
        v.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
      end
      config.vm.provision "shell", inline: $install_docker
      config.vm.provision "shell", inline: $start_jenkins
      config.vm.provision "shell", inline: "echo Hello, World"
    end

  end