# Minikube setup

## Steps to setup and configure minikube

1.	Create a folder and set it as home for minikube
	
		$ sudo mkdir -p /appvol/devops

		$ sudo chmod 744 /appvol/devops

		$ sudo chown devops:logger /appvol/devops


2.  Set the Minikube Environment Variables (update user profile .bash_profile as appropriate)

		$ export MINIKUBE_HOME=/appvol/devops/
	
	view the env_vars
		
		$ echo $MINIKUBE_HOME


3.  Set proxy setting in case of behind the firewall

		$ export HTTP_PROXY=http://username:password@Proxy_Server_Address:PORT/
	
		$ export HTTPS_PROXY=https://username:password@Proxy_Server_Address:PORT/
	
		$ export no_proxy=$no_proxy,$(minikube ip)
	

4.  Download VirtualBox https://www.virtualbox.org

	Follow the steps to install 


5.  Donwload minikube and kubectl for darwin (Mac OSX)

		$ curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/v1.10.3/bin/darwin/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/
	
		$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.28.0/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
	
	
	* Check for the release version you would like to use. 
		MiniKube release version can be found https://github.com/kubernetes/minikube/releases 
		Kubectl stable release https://storage.googleapis.com/kubernetes-release/release/stable.txt
	

6.  Ensure path includes minikube and kubectl

	/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
	
	Check the downloaded version 
	
		$ minikube version
		
		$ kubectl version
		

7.  Configure minikube. I like to use specific folder location for minikube and maintain kubernetes version inline with production

	set CPUS to use 4
	
		$ minikube config set cpus 4

	set memory to use 8096(8GB)
	
		$ minikube config set memory 8096

	set kubernetes version to v1.9.4
	
		$ minikube config set kubernetes-version v1.9.4

	view all minikube config
	
		$ minikube config view

		or

		$ cat /appvol/devops/.minikube/config/config.json

8.  Now start minikube

		$ minikube start

		sample out put
		"	Starting local Kubernetes v1.9.4 cluster...
			Starting VM...
			Downloading Minikube ISO
				153.08 MB / 153.08 MB [============================================] 100.00% 0s
			Getting VM IP address...
			Moving files into cluster...
			Downloading kubeadm v1.9.4
			Downloading kubelet v1.9.4
			Finished Downloading kubelet v1.9.4
			Finished Downloading kubeadm v1.9.4
			Setting up certs...
			Connecting to cluster...
			Setting up kubeconfig...
			Starting cluster components...
			Kubectl is now configured to use the cluster.
			Loading cached images from config file."
		
		"	~ $minikube status
			minikube: Running
			cluster: Running
			kubectl: Correctly Configured: pointing to minikube-vm at 192.168.99.100"
					
Links for Windows
https://github.com/kubernetes/minikube/issues/1627

environment:
Windows 10 Enterprise
Version: 1703
OS Build: 15063.786
Running Docker for Windows with Hyper-V
$ docker --version Docker version 17.09.0-ce, build afdb6d4
$ minikube version minikube version: v0.24.1
I needed to create the internal switch:
  New-VMSwitch -SwitchName "NATSwitch" -SwitchType Internal
  New-NetIPAddress –IPAddress 192.168.1.1 -PrefixLength 24 -InterfaceAlias "vEthernet (NATSwitch)"
And create the NAT:
New-NetNat –Name MinikubeNetwork -InternalIPInterfaceAddressPrefix 192.168.0.0/24
Start the minikube using the Switch:
minikube start --vm-driver="hyperv" --hyperv-virtual-switch="NATSwitch" --v=7 --alsologtostderr
Then:
$ minikube ip
192.168.0.10
$ kubectl cluster-info
Kubernetes master is running at https://192.168.0.10:8443
