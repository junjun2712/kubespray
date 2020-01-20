yum -y install epel-release

yum install python36 –y


cd /data

wget https://github.com/kubernetes-sigs/kubespray/archive/v2.11.0.tar.gz

tar zxvf v2.11.0.tar.gz

cd kubespray

pip3 install -r requirements.txt

cp -r inventory/sample inventory/mycluster

============================================================================

vi inventory/mycluster/hosts.ini

[k8s-cluster:children]

kube-master

kube-node

[all]

node2 ansible_host=192.168.254.139  ip=192.168.254.139

node3 ansible_host=192.168.254.140  ip=192.168.254.140

node4 ansible_host=192.168.254.141  ip=192.168.254.141

node5 ansible_host=192.168.254.142  ip=192.168.254.142



[kube-master]
node2
node3
node4

[kube-node]
node5


[etcd]
node2
node3
node4

[calico-rr]




============================================================================

部署集群

ansible-playbook -i inventory/mycluster/hosts.ini cluster.yml

升级ingress 插件

ansible-playbook -i inventory/mycluster/hosts.ini cluster.yml --tags=ingress-nginx

添加节点

ansible-playbook -i inventory/mycluster/hosts.ini scale.yml -b -v -u root -k

删除节点

ansible-playbook -i inventory/mycluster/hosts.ini remove-node.yml -b -v

kubespray运维经验

a.如果需要扩容Work节点，则修改hosts.ini文件，增加新增的机器信息。然后执行下面的命令：

ansible-playbook -i inventory/mycluster/hosts.ini scale.yml -b -v -k

b.将hosts.ini文件中的master和etcd的机器增加到多台，执行部署命令

ansible-playbook -i inventory/mycluster/hosts.ini cluster.yml -b -vvv

c.刪除节点，如果不指定节点就是刪除整个集群：

如果需要减去结点，只要修改hosts.ini文件，减少对应的结点，减哪个留哪个之后执行

ansible-playbook -i inventory/mycluster/hosts.ini remove-node.yml -b -v

d.如果需要卸载，可以执行以下命令：

ansible-playbook -i inventory/mycluster/hosts.ini reset.yml -b –vvv

e.升级K8s集群，选择对应的k8s版本信息，执行升级命令。涉及文件为upgrade-cluster.yml。

ansible-playbook upgrade-cluster.yml -b -i inventory/mycluster/hosts.ini -e kube_version=vX.XX.XX -vvv





