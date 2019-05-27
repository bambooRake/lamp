# lamp

### 1.ビルドツールのインストール

```
sudo apt update
sudo apt upgrade
sudo apt install -y build-essential \
libexpat1-dev \
libpcre3 \
libpcre3-dev \
libxml2-dev \
libssl-dev \
libncurses5-dev \
cmake \
autoconf
```

| ビルドに必要なパッケージ | ビルド対象 |
| --------------- | ---- |
| build-essential | 共通 |
| libexpat1-dev   |    apr-util  |
| libpcre3        |   apache   |
| libpcre3-dev    |   apache   |
| libxml2-dev     |   apache   |
| libssl-dev      |   MySQL   |
| libncurses5-dev |   MySQL   |
| cmake           |    MySQL  |
| autoconf        |    PHP  |

```
sudo vi /etc/sudoers
# コメントアウト
#Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin
# パスの引き継ぎ設定を追加
Defaults env_keep += "PATH"
```

### 2.apacheのインストール

```
wget ftp://ftp.jaist.ac.jp/pub/apache/apr/apr-1.7.0.tar.gz
tar xzvf apr-1.7.0.tar.gz
cd apr-1.7.0

Edit your configure file
Change the line
> $RM "$cfgfile"
to
> $RM -f "$cfgfile"

./configure -prefix=/opt/apr/apr-1.7.0
make
sudo make install
cd ..
```

```
wget ftp://ftp.jaist.ac.jp/pub/apache/apr/apr-util-1.6.1.tar.gz
tar xzvf apr-util-1.6.1.tar.gz
cd apr-util-1.6.1
./configure -prefix=/opt/apr-util/apr-util-1.6.1 -with-apr=/opt/apr/apr-1.7.0
make
sudo make install
cd ..
```

```
wget ftp://ftp.jaist.ac.jp/pub/apache/httpd/httpd-2.4.39.tar.gz
tar xzvf httpd-2.4.39.tar.gz
cd httpd-2.4.39
./configure -with-apr=/opt/apr/apr-1.7.0 -with-apr-util=/opt/apr-util/apr-util-1.6.1
make
sudo make install
cd ..

echo "export PATH=\$PATH:/usr/local/apache2/bin" >> ~/.bashrc
source ~/.bashrc
```

補足：
apacheのビルド時のコンフィグ設定確認
cat /usr/local/apache2/build/config.nice

### 3.MySQLのインストール

```
wget https://downloads.mysql.com/archives/get/file/mysql-boost-8.0.15.tar.gz
tar xzvf mysql-boost-8.0.15.tar.gz
cd mysql-boost-8.0.15

cmake -DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DFORCE_INSOURCE_BUILD=1 \
-DDOWNLOAD_BOOST=1 -DWITH_BOOST=./work_boost

make
sudo make install
cd ..

echo "export PATH=\$PATH:/usr/local/mysql/bin" >> ~/.bashrc
source ~/.bashrc

git clone git://github.com/mysql/mysql-server.git
```

補足：
mysqlの過去バージョンは下記からダウンロード可能
https://downloads.mysql.com/archives/community/

MySQLのビルド時のコンフィグ設定確認
<https://blog.kamipo.net/entry/20100822/1282451550>

```
% VISUAL='grep "Configure command" 1>&2' /usr/local/mysql/bin/mysqlbug > /dev/null
Configure command: ./configure  '--prefix=/usr/local/mysql' '--localstatedir=/usr/local/mysql/data' '--libexecdir=/usr/local/mysql/bin' '--with-comment=MySQL Community Server (GPL)' '--with-server-suffix=' '--enable-thread-safe-client' '--enable-local-infile' '--with-pic' '--with-fast-mutexes' '--with-client-ldflags=-static' '--with-mysqld-ldflags=-static' '--with-zlib-dir=bundled' '--with-big-tables' '--with-ssl' '--with-readline' '--with-embedded-server' '--with-partition' '--with-innodb' '--without-ndbcluster' '--with-archive-storage-engine' '--with-blackhole-storage-engine' '--with-csv-storage-engine' '--without-example-storage-engine' '--with-federated-storage-engine' '--with-extra-charsets=complex' 'CFLAGS=-g -O3' 'CXXFLAGS=-g -O3'
```

### 4.PHPのインストール

```
git clone https://github.com/php/php-src.git
cd php-src
git branch -r|grep 7.1
git checkout PHP-7.1.18
cd ./php-src
./buildconf --force

./configure \
--with-apxs2=/usr/local/apache2/bin/apxs \
--disable-cgi \
--enable-mbstring \
--enable-mbregex \
--with-pdo-mysql=mysqlnd \
--with-zlib-dir=/usr/lib

make
sudo make install
sudo cp php.ini-development /usr/local/lib/php.ini
cd ..
```

補足：
PHPのビルド時のコンフィグ設定確認
https://qiita.com/Yasuo_Nakata/items/80b5af4cc5d91f972e4b>
`php -i|grep configure`でconfigure設定確認

```
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>

※index.phpを追加
```

```
<IfModule php7_module>
  AddType application/x-httpd-php .php
</IfModule>
```

### 5.xdebugのインストール

