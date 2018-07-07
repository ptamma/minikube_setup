# Minikube Install on Windows 10 with Hyper-V

Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day.


This document is an attempt to provide few more details on steps to installing minikube on Windows 10 using Hyper-V with internal switch (i.e. NAT). Using NAT mode provides additional security and flexibility while moving between office and home network. Microsoft has made some great improvements to their Hypervisor on Windows 10 Fall Creators release.


##Prerequisite 

	*Windows 10 64bit Pro / Enterprise Edition
	
	*Windows 10 certified laptop with Intel Chip set(BIOS enabled with VT-x)
	
	*AIR Gap systems behind the firewall requires proxy forwards enabled for 
	
		*github.com
		
		*gcr.io/
		
		*k8s.gcr.io/
		
		*storage.googleapis.com/
	
	*Local Administrator level access required. These can be elevated privilege access to standard developer.

NOTE: On first run of minikube requires connection to above sites to download files. These sites are hard wired in [minikube constants](https://github.com/kubernetes/minikube/blob/master/pkg/minikube/constants/constants.go)

## System Used for Testing

``` 
	MacBookPro with Boot Camp and Lenovo ThinkPad T450
	
	WinVer - Windows 10 Pro - Microsoft Windows Version 1803(OS Build 17134.1) with Hyper-V
	
```


## Steps by Steps to install and configure minikube

1.	Enable VT-x (System BIOS specific)

2.	Enable Hyper-V - Open a PowerShell console as Administrator and Run the command: `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All` . When the installation has completed, Accept Y to reboot. For additional help refer [Install Hyper-V on Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

3.	Create a folder on Windows C Drive e.g. `C:\appvol\`. And added it to PATH environment variable. Ensure the folder in on C: Drive only.

4.	Download executable minikube and kubectl to C:\appvol\. 
	
	PS C:\appvol> `curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-windows-amd64.exe`
	PS C:\appvol> `curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/windows/amd64/kubectl.exe`
	
	NOTE: Download the latest stable release from respective site. [ Minikube ](https://github.com/kubernetes/minikube#windows) [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl)
	
5.	Check the Hyper-V VMSwitch

	PS C:\Windows\system32> `Get-VMSwitch`

	```	
		Name           SwitchType NetAdapterInterfaceDescription
		----           ---------- ------------------------------
		Default Switch Internal   Teamed-Interface
	```
	
6.	Check the available Net adapter. Ensure vEthernet (Default Switch) status is up. It is key that Default Switch is up for successful start of minikube. Hyper-v default switch act like a DHCP server which provides IP address to Guest VM.

	PS C:\Windows\system32> `Get-NetAdapter`

	```
		Name                      InterfaceDescription                    ifIndex Status       MacAddress             LinkSpeed
		----                      --------------------                    ------- ------       ----------             ---------
		vEthernet (Default Swi... Hyper-V Virtual Ethernet Adapter             17 Up           FA-15-B3-51-8A-44        10 Gbps
		Bluetooth Network Conn... Bluetooth Device (Personal Area Netw...      11 Disconnected 8C-85-90-25-39-9D         3 Mbps
		Wi-Fi                     Broadcom 802.11ac Network Adapter            10 Up           8C-85-90-1F-E4-59       468 Mbps
	```

7.	Disable IPv6 on Network Adapter Check the system IP Address.  Control Panel> Network and Internet> Network and Sharing Center > vEthernet (Default Switch) > Properties > `uncheck` Internet Protocol version 6 (TCP/IPv6)

	PS C:\Windows\system32> Get-NetIPAddress

	```
		IPAddress         : 172.24.11.129
		InterfaceIndex    : 17
		InterfaceAlias    : vEthernet (Default Switch)
		AddressFamily     : IPv4
		Type              : Unicast
		PrefixLength      : 28
		PrefixOrigin      : Manual
		SuffixOrigin      : Manual
		AddressState      : Preferred
		ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
		PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
		SkipAsSource      : False
		PolicyStore       : ActiveStore
		
	```
	
8.	Check Network Route
	
	PS C:\Windows\system32> Get-NetRoute

	```	
		ifIndex DestinationPrefix                              NextHop                                  RouteMetric ifMetric PolicyStore
		------- -----------------                              -------                                  ----------- -------- -----------
		5       255.255.255.255/32                             0.0.0.0                                          256 25       ActiveStore
		19      255.255.255.255/32                             0.0.0.0                                          256 25       ActiveStore
		10      255.255.255.255/32                             0.0.0.0                                          256 45       ActiveStore
		11      255.255.255.255/32                             0.0.0.0                                          256 65       ActiveStore
		17      255.255.255.255/32                             0.0.0.0                                          256 5000     ActiveStore
		1       255.255.255.255/32                             0.0.0.0                                          256 75       ActiveStore
		5       224.0.0.0/4                                    0.0.0.0                                          256 25       ActiveStore
		19      224.0.0.0/4                                    0.0.0.0                                          256 25       ActiveStore
		10      224.0.0.0/4                                    0.0.0.0                                          256 45       ActiveStore
		11      224.0.0.0/4                                    0.0.0.0                                          256 65       ActiveStore
		17      224.0.0.0/4                                    0.0.0.0                                          256 5000     ActiveStore
		1       224.0.0.0/4                                    0.0.0.0                                          256 75       ActiveStore
		10      192.168.1.255/32                               0.0.0.0                                          256 45       ActiveStore
		10      192.168.1.7/32                                 0.0.0.0                                          256 45       ActiveStore
		10      192.168.1.0/24                                 0.0.0.0                                          256 45       ActiveStore
	###	17      172.24.11.143/32                               0.0.0.0                                          256 5000     ActiveStore
	###	17      172.24.11.129/32                               0.0.0.0                                          256 5000     ActiveStore
	###	17      172.24.11.128/28                               0.0.0.0                                          256 5000     ActiveStore
		1       127.255.255.255/32                             0.0.0.0                                          256 75       ActiveStore
		1       127.0.0.1/32                                   0.0.0.0                                          256 75       ActiveStore
		1       127.0.0.0/8                                    0.0.0.0                                          256 75       ActiveStore
	###	10      0.0.0.0/0                                      192.168.1.1                                        0 45       ActiveStore
	```	

9.	Check minikube and kubectl version
	
	PS C:\Windows\system32> `minikube.exe version`
	
	minikube version: v0.28.0
	
	PS C:\Windows\system32> `kubectl version`
	
	Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.0", GitCommit:"925c127ec6b946659ad0fd596fa959be43f0cc05", GitTreeState:"clean", BuildDate:"2017-12-15T21:07:38Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"windows/amd64"}
	Unable to connect to the server: dial tcp [::1]:8080: connectex: No connection could be made because the target machine actively refused it.
	
10. Prepare minikube config.json file to parameterise configurable fields. Observer the user's home folder will now have `.minikube` directory with file placed at C:\Users\<username>\.minikube\config\config.json

	PS C:\Windows\system32> `minikube config set cpus 4`
	
	PS C:\Windows\system32> `minikube config set memory 8096`
	
	PS C:\Windows\system32> `minikube config set vm-driver hyperv`
	
	PS C:\Windows\system32> `minikube config set hyperv-virtual-switch "Default Switch"`
	
	PS C:\Windows\system32> `minikube config set kubernetes-version v1.9.0`
	
	PS C:\Windows\system32> `minikube config set v 9`
	
	View the minikube config 
	
	PS C:\Windows\system32> `minikube.exe config view`
	
	- vm-driver: hyperv
	- cpus: 4
	- hyperv-virtual-switch: Default Switch
	- kubernetes-version: v1.9.0
	- memory: 8096
	- v: 9
	
11.	Now start the first run of minikube and observer the screen logs.

	PS C:\Windows\system32> `minikube start`
	
	...........
	.............
	Moving files into cluster...
	Setting up certs...
	Connecting to cluster...
	[executing ==>] : C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NoProfile -NonInteractive ( Hyper-V\Get-VM minikube ).state
	[stdout =====>] : Running
	
	[stderr =====>] :
	[executing ==>] : C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NoProfile -NonInteractive (( Hyper-V\Get-VM minikube ).networkadapters[0]).ipaddresses[0]
	[stdout =====>] : 172.19.209.185
	
	[stderr =====>] :
	Setting up kubeconfig...
	Starting cluster components...
	Kubectl is now configured to use the cluster.
	Loading cached images from config file.
	###if you observer something like this your setup is successful.
	

## Reference Links and Special Thanks to
[Nicolas Prigent](https://www.red-gate.com/simple-talk/sysadmin/virtualization/whats-new-microsoft-hyper-v-windows-10-fall-creators-update/)
[Sarah Cooley](https://blogs.technet.microsoft.com/virtualization/2017/11/13/whats-new-in-hyper-v-for-windows-10-fall-creators-update/)
[Minikube Issue No 1627](https://github.com/kubernetes/minikube/issues/1627)

