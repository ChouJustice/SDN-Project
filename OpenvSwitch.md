#OpenvSwitch

安裝前所事先準備(注意最好先更新作業系統)

    apt-get install -y 
    git 
    automake 
    autoconf 
    gcc 
    uml-utilities 
    libtool 
    build-essential
    pkg-config 
    linux-headers-`uname -r`
    
下載OpenvSwitch

    http://openvswitch.org/releases/openvswitch-2.9.2.tar.gz
    
解壓縮後開啟命令提示字元移至檔案目錄，開始安裝

    ./boot.sh
    ./configure --with-linux=/lib/modules/`uname -r`/build
    make
    sudo make install
    
載入openvswitch kernel module

    sudo insmod datapath/linux/openvswitch.ko
    
建立設定檔案

    ouch /usr/local/etc/ovs-vswitchd.conf
    sudo mkdir -p /usr/local/etc/openvswitch
    sudo ovsdb-tool create /usr/local/etc/openvswitch/conf.db vswitchd/vswitch.ovsschema
    
啟動OVS資料庫

    ovsdb-server /usr/local/etc/openvswitch/conf.db \
    --remote=punix:/usr/local/var/run/openvswitch/db.sock \
    --remote=db:Open_vSwitch,manager_options \
    --private-key=db:SSL,private_key \
    --certificate=db:SSL,certificate \
    --bootstrap-ca-cert=db:SSL,ca_cert --pidfile --detach --log-file
    
啟動OpenvSwitch

    sudo ovs-vsctl --no-wait init
    sudo ovs-vswitchd --pidfile --detach
    sudo ovs-vsctl show
    
設定網路，使網路卡變成OpenvSwitch上面的port

    sudo ovs-vsctl add-br br0
    sudo ovs-vsctl add-port br0 eth0
    sudo ovs-vsctl add-port br0 eth1
    sudo ovs-vsctl add-port br0 eth2
    sudo ifconfig eth0 0
    sudo ifconfig eth1 0
    sudo ifconfig eth2 0
    sudo ifconfig br0 {yourIP} netmask {yournetmask}
    sudo route add default gw {yourgeteway} br0
    
連到OpenFlow Controller (認領你的Controller)

    sudo ovs-vsctl set-controller br0 tcp:{yourController:port}
    
若正常連線，可看到Controller

    ![switch]()
    
若連線失敗請檢查相關防火牆設定