<https://qiita.com/MasatoYoshioka@github/items/e542f39a6f1a3bc1f71e>
<https://qiita.com/k_o_gj/items/01191e19cbda4a93ba58>

pecl channel-update pecl.php.net 
opensslのインストール
php.iniに下記を追加
extension=/usr/local/lib/php/extensions/no-debug-zts-20160303/openssl.so
sudo pecl install xdebug
php.iniに下記を追加
zend_extension=/usr/local/lib/php/extensions/no-debug-zts-20160303/xdebug.so

pecl install xdebu



wget ftp://ftp.jaist.ac.jp/pub/apache/apr/apr-1.7.0.tar.gz
wget ftp://ftp.jaist.ac.jp/pub/apache/apr/apr-util-1.6.1.tar.gz
wget ftp://ftp.jaist.ac.jp/pub/apache/httpd/httpd-2.4.39.tar.gz

tar xzvf apr-1.7.0.tar.gz
tar xzvf apr-util-1.6.1.tar.gz
tar xzvf httpd-2.4.39.tar.gz

sudo apt-get -y install build-essential

cd apr-1.7.0
./configure -prefix=/opt/apr/apr-1.7.0
make
sudo make install

resolve ↓error
rm: cannot remove `libtoolT': No such file or directory

Edit your configure file

Change the line

> $RM "$cfgfile"

to

> $RM -f "$cfgfile"

cd apr-util-1.6.1
./configure -prefix=/opt/apr-util/apr-util-1.6.1 -with-apr=/opt/apr/apr-1.7.0
make
sudo make install

sudo apt intall -y libexpat1-dev
resolve 
xml/apr_xml.c:35:10: fatal error: expat.h: No such file or directory
 #include <expat.h>
          ^~~~~~~~~
compilation terminated.

cd httpd-2.4.39
./configure -with-apr=/opt/apr/apr-1.7.0 -with-apr-util=/opt/apr-util/apr-util-1.6.1
make
sudo make install

sudo apt install -y libpcre3 libpcre3-dev
resolve
configure: error: pcre-config for libpcre not found. PCRE is required and available from http://pcre.org/

sudo apt install -y libxml2-dev
resolve
Makefile:48: recipe for target 'htpasswd' failed 

[https://orebibou.com/2015/06/%E3%82%BD%E3%83%BC%E3%82%B9%E3%81%8B%E3%82%89%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%97%E3%81%9Fapache%E3%81%AEconfigure%E5%AE%9F%E8%A1%8C%E6%99%82%E3%81%AE%E3%82%AA%E3%83%97/](https://orebibou.com/2015/06/ソースからインストールしたapacheのconfigure実行時のオプ/)

echo "export PATH=\$PATH:/usr/local/apache2/bin" >> ~/.bashrc
source ~/.bashrc

```
sudo vi /etc/sudoers
# コメントアウト
#Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin
# パスの引き継ぎ設定を追加
Defaults env_keep += "PATH"
```



apacheのビルド時のコンフィグ設定確認
cat Apacheのインストール先ディレクトリ/build/config.nice

git clone https://github.com/php/php-src.git
cd php-src
git branch -r|grep 7.1
git checkout PHP-7.1.18
cd ./php-src
sudo apt install -y autoconf
./buildconf --force

```sh
./configure \
--with-apxs2=/usr/local/apache2/bin/apxs \
--disable-cgi \
--enable-mbstring \
--enable-mbregex \
--with-pdo-mysql=/usr/local/mysql
```

<https://qiita.com/Yasuo_Nakata/items/80b5af4cc5d91f972e4b>
`php -i`でconfigure設定確認

git clone https://github.com/xdebug/xdebug.git

mysqlの過去バージョンは下記からダウンロード可能<https://downloads.mysql.com/archives/community/>
wget https://downloads.mysql.com/archives/get/file/mysql-boost-8.0.15.tar.gz
tar xzvf mysql-boost-8.0.15.tar.gz
cd mysql-boost-8.0.15
sudo apt install -y libssl-dev libncurses5-dev 
sudo apt install -y cmake

```
cmake -DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DFORCE_INSOURCE_BUILD=1 \
-DDOWNLOAD_BOOST=1 -DWITH_BOOST=./work_boost
```

ビルド確認
<https://blog.kamipo.net/entry/20100822/1282451550>

```
% VISUAL='grep "Configure command" 1>&2' /usr/local/mysql/bin/mysqlbug > /dev/null
Configure command: ./configure  '--prefix=/usr/local/mysql' '--localstatedir=/usr/local/mysql/data' '--libexecdir=/usr/local/mysql/bin' '--with-comment=MySQL Community Server (GPL)' '--with-server-suffix=' '--enable-thread-safe-client' '--enable-local-infile' '--with-pic' '--with-fast-mutexes' '--with-client-ldflags=-static' '--with-mysqld-ldflags=-static' '--with-zlib-dir=bundled' '--with-big-tables' '--with-ssl' '--with-readline' '--with-embedded-server' '--with-partition' '--with-innodb' '--without-ndbcluster' '--with-archive-storage-engine' '--with-blackhole-storage-engine' '--with-csv-storage-engine' '--without-example-storage-engine' '--with-federated-storage-engine' '--with-extra-charsets=complex' 'CFLAGS=-g -O3' 'CXXFLAGS=-g -O3'
```



