# 설치 블로그
https://www.skyer9.pe.kr/wordpress/?p=6630

# 도커 삭제
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-buildx-plugin docker-compose-plugin 
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-buildx-plugin docker-compose-plugin 

sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock

sudo rm -rf /usr/local/bin/docker-compose
sudo rm -rf /etc/docker
sudo rm -rf ~/.docker

# k8s 삭제
kubeadm reset
sudo apt-get purge -y kubeadm kubectl kubelet kubernetes-cni kube*   
sudo apt-get -y autoremove  
sudo rm -rf ~/.kube


# key 오류
sudo mkdir -p /etc/apt/keyrings
echo "deb [signed-by=/etc/apt/keyrings/kubernetes.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes.gpg




# 8080 오류
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config



# docker container 오류
sudo rm /etc/containerd/config.toml
systemctl restart containerd


# Worker Join
sudo kubeadm join 192.168.0.29:6443 --token 6wa90e.powjpgwog6o72rrz \
        --discovery-token-ca-cert-hash sha256:a8ea6c33639e68463926a3f1b7508f0e6da26ca3819088ca5708f23f2feee0c1 
	
