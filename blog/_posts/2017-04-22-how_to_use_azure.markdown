---
layout: post
title:  "azure-cli를 이용하여 azure 사용하기"
date:   2017-04-22 22:19:05 +0900
categories: server
tags: [server, cli]
published: true
count: 125
descript: '대시보드를 사용하지 않고 azure-cli로 azure를 이용하기'
writer: 박정태
---

## azure-cli 설치하기

```base
$ npm install -g azure-cli
```

## azure-cli설치 확인 및 명령어 확인

* 버전확인

```base
$ azure --version # 설치 된 azure버전확인
0.10.12 (node: 6.9.2)
```

* 명령어 확인

```base
$ azure
Microsoft Azure CLI would like to collect data about how users use CLI
commands and some problems they encounter.  Microsoft uses this information
to improve our CLI commands.  Participation is voluntary and when you
choose to participate your device automatically sends information to
Microsoft about how you use Azure CLI.

If you choose to participate, you can stop at any time later by using Azure
CLI as follows:
1.  Use the azure telemetry command to turn the feature Off.
To disable data collection, execute: azure telemetry --disable

If you choose to not participate, you can enable at any time later by using
Azure CLI as follows:
1.  Use the azure telemetry command to turn the feature On.
To enable data collection, execute: azure telemetry --enable

Select y to enable data collection :(y/n) y
```
설치 직후 azure를 실행하면 위와같은 문구가 뜬다 **y**를 입력해준다.

```base
$ azure
info:             _    _____   _ ___ ___
info:            /_\  |_  / | | | _ \ __|
info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
info:       (_______ _ _)         _ ______ _)_ _
info:              (______________ _ )   (___ _ _)
info:
info:    Microsoft Azure: Microsoft's Cloud Platform
info:
info:    Tool version 0.10.12
help:
help:    Display help for a given command
help:      help [options] [command]
help:
help:    Log in to an Azure subscription using Active Directory or a Microsoft account identity.
help:      login [options]
help:
help:    Log out from Azure subscription using Active Directory. Currently, the user can log out only via Microsoft organizational account
help:      logout [options] [username]
help:
help:    Open the portal in a browser
help:      portal [options]
help:
help:    Manages the data collection preference.
help:      telemetry [options]
help:
help:    Commands:
help:      account          Commands to manage your account information and publish settings
help:      acs              Commands to manage your container services.
help:      ad               Commands to display Active Directory objects
help:      appserviceplan   Commands to manage your Azure appserviceplans
help:      availset         Commands to manage your availability sets.
help:      batch            Commands to manage your Batch objects
help:      cdn              Commands to manage Azure Content Delivery Network (CDN)
help:      config           Commands to manage your local settings
help:      datalake         Commands to manage your Data Lake objects
help:      feature          Commands to manage your features
help:      group            Commands to manage your resource groups
help:      hdinsight        Commands to manage HDInsight clusters and jobs
help:      insights         Commands related to monitoring Insights (events, alert rules, autoscale settings, metrics)
help:      iothub           Commands to manage your Azure IoT hubs
help:      keyvault         Commands to manage key vault instances in the Azure Key Vault service
help:      lab              Commands to manage your DevTest Labs
help:      location         Commands to get the available locations
help:      managed-disk     Commands to manage your disks.
help:      managed-image    Commands to manage your images.
help:      managed-snapshot Commands to manage your snapshots.
help:      network          Commands to manage network resources
help:      policy           Commands to manage your policies on ARM Resources.
help:      powerbi          Commands to manage your Azure Power BI Embedded Workspace Collections
help:      provider         Commands to manage resource provider registrations
help:      quotas           Command to view your aggregated Azure quotas
help:      rediscache       Commands to manage your Azure Redis Cache(s)
help:      resource         Commands to manage your resources
help:      role             Commands to manage role definitions
help:      servermanagement Commands to manage Azure Server Managment resources
help:      servicefabric    Commands to manage your Azure Service Fabric
help:      storage          Commands to manage your Storage objects
help:      tag              Commands to manage your resource manager tags
help:      usage            Command to view your aggregated Azure usage data
help:      vm               Commands to manage your virtual machines
help:      vmss             Commands to manage your virtual machine scale sets.
help:      vmssvm           Commands to manage your virtual machine scale set vm.
help:      webapp           Commands to manage your Azure webapps
help:
help:    Options:
help:      -h, --help     output usage information
help:      -v, --version  output the application version
help:
help:    Current Mode: arm (Azure Resource Management)
```

## azure login하기

```bash
$ azure login
info:    Executing command login
-info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code HUBCWPLCS to authenticate.
/
/
\info:    Added subscription Visual Studio Enterprise: BizSpark
info:    Setting subscription "Visual Studio Enterprise: BizSpark" as default
+
info:    login command OK
```

azure login 명령어를 입력하면 https://aka.ms/devicelogin사이트를 접속하여 터미널 창에 떠 있는 코드를 입력하여야 한다. 위의 경우의 코드는 HUBCWPLCS가 된다.

로그인이 성공적으로 됬으면 info : login command OK가 뜬다.

## 리소스 그룹 만들기

azure 리소스 그룹은  azure 리소스가 배포 및 관리되는 논리적 컨테이너 입니다.

```bash
$ azure group create --name {groupName} --location {locationName}
```
location지역에 groupName이라는 이름을 가진 리소스 그룹 생성


```bash
$ azure group create --name myResourceGroup --location westeurope
```
westeurope지역에 myResourceGroup라는 리소스 그룹 생성

```bash
$ azure group list
info:    Executing command group list
+ Listing resource groups
data:    Name             Location    Provisioning State  Tags:
data:    ---------------  ----------  ------------------  -----
data:    myResourceGroup  westeurope  Succeeded           null
info:    group list command OK
```

list를 통해 group을 볼 수 있다.

## 가상 컴퓨터 만들기

그룹 리소스를 만든 후 해당 그룹 리소스 내부에 가상 컴퓨터를 생성할 수 있습니다. vm이라는 파라미터를 이용합니다.

```bash
$ azure vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

myResourceGroup라는 리소스 그룹에 myVM이라는 가상 컴퓨터를 생성 합니다. 이때 UbuntuLTS버전을 설치를 합니다. 가상 컴퓨터를 생성하면 아래와 같은 정보를 띄어줍니다.

```
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

해당 가상 컴퓨터를 ssh로 접속하기를 원할 경우 cli로 하는 것 보다는 대시보드에서 하는게 좀더 편한 것 같다.

ssh로 접속하기 위한 키는 ssh-keygen이라는 툴을 이용해서 만들 수 있습니다.

[ssh-keygen을 이용하여 ssh 키파일 생성하는 방법](https://pjt3591oo.github.io/blog/tools/2017/04/21/how_to_create_ssh_key.html)

## 가상 컴퓨터의 포트 열기

```bash
$ azure vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

myResourceGroup라는 리소스 그룹의 myVM의 이름을 가진 가상 컴퓨터의 80번 포트를 열어줍니다.

## 가상 컴퓨터 삭제

리소스 그룹, vm에 관련된 모든 리소스를 제거할 수 있습니다.

* `리소스 그룹`삭제

```bash
$ azure group delete --name MyResourceGroup
```

* `가상 컴퓨터`삭제

```bash
$ azure vm delete --name myVM
```