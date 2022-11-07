# Grab the name of the default interface
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

Vagrant.configure("2") do |config|
    
      config.vm.define "mysqldb" do |mysql|
 
        #Config a imagem que vai ser usada
        mysql.vm.box = "ubuntu/bionic64"

        mysql.vm.provider "virtualbox" do |vb|
            vb.memory = 512
            vb.cpus = 1
            vb.name = "ubuntu_bionic_mysqld"
        end

        #Config o nome do host
        mysql.vm.hostname = "mysqld"

        #Config de IP estatico e DHCP em rede privada
        #mysql.vm.network "private_network", ip: "192.168.99.4"
        #mysql.vm.network "private_network", type: "dhcp"
    
        #Config de rede bridge
        mysql.vm.network "public_network", ip: "192.168.1.70", bridge: "#$default_network_interface"
    
        #Script teste de criacao de arquivo hello.txt na pasta /home/vagrant
        #mysql.vm.provision 'shell', run: 'always', inline: "echo hello, World >> hello.txt"

        #Desabilitando pasta vagrant default
        mysql.vm.synced_folder ".", "/vagrant", disabled: true
    
        #Config de pasta compartilhada
        mysql.vm.synced_folder "./configs", "/configs"
    
        #Script para copiar chave publica para dentro da vm 
        mysql.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"

      end


      config.vm.define "web" do |web|
  
        #Config a imagem que vai ser usada
        web.vm.box = "ubuntu/bionic64"

        web.vm.provider "virtualbox" do |vb|
            vb.memory = 1024
            vb.cpus = 2
            vb.name = "ubuntu_bionic_web"
        end
        
        web.vm.hostname = "nginx"

        #Redirecionando porta do host para guest
        web.vm.network "forwarded_port", guest: 8888, host:8888 
        
        #Config de rede modo bridge
        web.vm.network "public_network", ip:"192.168.1.71", bridge: "#$default_network_interface"

        #Config de pasta compartilhada
        web.vm.synced_folder "./configs", "/configs"

        #Script para copiar chave publica para dentro da vm 
        web.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"

      end

      config.vm.define "ansible" do |ansible|
     
        #Config a imagem que vai ser usada
        ansible.vm.box = "ubuntu/bionic64"
        
        #Config para o virtualbox
        ansible.vm.provider "virtualbox" do |vb|
            vb.memory = 512
            vb.cpus = 1
            vb.name = "ubuntu_bionic_ansible"
        end

        ansible.vm.hostname = "ansible"

        #Config de rede bridge
        ansible.vm.network "public_network", bridge: "#$default_network_interface"
   
        ansible.vm.provision "shell",
            inline: "apt-get update && \
                     apt-get install -y software-properties-common && \
                     apt-add-repository --yes --update ppa:ansible/ansible && \
                     apt-get install -y ansible"
  
        #Config de pasta compartilhada
        ansible.vm.synced_folder "./configs", "/configs"

        #Script para copiar chave publica para dentro da vm 
        ansible.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"

        #Script para copiar chave publica privada e ajustes p dentro da vm 
        ansible.vm.provision 'shell', inline: "cp /configs/id_rsa /home/vagrant/.ssh/id_rsa && \
          chmod 600 /home/vagrant/.ssh/id_rsa && chown vagrant:vagrant /home/vagrant/.ssh/id_rsa"

        #Script de integracao vagrant <-> ansible
        ansible.vm.provision "shell", inline: "ansible-playbook -i /vagrant/configs/ansible/hosts \
                 /vagrant/configs/ansible/main.yml"

      end
end
