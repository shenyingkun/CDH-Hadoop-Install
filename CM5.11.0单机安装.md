## 1.配置hosts
    vi /etc/hosts
    192.168.190.134 m1
## 2.安装jdk
    vi /etc/profile
     
    #set java environment
    export JAVA_HOME=/usr/local/jdk1.7.0_80
    export LASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$PATH:$JAVA_HOME/bin
     
    source /etc/profile
## 7.配置安装mysql
     yum install -y mysql mysql-server mysql-devel
     chkconfig mysqld on
     service mysqld start
     mysql                                ## 启动MySQL
     mysql> use mysql;
     mysql> grant all privileges on *.* to 'root'@'%' identified by 'thinker' with grant option;
     mysql> flush privileges;
     mysql> delete from user where host !='%';
     mysql> flush privileges;
## 8 安装依赖包
     yum install -y chkconfig python bind-utils psmisc libxslt zlib sqlite cyrus-sasl-plain cyrus-sasl-gssapi fuse fuse-libs redhat-lsb
## 9. 安装cm server和angent 上传cm 安装包 和 cdh的文件到 /soft目录
     tar -zxvf cloudera-manager-el6-cm5.11.0_x86_64.tar.gz.tar -C /opt/
## 11.创建用户 cloudera-scm
     useradd --system --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm
## 12.配置cm agent 修改文件 
     vi /opt/cloudera-manager/cm-5.11.0/etc/cloudera-scm-agent/config.ini
     修改: server_host=server1 (指定server是管理点)
## 13.拷贝mysql的驱动包到 /usr/share/java (名称改为mysql-connector-java.jar,没有文件夹就创建)
     cp mysql-connector-java-5.1.38-bin.jar /usr/share/java
     cp mysql-connector-java-5.1.38-bin.jar /opt/cm-5.11.0/share/cmf/lib/
     cd /usr/share/java
     mv mysql-connector-java-5.1.38-bin.jar mysql-connector-java.jar
     cd /opt/cm-5.11.0/share/cmf/lib/
     mv mysql-connector-java-5.1.38-bin.jar mysql-connector-java.jar
## 14.Mysql 创建用户
     grant all on *.* to 'cmmanager'@'%' identified by 'cmmanager' with grant option;
     flush privileges;
     create database hive DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
     create database oozie DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
     create database amon DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
     create database hue DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
## 15.执行脚本(只在server1) 执行:
     /opt/cloudera-manager/cm-5.11.0/share/cmf/schema/scm_prepare_database.sh mysql cmmanager -h server1 -ucmmanager -pcmmanager --scm-host server1 scm scm scm
## 16.创建parcel目录:(注意如果不赋予权限安装cm不会出现本地源的parcel)
     chown cloudera-scm:cloudera-scm /opt/cloudera/parcel-repo/*
     chown cloudera-scm:cloudera-scm /opt/cloudera/parcels/*
## 17.copy parcel文件
     cp /usr/java/mysource/CDH-5.11.0-1.cdh5.11.0.p0.34-el6.parcel* manifest.json /opt/cloudera/parcel-repo/
## 18.启动cm
     /opt/cm-5.11.0/etc/init.d/cloudera-scm-server start
     /opt/cm-5.11.0/etc/init.d/cloudera-scm-agent start
