# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|
    config.vm.box = 'ubuntu/jammy64'
  
    # define manager nodes
    (1..2).each do |number|
      config.vm.define "m#{number}" do |node|
        node.vm.network 'private_network', ip: "192.168.99.10#{number}"
        node.vm.network 'forwarded_port', id: 'ssh', host: "810#{number}", guest: "22"
      
        node.vm.hostname = "m#{number}"
  
        # vagrant ordering of script execution is out -> in
        # so all top level provision calls happen before nested and thus this will run after docker is installed 
        if number == 1
          node.vm.provision 'shell', path: 'scripts/m1-viz-standalone-container.sh'
          node.vm.provision 'shell', path: 'scripts/m1-swarm-init.sh'
        else
          node.vm.provision 'shell', path: 'scripts/manager-join.sh'
        end
      end
    end
    
    # define worker nodes
    (1..3).each do |number|
      config.vm.define "w#{number}" do |node|
        node.vm.network 'private_network', ip: "192.168.99.11#{number}"
        node.vm.network 'forwarded_port', id: 'ssh', host: "811#{number}", guest: "22"
      
        node.vm.hostname = "w#{number}"
  
        node.vm.provision 'shell', path: 'scripts/worker-join.sh'
      end
    end

    # define db nodes
    (1...2).each do |number|
      config.vm.define "db#{number}" do |node|
        node.vm.network 'private_network', ip: "192.168.99.14#{number}"
        node.vm.network 'forwarded_port', id: 'ssh', host: "814#{number}", guest: "22"
      
        node.vm.hostname = "db#{number}"
  
        node.vm.provision 'shell', path: 'scripts/db-source.sh'
        node.vm.provision 'shell', path: 'scripts/worker-join.sh'

      end
    end


    # define registry work
    config.vm.define "r0" do |node|
      node.vm.network 'private_network', ip: "192.168.99.130"
      node.vm.network 'forwarded_port', id: 'ssh', host: "8130", guest: "22"
    

      node.vm.hostname = "r0"
    end
    
    # tweak vm settings to optimize for your machine:
    config.vm.provider 'virtualbox' do |vb|
      # nest inside loops above for targeting mX and wX nodes or use conditionals to one off set values like the provisioner scripts
      vb.memory = '812'
      vb.cpus = 1
      vb.customize ["modifyvm", :id, "--nataliasmode1", "default"]
    end
    
    # install docker:
    config.vm.provision 'docker'
    
    # (optional) tools for demos:
    config.vm.provision 'shell', inline: <<-SHELL
      sudo apt-get install -y tree bat jq
      touch ~vagrant/.hushlogin

      mkdir -p /etc/docker
      echo '{"insecure-registries": ["192.168.99.130:5000"]}' |  tee /etc/docker/daemon.json > /dev/null 
      sudo systemctl reload docker
    SHELL
  end