Vagrant::configure("2") do |config|

	config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
	config.vm.box = "phusion/ubuntu-14.04-amd64"

	config.vm.network "forwarded_port", guest: 10001, host: 10001

	config.vm.provider "virtualbox" do |v, o|
		unless Vagrant.has_plugin?("vagrant-vbguest")
			raise 'Please install the vagrant-vbguest plugin! (with the following command: vagrant plugin install vagrant-vbguest)'
		end

		v.memory = 1024
		v.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "2", "--ioapic", "on", "--chipset", "ich9", "--ostype", "Ubuntu_64"]
	end

	config.vm.provision "shell",
		inline: "if [[ $(docker ps -aq) ]]; then docker rm -f $(docker ps -aq); fi"

	config.vm.provision "docker" do |d|
		d.build_image "/vagrant", args: "--no-cache -t fake-s3"
		d.run "fake-s3", image: "fake-s3", args: "--net=host -e FAKE_S3_BUCKETS=bucket1,bucket2"
	end

	# autostart is broken
	config.vm.provision :shell, run: :always,
		inline: "if [[ $(docker ps -aq) ]]; then docker start $(docker ps -aq); fi"
end
