 
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Configuración para el primer servidor Apache
  config.vm.define "apache1" do |apache1|
    apache1.vm.box = "ubuntu/bionic64"
    apache1.vm.hostname = "apache1"
    apache1.vm.network "private_network", ip: "192.168.56.11"
    apache1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    apache1.vm.synced_folder "./html", "/var/www/html" # Directorio compartido
    apache1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      echo "<h1>Servidor Apache 1</h1>" > /var/www/html/index.html
    SHELL
  end

  # Configuración para el segundo servidor Apache
  config.vm.define "apache2" do |apache2|
    apache2.vm.box = "ubuntu/bionic64"
    apache2.vm.hostname = "apache2"
    apache2.vm.network "private_network", ip: "192.168.56.12"
    apache2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    apache2.vm.synced_folder "./html", "/var/www/html" # Directorio compartido
    apache2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      echo "<h1>Servidor Apache 2</h1>" > /var/www/html/index.html
    SHELL
  end

  # Configuración para el servidor Nginx (Balanceador de carga)
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "ubuntu/bionic64"
    nginx.vm.hostname = "nginx"
    nginx.vm.network "private_network", ip: "192.168.56.10"
    nginx.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    nginx.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      cat <<EOL > /etc/nginx/conf.d/load_balancer.conf
upstream apache_servers {
    server 192.168.56.11;
    server 192.168.56.12;
}

server {
    listen 80;
    location / {
        proxy_pass http://apache_servers;
    }
}
EOL
      systemctl restart nginx
    SHELL
  end
end
