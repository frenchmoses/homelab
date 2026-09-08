
# General Setup #

Sign in as root and install some helpful tools  
```
apt install sudo tree wget -y && apt update
```
Asign your normal user to the sudo group
```
usermod -aG sudo {user}
```

### [Optional] Assign a static IP ###

1. Modify the interfaces file
```
nano /etc/network/interfaces
```
2. You should see lines similar to this
 ```
auto ens18
iface ens18 inet dhcp
```
3. Modify them to reflect something like this
```
auto ens18
iface ens18 inet static
	address 10.0.0.202
	netmask 255.255.255.0
	gateway 10.0.0.1
```
4. Check DNS
```
nano /etc/resolv.conf
```
5. Add these lines if something similar isnt already present
```
nameserver 1.1.1.1
nameserver 8.8.8.8
```
6. Restart networking
```
systemctl restart networking
```

Sign is as your normal user. Alternatively, you could open a command prompt and ssh in  
```
ssh {user}@{IP}
```


# Install Docker #

Official *[Install Guide](https://docs.docker.com/engine/install/debian/#install-using-the-repository)*

### 1. Uninstall old versions ###
```
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-doc docker-buildx podman-docker containerd runc | cut -f1)
```

### 2. Set up Docker's apt repository ###

Add Docker's official GPG key
```
sudo apt update && \
sudo apt install ca-certificates curl gnupg && \
sudo install -m 0755 -d /etc/apt/keyrings && \
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc && \
sudo chmod a+r /etc/apt/keyrings/docker.asc  
```

Add the repository to apt sources
```
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
	Types: deb
	URIs: https://download.docker.com/linux/debian
	Suites: trixie
	Components: stable
	Architectures: $(dpkg --print-architecture)
	Signed-By: /etc/apt/keyrings/docker.asc
EOF
```


### 3. Install the Docker packages ###

```
sudo apt update && \
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify docker is running
```
sudo systemctl status docker
```
If not start it manually
```
sudo systemctl start docker
```
### 4. Verify that the installation is successful by running the `hello-world` image ###

```
sudo docker run hello-world
```

# Setup *Arr Stack #

Assign the current user to the `docker` group
```
sudo usermod -aG docker $USER && newgrp docker
```
Create a `docker` directory, change into it, and assigned persmissions
```
mkdir /docker && cd /docker && sudo chown -R 1000:1000 /docker
```
Grab the `docker-compose` and `.env.template` file from this repo. The `.env` file is what docker will you use and what you will modify. The template is a safe backup. 
```
wget https://github.com/frenchmoses/homelab/blob/main/media/compose.yaml && \
wget https://github.com/frenchmoses/homelab/blob/main/media/.env.template && \
cp .env.template .env && \
chmod 600 .env
```
Create the `/data` pathing and assign permissions
```
mkdir -p /data/{media/{tv,movies,music,books},downloads/{complete/{tv,movies,music,books},incomplete/{tv,movies,music,books}}} && \
sudo chown -R 1000:1000 /data
```
Use `tree` to see the sctucture
```
tree /data
```




