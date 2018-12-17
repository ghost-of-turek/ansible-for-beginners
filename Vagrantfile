Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.network "private_network", type: "dhcp"

  config.vm.provision :shell, privileged: false do |s|
    ssh_pub_key = File.readlines("./id_rsa.pub").first.strip
    s.inline = <<-SHELL
       echo #{ssh_pub_key} > /home/$USER/.ssh/authorized_keys
       sudo bash -c "echo #{ssh_pub_key} > /root/.ssh/authorized_keys"
    SHELL
  end

  config.vm.define "lb01" do |lb|
    lb.vm.box = "ubuntu/xenial64"
    lb.vm.network "private_network", ip: "10.0.2.2", auto_config: false
    lb.vm.network "forwarded_port", guest: 80, host: 54321
    lb.vm.provision :shell, privileged: true do |s|
      s.inline = <<-SHELL
         echo "10.0.2.3 app01" >> /etc/hosts
         echo "10.0.2.4 app02" >> /etc/hosts
      SHELL
    end
  end

  config.vm.define "app01" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network "private_network", ip: "10.0.2.3", auto_config: false
  end

  config.vm.define "app02" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network "private_network", ip: "10.0.2.4", auto_config: false
  end
end
