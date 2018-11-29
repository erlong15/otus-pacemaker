# -*- mode: ruby -*-
# vi: set ft=ruby :
nodes = {
  :pcm1 => {
        :box_name => "centos/7",
        :ip_addr => '192.168.7.150'
   
  },
  :pcm2 => {
        :box_name => "centos/7",
        :ip_addr => '192.168.7.151'
         
  },
}

Vagrant.configure(2) do |config|
  nodes.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s
          box.vm.network "private_network", ip: boxconfig[:ip_addr]
	  box.vm.provider :virtualbox do |vb|
            vb.memory = "256"
            vb.name = boxname.to_s

          end
      end
  end
end
