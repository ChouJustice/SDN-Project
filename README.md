# SDN-Project
OpenDayLight Controller, OpenvSwitch, Snort and multiple end host

* Install multiple end host (with UDP Flooder)  
  * UDP Flooder : 連續發送UDP封包至受害者的主機，導致受害者網路頻寬充滿UDP封包，達成癱瘓網路的目的
  
* Install OpenvSwitch with ubuntu
  * 具有Flow Table內有Flow Rule可控制封包的流向
  * Flow Rule可定義封包的流動規則包含限制來源、目的、流量、規則持續時間等設定
  * 可設定由哪個Controller來控制Flow Table
  
* Install OpenDayLight Controller with Fedora
  * ODL Controller可觀察目前網路拓樸圖
  * 內有REST API可向Switch下達Flow Entry
