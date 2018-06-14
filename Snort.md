# Snort

事前準備

    post-up ethtool -K eth0 gro off
    post-up ethtool -K eth0 lro off
    
事前安裝

    sudo apt-get install -y 
    build-essential 
    libpcap-dev 
    libpcre3-dev 
    libdumbnet-dev 
    bison 
    flex 
    zlib1g-dev 
    liblzma-dev 
    openssl 
    libssl-dev
    libnghttp2-dev
    
下載 Snort

    wget https://snort.org/downloads/snort/daq-2.0.6.tar.gz
    tar -xvzf daq-2.0.6.tar.gz
    
安裝並設定 Snort (命令提示字元移至檔案目錄)

    ./configure
    make
    sudo make install
    sudo ldconfig
    sudo ln -s /usr/local/bin/snort /usr/sbin/snort

啟動 Snort

    snort -V
    
***

修改 Snort.conf 設定檔
    
    ipvar HOME_NET {yourNetworkEnvironment}  # line 45
    var RULE_PATH /etc/snort/rules                      # line 104
    var SO_RULE_PATH /etc/snort/so_rules                # line 105
    var PREPROC_RULE_PATH /etc/snort/preproc_rules      # line 106
    var WHITE_LIST_PATH /etc/snort/rules/iplists        # line 113
    var BLACK_LIST_PATH /etc/snort/rules/iplists        # line 114
    
加入自定義規則

    include $RULE_PATH/{yourRules}.rules
    
    規則內容 : (測試規則)
    alert icmp any any -> $HOME_NET any (msg:"ICMP test detected"; GID:1; sid:10000001; rev:001; classtype:icmp-event;)
    
啟動 Snort

    sudo /usr/local/bin/snort -A console -q -u snort -g snort -c /etc/snort/snort.conf -i {yourport}
    
可以看到 ()

![snort]()

***
安裝 Barnyard2(在此作為將警報存入MySQL的工具)  
 
事前安裝

    sudo apt-get install -y 
    mysql-server 
    libmysqlclient-dev 
    mysql-client 
    autoconf 
    libtool
    
設定 Snort.conf 警告輸出Log檔格式

    output unified2: filename snort.u2, limit 128     # line 520
    
安裝 Barnyard2

    wget https://github.com/firnsy/barnyard2/archive/master.tar.gz -O barnyard2-Master.tar.gz
    tar zxvf barnyard2-Master.tar.gz
    cd barnyard2-master
    autoreconf -fvi -I ./m4
    
    sudo ln -s /usr/include/dumbnet.h /usr/include/dnet.h
    sudo ldconfig
    ./configure --with-mysql --with-mysql-libraries=/usr/lib/i386-linux-gnu
    
    make
    sudo make install
    
設定

    sudo cp ~/snort_src/barnyard2-master/etc/barnyard2.conf /etc/snort/
 
    sudo mkdir /var/log/barnyard2
    sudo chown snort.snort /var/log/barnyard2
 
    sudo touch /var/log/snort/barnyard2.waldo
    sudo chown snort.snort /var/log/snort/barnyard2.waldo
    
設定MySQL

    mysql -u root -p
    mysql> create database snort;
    mysql> use snort;
    mysql> source ~/snort_src/barnyard2-master/schemas/create_mysql
    mysql> CREATE USER 'snort'@'localhost' IDENTIFIED BY 'MYSQLSNORTPASSWORD';
    mysql> grant create, insert, select, delete, update on snort.* to 'snort'@'localhost';
    mysql> exit
    
修改 barnyard2.conf

    output database: log, mysql, user=snort password=MYSQLSNORTPASSWORD dbname=snort host=localhost sensor name=sensor01
    
啟動 Snort and Barnyard2

    sudo barnyard2 -c /etc/snort/barnyard2.conf -d /var/log/snort -f snort.u2 -w /var/log/snort/barnyard2.waldo -g snort -u snort
    
到此Snort的設定就完成了


