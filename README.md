# minikube setup

steps to setup and configure minikube

1.	Create a folder and set it as home for minikube
	
	sudo mkdir -p /appvol/devops
	
	sudo chmod 744 /appvol/devops
	
	sudo chown devops:logger /appvol/devops


2.  Set the Minikube Environment Variables

	export MINIKUBE_HOME=/appvol/devops/
	
	view the env_vars
	
	~$ echo $MINIKUBE_HOME


3.  Set proxy setting in case of behind the firewall

	export HTTP_PROXY=http://username:password@Proxy_Server_Address:PORT/
	
	export HTTPS_PROXY=https://username:password@Proxy_Server_Address:PORT/
	
	export no_proxy=$no_proxy,$(minikube ip)
	


4.  Download VirtualBox https://www.virtualbox.org

	Follow the steps to install 


5.  Donwload minikube and kubectl for darwin (Mac OSX)

	curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/v1.10.3/bin/darwin/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/
	
	curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.28.0/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
	
	
	* check for the release version you would like to use. Latest stable version can be found https://github.com/kubernetes/minikube/releases and https://storage.googleapis.com/kubernetes-release/release/stable.txt
	

6.  Ensure path includes minikube and kubectl

	/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
	
	Check the downloaded version 
	
		~ $ minikube version
		
		~ $kubectl version
		

7.  Configure minikube. I like to use specific folder location for minikube and maintain kubernetes version inline with production

	set CPUS to use 4
	
		~$ minikube config set cpus 4

	set memory to use 8096(8GB)
	
		~$ minikube config set memory 8096

	set kubernetes version to v1.9.4
	
		~$ minikube config set kubernetes-version v1.9.4

	view all minikube config
	
		~$ minikube config view

		or

		~$ cat /appvol/devops/.minikube/config/config.json

8.  Now start minikube

		~$ minikube start
		
