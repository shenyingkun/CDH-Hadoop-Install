### 1.sqoop 报错

Error: JAVA_HOME is not set and could not be found.

export JAVA_HOME=/usr/local/jdk1.7.0_80
export LASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
