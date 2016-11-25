# http://qiita.com/masuidrive/items/0e0e5294bc2dc81a52c2

Vagrant.configure("2") do |config|

  # インストールするOSを選択する。https://atlas.hashicorp.com/boxes/searchにインストール可能なOSがリストされている。
  config.vm.box = "ubuntu/trusty64"
  config.vm.network :forwarded_port, guest: 3000, host: 3000
  config.vm.synced_folder ".", "/vagrant"

  # RubyのVersionを指定
  GUEST_RUBY_VERSION = '2.3.3'

  # Node.jsのVersionを指定
  GUEST_NVM_VERSION = '0.30.1'
  GUEST_NODE_VERSION = 'stable'

  # 必要なパッケージをインストール
  config.vm.provision "shell", privileged: true, inline: <<-__SCRIPT__
    export DEBIAN_FRONTEND=noninteractive
    apt-get update -y

    # MySQLのインストール
    apt-get install -y debconf-utils
    # debconf-set-selections <<< "mysql-server-5.5 mysql-server/root_password password \"\""
    # debconf-set-selections <<< "mysql-server-5.5 mysql-server/root_password_again password  \"\""
    # apt-get install -y mysql-server-5.5 mysql-client-5.5 libmysqlclient-dev
    # apt-get install -y redis-server

    # 開発に必要なライブラリのインストール
    apt-get install -y git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev

  __SCRIPT__

  # RubyとNodeをコンパイル
  config.vm.provision "shell", privileged: false, inline: <<-__SCRIPT__
    # Rubyのインストール(rbenv, ruby-build)
    git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
    git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
    git clone git://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash

    # rbenvの設定
    echo $'export PATH="\$HOME/.rbenv/bin:\$HOME/.rbenv/plugins/ruby-build/bin:\$PATH"' >> ~/.bashrc
    echo $'eval "\$(rbenv init -)"' >> ~/.bashrc
    echo $'gem: --no-ri --no-rdoc' > ~/.gemrc

    # nvmのインストール(Node.js)
    echo $'. ~/.nvm/nvm.sh' >> ~/.bashrc
    curl -s https://raw.githubusercontent.com/creationix/nvm/v#{GUEST_NVM_VERSION}/install.sh | bash

    export PATH="$HOME/.rbenv/bin:$HOME/.rbenv/plugins/ruby-build/bin:$PATH"
    eval "$(rbenv init -)"
    . ~/.nvm/nvm.sh

    # rbenvを利用してRubyのインストール
    #{defined?(GUEST_RUBY_VERSION) ? "rbenv install %s; rbenv global %s; gem install bundler" % [GUEST_RUBY_VERSION, GUEST_RUBY_VERSION] : ''}

    # Node.jsのインストール
    #{defined?(GUEST_NODE_VERSION) ? "nvm install %s; nvm alias default %s" % [GUEST_NODE_VERSION, GUEST_NODE_VERSION] : ''}
  __SCRIPT__

  config.vm.provider "virtualbox" do |vb|
    # Virtualboxのメモリサイズの指定(1024MB == 1GB)
    vb.memory = ENV["VM_MEMORY"] || "1024"
    vb.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]
  end
end

=begin
シンプルなRails開発環境

- Ruby
- Node (オプション)
- MySQL
- Redis
- PhantomJS
- Webkit5 (for Capybara)

参照:
https://gorails.com/setup/ubuntu/14.10
=end
