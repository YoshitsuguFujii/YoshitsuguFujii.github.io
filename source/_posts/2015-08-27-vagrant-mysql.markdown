---
layout: post
title: "VagrantとItamaeを使って5分でmysqlサーバー構築"
date: 2015-08-27 15:42:59 +0900
comments: true
categories: [vagrant, mysql, itamae]
---

普段はほぼモノシリックなサービスしか開発していないんだけど、去年の暮れぐらいからちょくちょく関わらせている案件がマイクロサービスで、新しい人が入るということで構築手順のまとめがてらVagrant + Itamaeで環境構築してみた。  
  
複数サーバーインスタンスを立ち上げて、各サーバーから一つのmysqlに接続できたらいいな。 (この構成ならdockerでやれという話もあるけど。先にvagrantを勉強してみたかったので）
  
とりあえず192.168.55.55で接続可能なmysqlのvagrant(なんていうんだろう。dockerだとコンテナに相当するやつ)を作成してみます。
  
<!-- more -->
  
```
vagrant init
vagrant plugin install vagrant-itamae
```
  
`vagrant init`して作成されたVagrantfileを編集します。  
  
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos65"
  config.vm.box_url = "https://github.com/2creatives/vagrant-centos/releases/download/v6.5.3/centos65-x86_64-20140116.box"

  config.vm.define "db" do |db|
    db.vm.hostname = "db"
    db.vm.network :private_network, ip: "192.168.55.55"

    config.vm.provision :itamae do |config|
      config.sudo = true
      config.recipes = ['./recipe.rb']
      config.json = './node.json'
    end
  end

  config.ssh.forward_agent = true

end
```
  
Itameのレシピ  
Vagrantfileと同じディレクトリにrecipe.rbという名称でおきます。  
  
```ruby recipe.rb 
package "epel-release"
package "gcc"
package "openssl-devel"
package "libyaml-devel"
package "readline-devel"
package "zlib-devel"
package "vim"

# mysql
package 'http://dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm' do
  not_if 'rpm -q mysql-community-release-el6-5'
end

%w( mysql-community-server mysql-community-devel ).each do |pkg|
  package pkg
end

utf8mb4_settting = <<"EOS"

[mysqld]
character-set-server = utf8mb4
character-set-client-handshake  = FALSE
character_set_server            = utf8mb4
collation_server                = utf8mb4_unicode_ci
innodb_file_format = Barracuda
innodb_file_per_table = 1
innodb_large_prefix

[client]
default-character-set=utf8mb4

EOS

execute "set db charset to utf8mb4" do
  command <<-EOF
    echo '#{utf8mb4_settting}' >> /etc/my.cnf
  EOF
end

service 'mysqld' do
  action [:start, :enable]
end

execute "allow remote host access" do
  # MySQLにリモートホストから接続できるようにする
  command "mysql -e \"grant all privileges on *.* to root@'192.168.%'\"";
end
```
  
レシピから参照するnode  
これもVagrantfileと同じ場所にnode.jsonという名前でおきます。  
  
```js 
{
  "mysql": {
    "password": ""
  }
}
```
  
そしたらvagrantを起動しましょう。  
```bash
vagrant up
```

無事に起動したらItamaeで環境構築です。
```bash
vagrant provision
```
  
無事に終了したらホストから接続してみます。  
```
mysql -uroot -h 192.168.55.55
```
  
で繋がれば完成です。
