# 설치 블로그
## docker
https://www.skyer9.pe.kr/wordpress/?p=7073

## kubernetes
https://www.skyer9.pe.kr/wordpress/?p=6630

# 초기화 방법

## 도커 삭제
```
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-buildx-plugin docker-compose-plugin 
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-buildx-plugin docker-compose-plugin 

sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock

sudo rm -rf /usr/local/bin/docker-compose
sudo rm -rf /etc/docker
sudo rm -rf ~/.docker
```

## k8s 삭제
```
kubeadm reset
sudo apt-get purge -y kubeadm kubectl kubelet kubernetes-cni kube*   
sudo apt-get -y autoremove  
sudo rm -rf ~/.kube
```

# docker 설치

## 필수 패키지 설치
```
sudo apt-get update

sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

## repo 추가
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## Docker, containerd 설치
```
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

sudo docker version
```

## 실행
```
sudo systemctl enable docker
sudo systemctl start docker

sudo systemctl enable containerd
sudo systemctl start containerd
```


## docker sudo 없이 실행
```
sudo groupadd docker
sudo gpasswd -a $USER docker
sudo service docker restart
```

# Kubernetes 설치
## 방화벽 off
```
sudo ufw disable
```

## Swap off
```
sudo swapoff -a && sudo sed -i '/swap/s/^/#/' /etc/fstab
```

## kubelet, kubeadm, kubectl 설치 (모든 master, worker node)
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system

sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list


sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl


sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl


sudo systemctl daemon-reload
sudo systemctl restart kubelet
```


## Master(Control Plane)만 설치
```
sudo kubeadm init

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

```

## Worker 설치
```
sudo kubeadm join 172.31.16.157:6443 --token ou08ek.xzjXXXXXXXXXXXXX \
        --discovery-token-ca-cert-hash sha256:6e0305d27b12b3f8b51d8e021138d59227124c2XXXXXXXXXXXXXXXX
```


## Master 마무리
```
kubectl get nodes


source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

# 오류 해결법
오류가 발생하면 다음의 명령어들로 일을 해결하자. 

## key 오류
```
sudo mkdir -p /etc/apt/keyrings
echo "deb [signed-by=/etc/apt/keyrings/kubernetes.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes.gpg
```

## 8080 오류
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## docker container 오류
```
sudo rm /etc/containerd/config.toml
systemctl restart containerd
```
