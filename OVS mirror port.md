#OVS mirror port

設定 Mirror

    ovs-vsctl \
    -- set bridge br0 mirrors=@m \
    -- --id=@vnet1 get Port eth0 \
    -- --id=@vnet2 get Port eth1 \
    -- --id=@m create Mirror name=mirror_test select-dst-port=@vnet1 select-src-port=@vnet1 output-port=@vnet2 
    
以上是針對某個特定端口做鏡像，但是針對Snort我們必須對所有端口做鏡像，所以我們改成

    ovs-vsctl \
    -- --id=@m create mirror name=mymirror \
    -- add bridge br0 mirrors @m \
    -- --id=@vnet2 get port vnet2 \
    -- set mirror mymirror select_all=true output-port=@vnet2
    
將 select_all=true 參數加入可達成目的
    
