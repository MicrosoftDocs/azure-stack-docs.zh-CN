---
title: 将 Java WAR 部署到 Azure Stack 中的虚拟机 |Microsoft Docs
description: 将 Java WAR 部署到 Azure Stack 中的虚拟机。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: dbf6083ff81d045d92d488eda5cfab757093bb7e
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782891"
---
# <a name="how-to-deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>如何将 Java web 应用部署到 Azure Stack 中的 VM

可以创建用于托管在 Azure Stack 中的将 Python Web 应用的虚拟机。 本文探讨您需要在设置服务器、 配置服务器来托管 Python web 应用，然后部署应用程序中遵循的步骤。

本文将引导完成安装和配置 Azure Stack 中的 Linux VM 上的 Apache Tomcat 服务器以及然后将 Java Web 应用程序资源 (WAR) 文件加载到服务器。 WAR 文件用于将分发的 JAR 文件中，集合包含 Java 资源，例如类、 文本、 图像、 XML 和 HTML，并使用提供的 web 应用程序的其他资源的压缩文件。

## <a name="create-a-vm"></a>创建 VM

1. 设置 Azure Stack 中设置 VM。 按照中的步骤[部署 Linux VM 托管在 Azure Stack 中的 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 VM 网络的边栏选项卡，确保以下端口可访问：

    | Port | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器提供网页的协议。 客户端通过 HTTP 进行连接的 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议安全 (HTTPS) 是 HTTP 的一个安全的需要的安全证书，并允许加密传输信息版本。  |
    | 22 | SSH | 安全外壳 (SSH) 是安全通信的加密的网络协议。 要将此连接用于 SSH 客户端将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许使用图形用户界面的远程桌面连接你的计算机。   |
    | 8080 | 自定义 | Apache Tomcat 服务的默认端口为 8080。 对于生产服务器，要在通过路由流量 80 和 443。 |

## <a name="install-java"></a>安装 Java

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTy SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty)。
2. 在 VM 上在 bash 提示符处，键入以下命令：

    ```bash  
        sudo apt-get install default-jdk
    ```

3. 验证你的安装。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>安装和配置 Tomcat

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTy SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty)。

2. 创建 Tomcat 的用户。
    - 首先，创建新的 Tomcat 组：
        ```bash  
            sudo groupadd tomcat
        ```
     
    - 其次，创建一个 Tomcat 用户。 将此用户添加到的主目录的 tomcat 组`/opt/tomcat`。 要将 Tomcat 部署到此目录：
        ```bash  
            sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
        ```

3. 安装 Tomcat。
    - 首先，从 Tomcat 8 下载页面，获取最新版本的 Tomcat 8 tar 的 URL:  [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)
    - 第二个，用户 curl 下载最新版本使用的链接。

        ```bash  
            cd /tmp 
            curl -O <URL for the tar for the latest version of Tomcat 8>
        ```

    - 第三，安装到 Tomcat`/opt/tomcat`目录。 创建文件夹。  打开存档：

        ```bash  
            sudo mkdir /opt/tomcat
            sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
            sudo chown -R tomcat webapps/ work/ temp/ logs/
        ```

4. 更新的 Tomcat 的权限。

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

5. 创建`systemd`服务文件。 以便可以作为一项服务来运行 Tomcat。

    - 需要知道 Java 安装 tomcat。 此路径通常称为**JAVA_HOME**。 通过运行中找到的位置：

        ```bash  
            sudo update-java-alternatives -l
        ```

        这将生成类似于以下内容：

        ```Text  
            Output
            java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
        ```

        **JAVA_HOME**变量值可以通过从输出的路径并添加构造`/jre`。 例如，使用上面的示例： `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`

    - 使用你的服务器中的值创建 systemd 服务文件。

        ```bash  
            sudo nano /etc/systemd/system/tomcat.service
        ```

    - 将以下内容粘贴到你的服务文件。 修改的值**JAVA_HOME**如果所需的值匹配您找到您的系统上。 您可能想要修改 CATALINA_OPTS 中指定的内存分配设置：

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

    - 保存并关闭该文件。
    - 重新加载 systemd 守护程序，以使它才知道你的服务文件：

        ```bash  
            sudo systemctl daemon-reload
        ```

    - 启动 Tomcat 服务 

        ```bash  
            sudo systemctl start tomcat
        ```

    - 验证它已启动且未出错通过键入：

        ```bash  
            ssudo systemctl status tomcat
        ```

