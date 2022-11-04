$script_mysql = <<-SCRIPT
    apt-get update && \
    apt-get install -y mysql-server-5.7 && \
    mysql -e "CREATE USER IF NOT EXISTS 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|
    
      config.vm.define "mysqldb" do |mysql|
 
        #Config a imagem que vai ser usada
        mysql.vm.box = "ubuntu/bionic64"

        #Config de IP estatico e DHCP em rede privada
        #mysql.vm.network "private_network", ip: "192.168.99.4"
        #mysql.vm.network "private_network", type: "dhcp"
    
        #Config de rede bridge
        mysql.vm.network "public_network"
    
        #Script teste de criacao de arquivo hello.txt na pasta /home/vagrant
        #mysql.vm.provision 'shell', run: 'always', inline: "echo hello, World >> hello.txt"

        #Desabilitando pasta vagrant default
        mysql.vm.synced_folder ".", "/vagrant", disabled: true
    
        #Config de pasta compartilhada
        mysql.vm.synced_folder "./configs", "/configs"
    
        #Script para copiar chave publica para dentro da vm 
        mysql.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> .ssh/authorized_keys"

        #Script para rodar apt-get diretamente
        #mysql.vm.provision "shell", inline: "apt-get update && apt-get install -y mysql-server-5.7"

        mysql.vm.provision "shell", inline: $script_mysql
        mysql.vm.provision "shell",
          inline: "cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
        mysql.vm.provision "shell",
          inline: "service mysql restart"
      
      end


      config.vm.define "web" do |web|
  
        #Config a imagem que vai ser usada
        config.vm.box = "ubuntu/bionic64"

        #Redirecionando porta do host para guest
        web.vm.network "forwarded_port", guest: 8888, host:8888 
        
        #Config de rede modo bridge
        web.vm.network "public_network"

        #Config de instalacao do puppet
        web.vm.provision "shell",
          inline: "apt-get update && apt-get install -y puppet"

        #Config de chamada do puppet
        web.vm.provision "puppet" do |puppet|
            puppet.manifests_path = "./configs/manifests"
            puppet.manifest_file = "phpweb.pp"
        end 

        #Config. para alterar rota default
        #web.vm.provision "shell",
        #run: "always",
        #inline: "ip route replace 192.168.1.0/24 via 192.168.1.1"
      end

        
end
