# -*- mode: ruby -*-
# vi: set ft=ruby :

VM_NAME = "site-builder"

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = VM_NAME

  config.ssh.forward_agent = true

  config.vm.provider 'virtualbox' do |virtualbox|
    virtualbox.name = VM_NAME
    virtualbox.memory = 16384
    virtualbox.cpus = 8
  end

  config.vm.provider "hosts" do |host|
    host.add_host "192.168.50.301", ["#{VM_NAME}.local"]
  end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y git tmux vim-nox golang
    apt-get install -y libffi-dev zlib1g-dev libssl-dev libreadline-dev

    wget -O /usr/local/bin/lein https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
    chmod a+x /usr/local/bin/lein
    lein -v

    wget -qO- https://get.docker.com/ | sh
    usermod -aG docker vagrant

    curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    chmod a+x /usr/local/bin/docker-compose

  SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL

    git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
    git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

    git clone git://github.com/andsens/homeshick.git $HOME/.homesick/repos/homeshick
    source "$HOME/.homesick/repos/homeshick/homeshick.sh"

    homeshick clone jimeh/git-aware-prompt
    homeshick clone gmarik/vundle.vim
    homeshick clone spadin/dotbashrc
    homeshick clone spadin/dotvimrc
    homeshick clone spadin/dotfilesdeux
    homeshick link -f

    vim -u ~/.vimrc.d/plugins.vim +PluginInstall +qall

  SHELL

end
