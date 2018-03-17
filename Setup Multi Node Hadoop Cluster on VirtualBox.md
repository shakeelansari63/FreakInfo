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
