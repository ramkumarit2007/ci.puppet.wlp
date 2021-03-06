ci.puppet.wlp
=============

Puppet modules for installing and managing WebSphere Application Server Liberty Profile #devops

#Description
  
The liberty module installs and configures WebSphere Application Server Liberty Profile. It provides manifests and templates for creating, managing and configuring Liberty profile server instances.
  
#Basic Configuration
  The liberty module can install Liberty profile from jar archives or zip files. The installation method is configured via the liberty::variables::mode property.
  
  Jar installation
  Jar installation is used to install Liberty from the jar file installable that is provided on wasdev.net. You must set the variable 'liberty::variables::acceptLicense' to true before invoking the class liberty::install_wlp which will install the Liberty server. If you do not set this
  variable the installation will fail. By default the install_wlp class is configured to use the jar installation method. 
  
  Zip installation
  Zip installation is used to install Liberty from a zip archive. The zip is assumed to be generated by running ./bin/server package Liberty command with --include=all or --include=minify option. You would need to set the mode variable to 'zip' in order to trigger
  a zip installation.
  
#Requirements
  Platform:
    Centos
    Redhat

#Modules:
    java
    
Note: The liberty::variables::java_home variable needs to be set for this module to work correctly.
  
#Variables

The class liberty::variables is used to declare the variables that can be used to configure the puppet module liberty.The important variables and their usage are given below

acceptLicense = Whether or not you accept the Liberty License. eg: $acceptLicense = "true"

serverName = The name of the liberty server that should be created on each node. eg: $serverName = "server1"

baseDir = The parent directory into which the liberty profile will be installed. Note that this is just 
the directory name and not the full path. eg: $baseDir = "liberty" 

appNames = Defines the names of the applications that will be installed onto the server. 
           eg: $appNames = [ 'acmeair-webapp-1.0-SNAPSHOT.war','DefaultApplication.ear',]

extremeScaleBinaryName = The name of the WebSphere extremescale install zip archive. eg: $extremeScaleBinaryName="extremescaletrial860.zip"

install_root = The base installation directory for both Liberty Profile and WebSphere ExtremeScale. eg: $install_root="/opt/IBM/"

wxs_serverdirName = The directory into which the WebSphere Extreme Scale server needs to be installed. eg: $wxs_serverdirName = "ObjectGrid", 

extremeScaleLibertyBinaryName = The name of the WebSphere ExtremeScale liberty binary. eg: $extremeScaleLibertyBinaryName="wxs-wlp_8.6.0.4.jar", 

user = The user that is used for installing the packages. eg: $user = "puppetuser", 

serverBinaryName = The name of the WebSphere Liberty Base binary. eg: $serverBinaryName="wlp-developers-runtime-8.5.5.1.jar",

appsDirName = The name of the directory that contains the application content for installation in the files directory of the puppet liberty module. eg: $appsDirName = "apps", 

extendedBinaryName = The name of the WebSphere Liberty extended binary. eg: $extendedBinaryName="wlp-developers-extended-8.5.5.1.jar", 

extrasBinaryName = The name of the WebSphere Liberty Extras binary. eg: $extrasBinaryName="wlp-developers-extras-8.5.5.1.jar", 

mode = The mode of installation. There are two modes of installation namely 'zip' and 'archive'. eg: $mode = "archive",

artifact = The artifact to install. can be 'base', 'extended', 'extras'. eg: $artifact = "base", 

action = The action that should be performed. Possible values are 'install' and 'uninstall'. eg: $action = "install", 

java_home = The directory where Java is installed. eg: $java_home = "/usr/lib/jvm/java-1.6.0"

userHome = The home directory of the user that used for installation.eg: $userHome = "/home/${user}"

puppetFileRoot = This is the location where all the installables should be present in case of standalone puppet. In the case of master agent puppet, this is the location where the master will copy the installable files. eg: $puppetFileRoot="${userHome}"
                 
includesDir = This is the directory where all the xml files that need to be included into server.xml are placed. Each application can provide an xml file snippet that will be included in the final server.xml. This snippet can contain all the features and config that is needed for that application. eg: $includesDir ="${liberty::variables::install_root}/${baseDir}/wlp/usr/servers/${serverName}/includes"
              
extremeScaleBinary = The complete path to the WebSphere ExtremeScale binary. eg: $extremeScaleBinary = "${puppetFileRoot}/${extremeScaleBinaryName}"

extremeScaleLibertyBinary = The complete path to the WebSphere ExtremeScale Liberty binary.eg: $extremeScaleLibertyBinary = "$puppetFileRoot/${extremeScaleLibertyBinaryName}"

dependantLibs = The complete path to the libraries that are needed for the ACME AIR app. eg: $dependantLibs = "$puppetFileRoot/lib"

