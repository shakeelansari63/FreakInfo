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
  cd /etc/yum.repos.d
  wget http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.6.1.5/ambari.repo
  wget http://public-repo-1.hortonworks.com/HDP/centos7/2.x/updates/2.6.4.0/hdp.repo
  cd ~
  yum repolist
  ```
  And you should see Ambari, HDP and HDP-UTILS repositories.
  
# 9. Install Ambari-Server on Master Node
  ```console
  sudo yum install ambari-server
  ```
  
# 10. Setup Ambari Server on Master Node
  [Follow instructions on this page to setup Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.6.1.5/bk_ambari-installation/content/set_up_the_ambari_server.html)

# 11. Allow Firewall to access Ambari from Host Machine
  Easiest Option would be to Stop Firewall on all VMs so that Ambari setup can access every port.
  ```console
  service firewalld stop
  ```
  If you have disabled Firewall service, you can proceed with step 12. If you don't want to disable firewall but want to allow every ports for Ambari, go for further steps.
  ```console
  service firewalld status
  ```
  If above command tells that firewall is running, then follow these steps to allow connections from Host. 
  
  Else, go to Step 12.
  
  ```console
  firewall-cmd --get-zones
  ```
  This should tell you firewall zones. We need *public* zone here.
  ```console
  firewall-cmd --zone=public --list-services
  firewall-cmd --zone=public --permanent --list-services
  ```
  This gives you list of services in Public Zone. We need http and https services in this zone. 
  
  If you do not see http and https, then add them using.
  ```console
  firewall-cmd --zone=public --add-service=http
  firewall-cmd --zone=public --add-service=https
  firewall-cmd --zone=public --permanent --add-service=http
  firewall-cmd --zone=public --permanent --add-service=https
  ```
  Again run List services to check make sure http and https are added.
  
  Now allow 8080 port with following command
  ```console
  firewall-cmd --zone=public --add-port=8080/tcp
  firewall-cmd --zone=public --permanent --add-port=8080/tcp
  ```
  
  Confirm using 
  ```console
  firewall-cmd --zone=public --list-ports
  firewall-cmd --zone=public --permanent --list-ports
  ```

# 12. Login to Ambari from Host Machine 
  Now login to Ambari from Host machine browser and start setting up your cluster.
  http://\<masternode\>:8080

# 13. Rest setup can be done in Ambari. 
  Note: While Hive setup, select new mysql database. If you want to use existing postgres database, then you have to make few changes to Postgres.
  
# 14. PostGres changes for Hive & Oozie Metastore.
  Credits: [Sandeep Singh](https://hadoopjournal.wordpress.com/2015/07/25/hadoop-cluster-setup-ambari-installation-and-configuring-metastore-database/)
  ```console
  yum install postgresql-jdbc*
  ls /usr/share/java/postgresql-jdbc.jar
  ambari-server setup --jdbc-db=postgres --jdbc-driver=/usr/share/java/postgresql-jdbc.jar
  ```
  Now edit following pg_hba.conf file to give hive and oozie users access to metastore.
  ```console
  vim /var/lib/pgsql/data/pg_hba.conf
  ```
  ```console
  # TYPE  DATABASE        USER            ADDRESS                 METHOD
  # "local" is for Unix domain socket connections only
  local   all   postgres                                     peer
  # IPv4 local connections:
  host    all   postgres             127.0.0.1/32            ident
  # IPv6 local connections:
  host    all   postgres             ::1/128                 ident
  # Allow replication connections from localhost, by a user with the
  # replication privilege.
  #local   replication     postgres                                peer
  #host    replication     postgres        127.0.0.1/32            ident
  #host    replication     postgres        ::1/128                 ident
  local  all  ambari,mapred,hive,oozie md5
  host  all   ambari,mapred,hive,oozie 0.0.0.0/0  md5
  host  all   ambari,mapred,hive,oozie ::/0 md5
  ```
  And restart postgres
  ```console
  service postgresql restart
  ```
  Now login to Postgres and add Hive and Oozie users and databases.
  ```console
  sudo su postgres
  psql
  ```
  ```sql
  create database hive;
  create user hive with password 'HivePassword';
  grant all privileges on database hive to hive;
  create database oozie;
  create user oozie with password 'OoziePassowrd';
  grant all privileges on database oozie to oozie;
  ```
  
