# VirtualBox安装Ubuntu-18.04-Server笔记

## 准备
- 安装'Windows Terminal'
- 安装WSL
- 安装VirtualBox

## 安装
1. 虚拟磁盘映像文件选择创建在SSD（如果有）
1. 选择openssh，公钥从GitHub获取，前提是GitHub已经上传了本机的公钥
1. 更新软件：apt update, apt upgrade, apt autoremove

## 网络配置
1. 基本配置
  - 网卡1（网络地址转换NAT）
  - 网卡2（Host-Only）
    - `sudo vim /etc/netplan/50-cloud-init.yaml`
    - 配置
      ```yml
        network:
          ethernets:
            enp0s3:
              dhcp4: true
            enp0s8:
              dhcp4: false
              addresses: [192.168.56.101/24]
          version: 2
      ```
    - `sudo netplan try`
    - `sudo netplan apply`
2. 代理配置
  - 安装'proxychains4'
  - `sudo ln -s /usr/bin/proxychains4 /usr/local/bin/pc`
  - 编辑'/etc/proxychains4.conf'，添加`协议 IP 端口`
  
## 创建快照
为一个基本配置好的状态创建快照，方便以后出问题回滚到当前状态

## 无界面启动
1. GUI
2. CLI
  - 添加'C:\Program Files\Oracle\VirtualBox'环境变量'PATH'中
  - 命令
    - VBoxManage list vms | runningvms
    - VBoxManage showvminfo xx
    - VBoxManage controlvm xx pause|resume|reset|poweroff|savestate
    - VBoxManage startvm xx --type gui|sdl|headless|separate
  - wsl创建快捷命令，编辑.bash_aliases文件
    ```
      # VirtualBox comand
      alias vbstatus='VBoxManage.exe list runningvms'
      alias vbstop='VBoxManage.exe controlvm ubuntu-server savestate'
      alias vbstart='VBoxManage.exe startvm ubuntu-server --type headless'
    ```
    
## SSH
- 安装'Windows Terminal'
- 编辑配置
  - 添加profiles
  - "commandline" : "ssh xxx@192.168.56.101"
  - guid生成：`uuidgen`命令


  
  