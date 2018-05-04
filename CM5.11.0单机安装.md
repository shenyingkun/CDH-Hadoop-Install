## 1.配置主机信息
    vi /etc/hosts
    192.168.190.134 m1
    关闭防火墙
    service iptables stop
    chkconfig iptables off 
    关闭selinux
    setenforce 0                    ##临时关闭
    vi /etc/selinux/config          ##重启生效
    SELINUX=disabled
## 2.安装jdk
    tar -zxvf jdk-7u80-linux-x64.tar.gz -C /usr/local/
    
    vi /etc/profile
    #set java environment
    export JAVA_HOME=/usr/local/jdk1.7.0_80
    export LASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$PATH:$JAVA_HOME/bin
     
    source /etc/profile
## 3.配置安装mysql数据库
    yum install -y mysql mysql-server mysql-devel
    chkconfig mysqld on
    service mysqld start
    mysql                                
    mysql> use mysql;
    mysql> grant all privileges on *.* to 'root'@'%' identified by 'thinker' with grant option;
    mysql> flush privileges;
    mysql> delete from user where host !='%';
    mysql> flush privileges;
## 4.安装依赖包
    yum install -y chkconfig python bind-utils psmisc libxslt zlib sqlite cyrus-sasl-plain cyrus-sasl-gssapi fuse fuse-libs redhat-lsb
## 5.安装server和angent
    tar -zxvf cloudera-manager-el6-cm5.11.0_x86_64.tar.gz.tar -C /opt/
## 6.创建用户
    useradd --system --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm
## 7.配置文件 
    vi /opt/cm-5.11.0/etc/cloudera-scm-agent/config.ini
    修改: server_host=m1 (指定server是管理点)
## 8.拷贝驱动包
    mkdir /usr/share/java
    cp mysql-connector-java.jar /usr/share/java
    cp mysql-connector-java.jar /opt/cm-5.11.0/share/cmf/lib/
## 9.Mysql 创建用户
    mysql -uroot -p
    mysql> grant all privileges on *.* to 'root'@'m1' identified by 'thinker' with grant option;
    mysql> flush privileges;
    mysql> create database hive DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
    mysql> create database oozie DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
    mysql> create database amon DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
    mysql>create database hue DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
    
## 10.执行脚本(只在管理点执行）
     /opt/cm-5.11.0/share/cmf/schema/scm_prepare_database.sh mysql cm -h localhost -uroot -p --scm-host master root thinker scm
## 11.copy parcel文件并授权(不赋予权限web不出现本地源parcel)
     cp /soft/CDH-5.11.0/CDH-* /opt/cloudera/parcel-repo/
     cp /soft/manifest.json /opt/cloudera/parcel-repo/
     cd /opt/cloudera/parcel-repo/
     mv CDH-5.11.0-1.cdh5.11.0.p0.34-el6.parcel.sha1 CDH-5.11.0-1.cdh5.11.0.p0.34-el6.parcel.sha
     chown cloudera-scm:cloudera-scm /opt/cloudera/parcel-repo/*
     chown cloudera-scm:cloudera-scm /opt/cloudera/parcels/*
## 12.启动
     /opt/cm-5.11.0/etc/init.d/cloudera-scm-server start
     /opt/cm-5.11.0/etc/init.d/cloudera-scm-agent start
