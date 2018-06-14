#OpenDayLight Controller

下載檔案  

    http://www.opendaylight.org/software/downloads

解壓縮後，開啟命令提示字元，移至檔案目錄

    ./bin/karaf
    
安裝套件

    feature:install odl-mdsal-clustering
    feature:install odl-restconf
    feature:install odl-l2switch-switch
    feature:install odl-openflowplugin-all
    feature:install odl-dlux-all
    feature:install odl-mdsal-all
    feature:install odl-adsal-northbound
    
此時圖形化介面已經可以看到了

[!topo]()
