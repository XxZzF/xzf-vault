# 上东Maven配置.md
```xml
<?xml version="1.0" encoding="UTF-8"?>

<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">


 <!-- <localRepository>本机仓库地址</localRepository> -->

  <pluginGroups>
   <pluginGroup>com.soebes.maven.plugins</pluginGroup>
  
  </pluginGroups>





     <mirrors>
        <mirror>
            <id>rdc-releases</id>
            <mirrorOf>!snapshots</mirrorOf>
            <name>rdc-releases</name>
            <url>https://packages.aliyun.com/maven/repository/2404708-release-sSqSsK/</url>
        </mirror>
          <mirror>
            <id>rdc-snapshots</id>
            <mirrorOf>!releases</mirrorOf>
            <name>snapshots</name>
            <url>https://packages.aliyun.com/maven/repository/2404708-snapshot-ryY8t6/</url>
        </mirror>
            <mirror>
                <id>alimaven</id>
                <name>aliyun maven</name>
                <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
                <mirrorOf>central</mirrorOf>
                </mirror>
    </mirrors>
    <servers>
        <server>
            <id>rdc-releases</id>
            <username>LfdV5E</username>
            <password>T7QJ4xcbqL</password>
        </server>
        <server>
            <id>rdc-snapshots</id>
            <username>LfdV5E</username>
            <password>T7QJ4xcbqL</password>
        </server>
    </servers>
    <profiles>
        <profile>
            <id>rdc</id>
            <properties>
                <altReleaseDeploymentRepository>
                    rdc-releases::default::https://packages.aliyun.com/maven/repository/2404708-release-sSqSsK/
                </altReleaseDeploymentRepository>
                <altSnapshotDeploymentRepository>
                    rdc-snapshots::default::https://packages.aliyun.com/maven/repository/2404708-snapshot-ryY8t6/
                </altSnapshotDeploymentRepository>
            </properties>
            <repositories>
           
                <repository>
                    <id>rdc-releases</id>
                    <url>https://packages.aliyun.com/maven/repository/2404708-release-sSqSsK/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </repository>
                <repository>
                    <id>rdc-snapshots</id>
                    <url>https://packages.aliyun.com/maven/repository/2404708-snapshot-ryY8t6/</url>
                    <releases>
                        <enabled>false</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
            <pluginRepositories>
             
                <pluginRepository>
                    <id>rdc-releases</id>
                    <url>https://packages.aliyun.com/maven/repository/2404708-release-sSqSsK/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </pluginRepository>
                <pluginRepository>
                    <id>rdc-snapshots</id>
                    <url>https://packages.aliyun.com/maven/repository/2404708-snapshot-ryY8t6/</url>
                    <releases>
                        <enabled>false</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
    </profiles>
    <activeProfiles>
        <activeProfile>rdc</activeProfile>
    </activeProfiles>
</settings>
```