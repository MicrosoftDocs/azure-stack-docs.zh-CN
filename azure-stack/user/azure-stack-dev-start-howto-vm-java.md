---
title: Azure Stack 中的虚拟机部署 Java WAR |Microsoft Docs
description: 将 Java WAR 部署到 Azure Stack 中的虚拟机。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 28d60e8fc5b575cd2fbefee1298220418e4f59a1
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418237"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>在 Azure Stack 中将 Java web 应用部署到 VM

可以在 Azure Stack 中创建虚拟机 (VM) 以托管 Python web 应用。 本文介绍如何在 Azure Stack 中的 Linux VM 上安装和配置 Apache Tomcat 服务器。 然后, 将 Java Web 应用程序资源 (WAR) 文件加载到服务器中。 WAR 文件用于分发 Java 存档 (JAR) 文件的集合、包含 Java 资源 (如类、文本、图像、XML、HTML) 的压缩文件以及用于传递 web 应用程序的其他资源。

## <a name="create-a-vm"></a>创建 VM

1. 按照[部署 LINUX VM 以在 Azure Stack 中托管 web 应用](azure-stack-dev-start-howto-deploy-linux.md)中的说明, 在 Azure Stack 中设置 VM。

2. 在 "VM 网络" 窗格中, 确保可访问以下端口:

    | Port | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器传递网页的协议。 客户端通过 HTTP 连接 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议 (HTTPS) 是 HTTP 的安全版本, 它需要安全证书, 并允许加密传输信息。 |
    | 22 | SSH | 安全外壳 (SSH) 是一种用于安全通信的加密网络协议。 将此连接与 SSH 客户端结合使用, 以配置 VM 和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议 (RDP) 允许远程桌面连接在计算机上使用图形用户界面。   |
    | 8080 | 自定义 | Apache Tomcat 服务的默认端口。 对于生产服务器, 可通过80和443路由流量。 |

## <a name="install-java"></a>安装 Java

