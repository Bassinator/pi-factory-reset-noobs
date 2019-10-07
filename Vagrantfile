Vagrant.configure("2") do |config|

  config.vm.define "formatter" do |formatter|
    formatter.vm.box = "centos/7"
    formatter.vm.synced_folder ".", "/vagrant", type: "rsync"
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

    formatter.vm.provision "shell",
    inline: "yum -y install python-setuptools"
    
    formatter.vm.provision "shell",
    inline: "easy_install pip"

    formatter.vm.provision "shell",
    inline: "pip install ansible"

    formatter.vm.provision "shell",
    inline: "ansible-galaxy install -r /vagrant/requirements.yml --force"

    formatter.vm.provision "shell",
    inline: "ansible-playbook --connection=local --inventory 127.0.0.1, /vagrant/noobs.yml"

  end

end
