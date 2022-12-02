# DSS+Linkis Ansible一键安装脚本
DSS1.1.1 &amp; Linkis 1.3.0 Ansible 一键部署脚本

## 一、简介

为解决繁琐的部署流程，简化安装步骤，本脚本提供一键安装最新版本的DSS+Linkis环境；部署包中的软件采用我自己编译的安装包，并且为最新版本：`DSS1.1.1` + `Linkis1.3.0`。

因安装包高达3.5G，无法上传到Github上，可使用如下方式获取安装包：

- 链接: https://pan.baidu.com/s/1NYVFqyVwMdMQUBjmeUtGmQ?pwd=kddf 
- 提取码: kddf 

### 1.1 版本介绍

以下版本及配置信息可参考安装程序`hosts`文件中的`[all:vars]`字段。

| 软件名称         | 软件版本     | 应用路径              | 测试/连接命令                            |
| ---------------- | ------------ | --------------------- | ---------------------------------------- |
| MySQL            | mysql-5.6    | /usr/local/mysql      | mysql -h 127.0.0.1 -uroot -p123456       |
| JDK              | jdk1.8.0_171 | /usr/local/java       | java -version                            |
| Python           | python 2.7.5 | /usr/lib64/python2.7  | python -V                                |
| Nginx            | nginx/1.20.1 | /etc/nginx            | nginx -t                                 |
| Hadoop           | hadoop-2.7.2 | /opt/hadoop           | hdfs dfs -ls /                           |
| Hive             | hive-2.3.3   | /opt/hive             | hive -e "show databases"                 |
| Spark            | spark-2.4.3  | /opt/spark            | spark-sql -e "show databases"            |
| dss              | dss-1.1.1    | /home/hadoop/dss      | http://<服务器IP>:8085                   |
| links            | linkis-1.3.0 | /home/hadoop/linkis   | http://<服务器IP>:8188                   |
| zookeeper        | 3.4.6        | /usr/local/zookeeper  | 无                                       |
| DolphinScheduler | 1.3.9        | /opt/dolphinscheduler | http://<服务器IP>:12345/dolphinscheduler |
| Visualis         | 1.0.0        | /opt/visualis-server  | http://<服务器IP>:9088                   |
| Qualitis         | 0.9.2        | /opt/qualitis         | http://<服务器IP>:8090                   |
| Streamis         | 0.2.0        | /opt/streamis         | http://<服务器IP>:9188                   |
| Soop             | 1.4.6        | /opt/sqoop            | sqoop                                    |
| Exchangis        | 1.0.0        | /opt/exchangis        | http://<服务器IP>:8028                   |

## 二、部署前注意事项

**要求**：

- 本脚本仅在`CentOS 7`系统上测试过，请确保安装的服务器为`CentOS 7`。
- 安装前请关闭服务器防火墙及SElinux，并使用`root`用户进行操作。
- 安装服务器必须通畅的访问互联网，脚本需要yum下载一些基础软件。
- 保证服务器未安装任何软件，包括不限于`java`、`mysql`、`nginx`等，最好是全新系统。
- 必须保证服务器除`lo:127.0.0.1`回环地址外，仅只有一个IP地址，可使用`echo $(hostname -I)`命令测试。

## 三、部署方法

本案例部署主机IP为`192.168.1.52`，以下步骤请按照自己实际情况更改。

#### 2.1 安装前设置

```bash
### 安装ansible
$ yum -y install epel-release
$ yum -y install ansible
### 配置免密
$ ssh-keygen -t rsa
$ ssh-copy-id root@192.168.1.52
```

#### 2.2 部署linkis+dss

```bash
### 解压安装包
$ tar zxvf dss-linkis-ansible.tar.gz
$ cd dss-linkis-ansible
# 目录说明
$ dss-linkis-ansible
├── ansible.cfg    # ansible 配置文件
├── hosts          # hosts主机及变量配置
├── playbooks      # playbooks剧本
├── README.md      # 说明文档
└── roles          # 角色配置
### 配置部署主机（注：ansible_ssh_host的值不能设置127.0.0.1）
$ vim hosts
[deploy]
dss-service ansible_ssh_host=192.168.1.52 ansible_ssh_port=22
# 一键安装Linkis+DSS
$ ansible-playbook playbooks/all.yml
```

执行结束后，即可访问：http://192.168.1.52 查看信息页面，上面记录了所有服务的访问地址及账号密码。

#### 2.3 部署其它服务

```bash
# 安装dolphinscheduler
$ ansible-playbook playbooks/dolphinscheduler.yml
### 注: 安装以下服务必须优先安装dolphinscheduler调度系统
# 安装visualis
$ ansible-playbook playbooks/visualis.yml 
# 安装qualitis
$ ansible-playbook playbooks/qualitis.yml
# 安装streamis
$ ansible-playbook playbooks/streamis.yml
# 安装exchangis
$ ansible-playbook playbooks/exchangis.yml
```