6. 确认 Tomcat 服务器。 Tomcat 使用端口 8080 接受传统的请求。 通过运行以下命令来允许流量流向该端口：

    ```bash  
        sudo ufw allow 8080
    ```

    如果尚未添加**入站端口规则**为 Azure Stack VM，然后添加那些现在。 有关详细信息，请参阅[创建 VM](#create-a-vm)。

7. 在 Azure Stack 与位于同一网络中打开浏览器并打开你的服务器： `yourmachine.local.cloudapp.azurestack.external:8080`

    ![在 Azure Stack VM 上的 Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    在服务器上的 Apache Tomcat 页加载。 接下来，您将配置服务器以允许你访问服务器状态、 管理器应用程序和主机管理器。

8. 启用服务文件，以便该 Tomcat 自动启动时重新启动服务器：

    ```bash  
        sudo systemctl enable tomcat
    ```

9. Tomcat 服务器配置为允许您访问的 web 管理界面。 编辑`tomcat-users.xml`和定义的角色和用户，以便你可以登录。 定义用户访问`manager-gui`和`admin-gui`。

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

    - 添加以下元素到`<tomcat-users>`部分：

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    - 例如，您最终文件可能如下所示：

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
         <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    - 保存并关闭该文件。


10. Tomcat 会限制对访问**管理器**并**宿主管理器**应用到来自服务器的连接。 由于你要在 Azure Stack 中的 VM 上安装 Tomcat，因此，想要解除此限制。 通过编辑相应更改这些应用的 IP 地址限制`context.xml`文件。

    - 更新`context.xml`管理器应用：

        ```bash  
            sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
        ```

    - 注释掉 IP 地址限制以允许连接从任意位置，或添加用来连接到 Tomcat 在计算机的 IP 地址。

        ```XML  
        <Context antiResourceLocking="false" privileged="true" >
          <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                 allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
        </Context>
        ```

    - 保存并关闭该文件。

    - 更新`context.xml`宿主管理器应用程序与类似的更新：

        ```bash  
            sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
        ```

    - 保存并关闭该文件。

11. 重新启动 Tomcat 服务以使用所做的更改更新服务器：

    ```bash  
        sudo systemctl restart tomcat
    ```

12. 在 Azure Stack 与位于同一网络中打开浏览器并打开你的服务器： `yourmachine.local.cloudapp.azurestack.external:8080`。

    - 选择要查看 Tomcat 服务器的状态，并验证您具有访问权限的服务器状态。
    - 使用 Tomcat 凭据登录。

![在 Azure Stack VM 上的 Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>创建应用

你将需要创建将部署到 Tomcat WAR。 如果你只想要检查你的环境，可以找到一个示例 Apache TomCat 站点上的 War:[示例应用](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/)。

有关开发 Java 应用程序在 Azure 中的指南，请参阅[生成和部署 Azure 上的 Java 应用](https://azure.microsoft.com/develop/java/)。

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTy SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty)。
1. 停止，Tomcat 服务以使用你的应用程序包更新服务器：

    ```bash  
        sudo systemctl stop tomcat
    ```

2.  将 FTP 用户添加到 tomcat 组，以便您可以编写到 webapps 文件夹。 FTP 用户是用户在 Azure Stack 中创建 VM 时定义。

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

3. 连接到使用 FileZilla 清除 webapps 文件夹，然后加载新的或更新 WAR VM。 有关使用 FileZila 的说明，请参阅[使用 FileZilla 与 SFTP 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla)。
    - 清除`TOMCAT_HOME/webapps`。
    - 添加到 WAR ` TOMCAT_HOME/webapps`，例如`/opt/tomcat/webapps/`。

4.  Tomcat 自动扩展和部署应用程序。 您可以查看它使用先前创建的 DNS 名称。 例如:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [Develop for Azure Stack](azure-stack-dev-start.md)
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).
- To learn the Java programming language and find additional resources for Java, see [Java.com](https://www.java.com).