serverBinary = The complete path to the WebSphere Liberty profile binary. eg: $serverBinary = "${userHome}/${liberty::variables::serverBinaryName}"

applicationSourceDirectory = The complete path to the directory containing application files. eg: $applicationSourceDirectory = "${userHome}/${appsDirName}"

//////applicationFileName = .eg: $applicationFileName = $appNames//////////// what are we using this for??? Is this needed??

extendedBinary = The complete path to the WebSphere Liberty profile extended binary. eg: $extendedBinary = "${puppetFileRoot}/${extendedBinaryName}"

extrasBinary = The complete path to the WebSphere Liberty profile extras binary. eg: $extrasBinary   = "${puppetFileRoot}/${extrasBinaryName}"

path = This is used internally and is the path used for puppet spawned shells. eg: $path = "${liberty::variables::java_home}/bin:/opt/puppet/bin:${liberty::variables::install_root}/${baseDir}/wlp/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin"

wxs_serverdir = The path where WebSphere Extreme Scale should be installed. eg: $wxs_serverdir = "${install_root}/${wxs_serverdirName}"

env_file = The name of the env.sh file for WebSphere Extreme Scale. This is an internal variable and should not be changed. eg: $env_file = "env.sh"

deploymentconfig = The name of the.deployment config file for the WebSphere Extreme Scale instance for the ACME AIR application. eg: $deploymentconfig = "deployment.xml"

objectgridconfig = The name of the object grid config file for WebSphere Extreme Scale instance for the ACME AIR application. eg: $objectgridconfig = "objectgrid.xml"

#Standalone Puppet Usage

 If you want to install Liberty and deploy an application to it on a machine that has puppet installed you need to create a puppet manifest file and paste the following into it.

class { "liberty::variables":
     appNames => ['DefaultApplication.ear',],
     applicationSourceDirectory => "/home/puppetuser/installableApps",
     puppetFileRoot => "/home/puppetuser/installables",
     serverBinaryName => "wlp-developers-runtime-8.5.5.1.jar",
     acceptLicense => �true�,
}->
class { "liberty::install_wlp":} ->
class { "liberty::create_server":}->
class { "liberty::install_application":}->
class { "liberty::start_server": }
 
 First you will need to specify the applications that need to be installed via the appNames variable in the liberty::variables class. You can specify multiple application names as an array. Next you need to specify the applicationSourceDirectory where your application artifacts are present. The installableApps directory can contain the following 2 files for each application

 1) Application Archive. eg: acmeair-webapp-1.0-SNAPSHOT.war
 2) snippet to include in server.xml. eg:acmeair-webapp-1.0-SNAPSHOT.xml
 Note that the names should be the same and the extensions will only be different.
 
The next property puppetFileRoot points to the location where your Liberty installable is present and serverBinaryName to the name of the Liberty installable file. 
Applying this manifest will result in Liberty being installed and the applications specified deployed. Finally the server is started.
 
#Master Agent Puppet Usage

Another usecase would be to install Liberty across multiple nodes. This can be simplified if the nodes are having puppet agents that are federated to a puppet master. In such a case you would need to modify the site.pp manifest file of the puppet master as shown below. You need to define a base node that all your nodes that require Liberty installed should extend
from. Shown below is a sample base Node for installing Liberty and an application.

node base {
   class { "liberty::variables":
     appNames => ['DefaultApplication.ear',],
     applicationSourceDirectory => "/home/puppetuser/installableApps",
     puppetFileRoot => "/home/puppetuser/installables",
     serverBinaryName => "wlp-developers-runtime-8.5.5.1.jar",
   }->
   class { "liberty::copy_files_liberty":}->
   class { "liberty::install_wlp":} ->
   class { "liberty::create_server":}->
   class { "liberty::install_application":}->
   class { "liberty::start_server":
     # require =>  File["${liberty::variables::install_root}/${liberty::variables::baseDir}/wlp/usr/servers/${liberty::variables::serverName}/server.xml"],
   }
}
A Node extending the base node.

node 'libertyagent' inherits base {
}

An important thing to note here is the location of the files including the application files and the installable files. The installable files need to be present in the files directory of the puppet module. So the default location would be /etc/puppetlabs/puppet/modules/liberty/files. The application files should be present in the directory referred to by the appsDirName variable. By default the value of this variable is apps. So the location where the application artifacts should be present is /etc/puppetlabs/puppet/modules/liberty/files/apps. The reason that we need to put these files in this location is to enable the file server present in the puppet master to server these files to the nodes where the puppet agent is running which needs access to these files.


# Notice

© Copyright IBM Corporation 2013, 2014.

# License

```text
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
