# -*- mode: ruby -*-
# vi: set ft=ruby :

VM_NAME = "site-builder"

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = VM_NAME

  config.ssh.forward_agent = true

  config.vm.network "private_network", ip: "192.168.50.201"

  config.vm.provider 'virtualbox' do |virtualbox|
    virtualbox.name = VM_NAME
    virtualbox.memory = 16384
    virtualbox.cpus = 8
  end

  config.vm.provision "shell", inline: <<-SHELL

    add-apt-repository -y ppa:webupd8team/java

    apt-get update
    apt-get install -y git tmux vim-nox golang
    apt-get install -y libffi-dev zlib1g-dev libssl-dev libreadline-dev

    echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections
    echo debconf shared/accepted-oracle-license-v1-1 seen true | debconf-set-selections

    apt-get install -y oracle-java8-installer

    wget -O /usr/local/bin/lein https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
    chmod a+x /usr/local/bin/lein
    lein -v

    wget -qO- https://get.docker.com/ | sh
    usermod -aG docker vagrant

    curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    chmod a+x /usr/local/bin/docker-compose

  SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL

    mkdir -p $HOME/go/{src,bin}
    go get github.com/mattn/goreman

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

  config.vm.provision "shell", privileged: false, inline: <<-SHELL

    mkdir -p $HOME/code/#{VM_NAME}
    cd $HOME/code/#{VM_NAME}

    git clone git@github.com:site-builder/frontend.git
    git clone git@github.com:site-builder/registrar.git

    cd frontend
    rbenv install
    rbenv rehash
    gem install bundler
    bundle install

    go get github.com/site-builder/fetcher

  SHELL

end
