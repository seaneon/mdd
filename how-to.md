sudo apt update
sudo apt upgrade -y
sudo apt install -y bridge-utils qemu-kvm cloud-utils
#kvm-ok
sudo vi /etc/modules-load.d/br_nf.conf
sudo vi /etc/modules-load.d/br_nf.conf
sudo modprobe br_netfilter
sudo vi /etc/sysctl.d/10-disable-firewall-on-bridge.conf
sudo vi /etc/sysctl.d/10-ip-forwarding.conf
sudo vim /etc/sysctl.d/10-disable-firewall-on-bridge.conf
sudo vim /etc/sysctl.d/10-ip-forwarding.conf
sudo sysctl -p /etc/sysctl.d/10-disable-firewall-on-bridge.conf
sudo sysctl -p /etc/sysctl.d/10-ip-forwarding.conf
sudo sysctl -a | grep net.bridge.bridge
sudo mkdir -p /etc/qemu/
echo "allow br0" | sudo tee /etc/qemu/bridge.conf
sudo ip link add name br0 type bridge
sudo ip address add 172.16.0.1/24 dev br0
sudo ip address add 172.16.0.1/24 dev br0
sudo ip link set br0 up
sudo ip address add 172.16.0.1/24 dev br0
mkdir -p /var/log/qemu/
sudo mkdir -p /var/kvm/images
cd /var/kvm/images/
sudo chown seaneon: /var/kvm/images
MAC=$(printf "aa:a3:a3:%02x:%02x:%02x\n" $((RANDOM%256)) $((RANDOM%256)) $((RANDOM%256)))
KEY1=$(cat ~/.ssh/id_rsa.pub)
vim meta-data.yaml
vim user-data.yaml
vim net-config.yaml
wget https://cloud-images.ubuntu.com/releases/22.04/release/ubuntu-22.04-server-cloudimg-amd64.vmdk
qemu-img convert -O qcow2 /var/kvm/images/ubuntu-22.04-server-cloudimg-amd64.vmdk /var/kvm/images/ubuntu-22.04-server-cloudimg-amd64.img
cloud-localds cloud-init.iso user-data.yaml meta-data.yaml --network-config=net-config.yaml
sudo /sbin/iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
sudo /sbin/iptables -A FORWARD -i ens3 -o br0 -m state  --state RELATED,ESTABLISHED -j ACCEPT
sudo /sbin/iptables -A FORWARD -i br0 -o ens3 -j ACCEPT
sudo /usr/bin/qemu-system-x86_64    -enable-kvm    -drive file=/var/kvm/images/ubuntu-22.04-server-cloudimg-amd64.img,if=virtio    -cdrom /var/kvm/images/cloud-init.iso    -display curses    -nographic    -smp cpus=1    -m 1G    -net nic,netdev=tap1,macaddr=$MAC1    -netdev bridge,id=tap1,br=br0   -d int    -D /var/log/qemu/qemu.log    
