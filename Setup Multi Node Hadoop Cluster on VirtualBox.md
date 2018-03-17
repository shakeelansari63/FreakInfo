# 1. Install CentOS 7 on 3 VMs (1 master 2 Worker) in VirtualBox
  Master: 60GB Disk, 4GB RAM
  Worker: 100GB Disk, 4GB RAM
	
  Add Network to all VMs
  1 - HOST Only Network
  2 - Bridge Network (For Internet Access)
	
# 2. Start All 3 VMs

# 3. Check Network Access on all VMs using 
  ```console
  nmcli d
  ```
  If you see all VMs are connected to network, go to Step 4
  Else issue following command to enter command Network Manager UI
  ```console
  nmtui
  ```
  ![alt text](/items/1.PNG)
  
  Go to **Edit a Connection**
  Select your connection name and go to **Edit**
  
  ![alt text](/items/2.PNG)

  Make sure **IPV4 Confugiration** is **Automatic**
  And **Automatically Connect** is checked (X)
  
  ![alt text](/items/3.PNG)

  Click **OK** and **Quit** to go back to Console.
  
  Again check network connection with following command. And all network interfaces should be connected now.
  ```console
  nmcli d
  ```
# 4. Assign Static IP for all VMs on Network. 
	This step is to make sure our VMs get same IP everytime they are booted and does not get randon IP from DHCP.
  ```console
  vi /etc/sysconfig/network-scripts/ifcfg-enp0sX
  ```
  Here X can be any of your network number. Check your network using
  ```console
  ip addr
  ```
  Make changes to BOOTPROTO and IPADDR. If IPADDR does not exist, them add a line. 
  Make sure, IP addr is different for each VM.

  ```console
  Make changes as follow
  TYPE=Ethernet                             
  PROXY_METHOD=none                         
  BROWSER_ONLY=no                           
  BOOTPROTO=static                          
  DEFROUTE=yes                              
  IPV4_FAILURE_FATAL=no                     
  IPADDR=192.168.99.100                     
  IPV6INIT=yes                              
  IPV6_AUTOCONF=yes                         
  IPV6_DEFROUTE=yes                         
  IPV6_FAILURE_FATAL=no                     
  IPV6_ADDR_GEN_MODE=stable-privacy         
  NAME=enp0s8                               
  UUID=d67596b8-cd0e-4626-9535-39090b1eece4 
  DEVICE=enp0s8                             
  ONBOOT=yes      
  ```
# 5. Install required packages on all VMs
  ```console
  sudo yum install wget net-tools vim 
  ```
# 6. Give Different host names to each VMs
  ```console
  vim /etc/hostname
  ```
  
  e.g.
  ```console
  hdpmaster.sandbox
  ```
  ```console
  hdpworker1.sandbox
  ```
  ```console
  hdpworker2.sandbox
  ```

# 7. Do SSH-KEY setup for all nodes, so they can communicate with each other.
  ```console
  ssh-keygen
  cd ~/.ssh
  cp id_rsa.pub authorized_keys
  sftp root@<other nodes>
  	cd /root
	mkdir .ssh
	put * ./
  ```

# 8. Add Ambari & HDP repos on master Node
  ```console
  cd /etc/yum/repos.d
  wget http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.6.1.5/ambari.repo
  wget http://public-repo-1.hortonworks.com/HDP/centos7/2.x/updates/2.6.4.0/hdp.repo
  cd ~
  yum repolist
  ```
  And you should see Ambari, HDP and HDP-UTILS repositories.
  
# 9. 