1. 使用 SSH 客户端连接到 VM。 有关说明, 请参阅[通过 SSH 连接到 PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

2. 在虚拟机上的 bash 提示符下, 运行以下命令:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. 验证安装。 在 SSH 会话中仍连接到 VM, 请运行以下命令:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>安装并配置 Tomcat

1. 使用 SSH 客户端连接到 VM。 有关说明, 请参阅[通过 SSH 连接到 PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 通过执行以下操作创建 Tomcat 用户:

    a. 通过运行以下命令创建新的 Tomcat 组:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. 创建新的 Tomcat 用户。 将此用户添加到包含 */opt/tomcat*主目录的 Tomcat 组。 将 Tomcat 部署到此目录:

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. 通过执行以下操作来安装 Tomcat:

    a. 从[tomcat 8 下载页](http://tomcat.apache.org/download-80.cgi)获取最新版本的 tomcat 8 的 tar URL。

    b. 使用 "连接" 链接来下载最新版本。 运行以下命令：

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. 将 Tomcat 安装到 */opt/tomcat*目录中。 创建文件夹, 然后打开存档:

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. 通过运行以下命令来更新 Tomcat 的权限:

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. 创建*systemd*服务文件, 以便可以将 Tomcat 作为服务运行。

   a. Tomcat 需要知道安装 Java 的位置。 此路径通常称为 *JAVA_HOME*。 运行以下命令找到该位置：

    ```bash  
        sudo update-java-alternatives -l
    ```

    这将产生如下所示的内容:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    可以通过从输出中获取路径并添加 */jre*来构造*JAVA_HOME*变量值。 例如, 使用前面的示例 */usr/lib/jvm/java-1.8.0-openjdk-amd64/jre*。

    b. 使用服务器中的值创建 systemd 服务文件:

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. 将以下内容粘贴到该服务文件中。 如有必要, 请修改*JAVA_HOME*的值, 使其与你在系统中找到的值匹配。 你可能还需要修改在 CATALINA_OPTS 中指定的内存分配设置:

    ```Text  
        [Unit]
        Description=Apache Tomcat Web Application Container
        After=network.target
        
        [Service]
        Type=forking
        
        Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
        Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
        Environment=CATALINA_HOME=/opt/tomcat
        Environment=CATALINA_BASE=/opt/tomcat
        Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
        Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
        
        ExecStart=/opt/tomcat/bin/startup.sh
        ExecStop=/opt/tomcat/bin/shutdown.sh
    
        User=tomcat
        Group=tomcat
        UMask=0007
        RestartSec=10
        Restart=always
        
        [Install]
        WantedBy=multi-user.target
    ```

    d. 保存并关闭该文件。

    e. 重新加载 systemd 守护程序，使其能够识别上述服务文件：

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. 启动 Tomcat 服务: 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. 输入以下内容, 验证它是否已开始没有错误:

    ```bash  
        sudo systemctl status tomcat
    ```

1. 验证 Tomcat 服务器。 Tomcat 使用端口 8080 来接受传统的请求。 运行以下命令，允许流量流向该端口：

    ```bash  
        sudo ufw allow 8080
    ```

    如果尚未为 Azure Stack VM 添加*入站端口规则*, 请立即添加。 有关详细信息，请参阅[创建 VM](#create-a-vm)。

1. 在与 Azure Stack 相同的网络中打开浏览器, 然后打开服务器*yourmachine。 p p.: 8080*。

    ![Azure Stack VM 上的 Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    此时会加载服务器上的 Apache Tomcat 页。 接下来, 将服务器配置为允许访问服务器状态、管理器应用和主机管理器。

1. 启用服务文件，以便在重新启动服务器时自动启动 Tomcat：

    ```bash  
        sudo systemctl enable tomcat
    ```

1. 若要允许自己访问 web 管理界面, 请配置 Tomcat 服务器。 

   a. 编辑*tomcat-users*文件, 并定义角色和用户, 以便可以登录。 将用户定义为可访问 `manager-gui` 和 `admin-gui`。

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. 将以下元素添加到`<tomcat-users>`部分:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    例如, 最终文件可能如下所示:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
        <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    c. 保存并关闭该文件。

1. Tomcat 会将“管理器”和“主机管理器”应用的访问权限限制为来自服务器的连接。 因为你要在 Azure Stack 中的 VM 上安装 Tomcat, 所以需要删除此限制。 通过编辑相应的*上下文 .xml*文件来更改这些应用的 IP 地址限制。

    a. 在管理器应用中更新*上下文 .xml* :

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. 注释掉 IP 地址限制以允许来自任何位置的连接, 或者添加用于连接到 Tomcat 的计算机的 IP 地址。

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. 保存并关闭该文件。

    d. 使用类似更新更新主机管理器应用的*上下文 .xml* :

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. 保存并关闭该文件。

1. 若要用更改更新服务器, 请重新启动 Tomcat 服务:

    ```bash  
        sudo systemctl restart tomcat
    ```

1. 在与 Azure Stack 相同的网络中打开浏览器, 然后打开服务器: yourmachine. *p p. test-azurestack: 8080*。

    a. 若要查看 Tomcat 服务器的状态并验证你是否具有访问权限, 请选择 "**服务器状态**"。

    b. 使用 Tomcat 凭据登录。

    ![Azure Stack VM 上的 Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>创建应用

需要创建一个要部署到 Tomcat 的 WAR。 如果只想要检查您的环境, 可以在[Apache Tomcat 站点](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/)上找到示例 WAR。

有关在 Azure 中开发 Java 应用的指导，请参阅[在 Azure 中生成和部署 Java 应用](https://azure.microsoft.com/develop/java/)。

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 使用 SSH 客户端连接到 VM。 有关说明, 请参阅[通过 SSH 连接到 PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 若要将服务器更新为应用包, 请停止 Tomcat 服务:

    ```bash  
        sudo systemctl stop tomcat
    ```

1. 若要写入到 webapps 文件夹, 请将 FTP 用户添加到 Tomcat 组。 FTP 用户是在 Azure Stack 中创建 VM 时定义的用户。

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. 若要清除 webapps 文件夹, 然后加载新的或更新的 WAR, 请通过 FileZilla 连接到 VM。 有关说明, 请参阅通过[FileZilla 与 SFTP 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla)。

    a. 清除*TOMCAT_HOME/webapps*。

    b. 将 WAR 添加到*TOMCAT_HOME/webapps* (例如 */opt/tomcat/webapps/* )。

1.  Tomcat 会自动扩展并部署应用程序。 您可以使用之前创建的 DNS 名称来查看它。 例如：

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample
    ```
    
## <a name="next-steps"></a>后续步骤

- 详细了解如何[针对 Azure Stack 进行开发](azure-stack-dev-start.md)。
- 了解[用作 IaaS 的 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Java 编程语言并查找适用于 Java 的其他资源，请参阅 [Java.com](https://www.java.com)。
