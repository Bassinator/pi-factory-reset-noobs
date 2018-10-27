Vagrant.configure("2") do |config|

  require 'open3'
  stdout, stderr, status = Open3.capture3("ifconfig | grep 'inet '| grep -Fv 127.0.0.1 | awk '{print $2}'")

  config.vm.define "proxy" do |proxy|
    proxy.vm.network "public_network"
    proxy.vm.network :forwarded_port, host: 3128, guest: 3128
    proxy.vm.provider "docker" do |docker|
      docker.image = "bassualdo/centosvagrant"
      docker.has_ssh = true
    end
    proxy.vm.provision "ansible_local" do |provisioner|
      provisioner.extra_vars = {
        proxy_server_ip: stdout
      }
      provisioner.verbose = "vvv"
      provisioner.playbook = "proxy.yml"
      provisioner.install = true
    end
  end

  config.vm.define "formatter" do |formatter|
    formatter.vm.box = "centos/7"
    formatter.vm.synced_folder ".", "/vagrant", type: "virtualbox"
    formatter.vm.provider 'virtualbox' do |vb|
      # for usb support virtualbox extension pack is needed
      # see  https://www.virtualbox.org/wiki/Downloads
      vb.customize ["modifyvm", :id, "--usb", "on"]
      vb.customize ["modifyvm", :id, "--usbxhci", "on"]
      vb.customize ["usbfilter", "add", "0",
        "--target", :id,
        "--name", "usbdrive",
        "--product", "USB3.0-CRW"]
    end
    formatter.vm.provision "ansible_local" do |ansible|
      ansible.verbose = "vvv"
      ansible.extra_vars = {
        proxy_server_ip: stdout.chop!
      }
      ansible.galaxy_role_file = 'requirements.yml'
      ansible.playbook = "noobs.yml"
    end
  end

end
