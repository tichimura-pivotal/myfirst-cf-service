# はじめてのCloud Foundry
--- アプリケーションとサービス編 ---

* はじめに  
  - PCFDevの設定
    - [Virtualbox]( https://www.virtualbox.org) (5.0以上を推奨)
    - 3GBの空きメモリ
    - 20GBの空きディスク領域
  - PWS アカウント
    - [Pivotal Web Services](https://run.pivotal.io)
  - cf cli
    - [Windows 64bit版](https://cli.run.pivotal.io/stable?release=windows64&source=github-rel)
    - [Mac OS 64版](https://cli.run.pivotal.io/stable?release=macosx64&source=github)
  - git cli
    - Mac OS Xの場合 Xcodeが必要となりますのでご注意下さい

* 目次
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [1. サービスの事始め](#1-%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%81%AE%E4%BA%8B%E5%A7%8B%E3%82%81)
- [2. サービスの利用](#2-%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%81%AE%E5%88%A9%E7%94%A8)
- [3. サービスアクセス](#3-%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9)
- [4. サービスの種類　　](#4-%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%81%AE%E7%A8%AE%E9%A1%9E)
  - [User Provided Service](#user-provided-service)
  - [Brokered Service](#brokered-service)
  - [Managed Service](#managed-service)
  - [On-Demand Service](#on-demand-service)
  - [Route Service](#route-service)
  - [Volume Service](#volume-service)
- [5. サービスブローカーを試してみる](#5-%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%83%96%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%BC%E3%82%92%E8%A9%A6%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B)
- [5. SDKを利用したサービスブローカーの作成](#5-sdk%E3%82%92%E5%88%A9%E7%94%A8%E3%81%97%E3%81%9F%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%83%96%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%BC%E3%81%AE%E4%BD%9C%E6%88%90)
- [6. クラウドサービスの利用 (PCF)](#6-%E3%82%AF%E3%83%A9%E3%82%A6%E3%83%89%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%81%AE%E5%88%A9%E7%94%A8-pcf)
- [6. サービスタイルの管理 (PCF)](#6-%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB%E3%81%AE%E7%AE%A1%E7%90%86-pcf)
- [- アプリとサービス](#--%E3%82%A2%E3%83%97%E3%83%AA%E3%81%A8%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9)
- [- Appendix](#--appendix)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 1. サービスの事始め

1. PWS ( or PCF Dev )にログイン

  > PWS: https://run.pivotal.io

  > PCF Dev: https://console.local.pcfdev.io

2. Marketplaceをクリック
  > サービス一覧を確認
  > (cf marketplaceでも可)

3. サービス内容の確認
  > サービスプランをクリック。"free"とあれば無償利用、"$10/Month"などとあれば、利用料が必要

4. サービスの選択
  > "Select this plan"を押下

5. サービスの作成
  > Instance Nameとしてサービス名を指定 (のちに"cf services"にて確認)   
  > 適用するSpaceを指定   
  > アプリケーションの指定（Option)

## 2. サービスの利用

1. アプリケーションのPush
  > cf pushで任意のアプリケーションをアップロード

  > サンプルが必要ならこちら  
  >  https://github.com/cloudfoundry-samples/cf-sample-app-spring

  > git clone https://github.com/cloudfoundry-samples/cf-sample-app-spring  
  > cd cf-sample-app-spring   
  > cf push APP_NAME (APP_NAMEは任意)

2. アプリケーションへのバインド

  2-1. Apps Managerを使った場合

  - "Apps"タブより作成したアプリケーションを選択

  - "Services"タブにて,"Add a service"を選択

  - Marketplaceの画面に戻るので、サービスを指定

  - Instance Nameから、サービスインスタンスを指定

  2-2. cf cliを使った場合
  - サービスをバインド
  > cf bind-service APP_NAME SERVICE_INSTANCE

3. 割り当てられたサービスの確認

  3-1. cf env APP_NAME

  ```
  $ cf env simple-go-web-ichi
  Getting env variables for app simple-go-web-ichi in org Demo / space demo as admin...
  OK

  System-Provided:
  {
   "VCAP_SERVICES": {
    "p-mysql": [
     {
      "credentials": {
       "hostname": "10.aaa.bbb.ccc",
       "jdbcUrl": "jdbc:mysql://10.aaa.bbb.ccc:3306/cf_ec7c6b37_271f_49b8_b2f2_fb0ee7f35702?user=username\u0026password=password",
       "name": "cf_ec7c6b37_271f_49b8_b2f2_fb0ee7f35702",
       "password": "password",
       "port": 3306,
       "uri": "mysql://n4uWhR4SaCqceIy1:Hs7PzOrb7kS6PQC4@10.193.133.92:3306/cf_ec7c6b37_271f_49b8_b2f2_fb0ee7f35702?reconnect=true",
       "username": "username"
      },
      "label": "p-mysql",
      "name": "mysql100mb",
      "plan": "100mb",
      "provider": null,
      "syslog_drain_url": null,
      "tags": [
       "mysql",
       "relational"
      ],
      "volume_mounts": []
     }
    ]
   }
  }
  ```

  3-2. cf services SERVICE_INSTANCE
  > サービスインスタンスのステータス

  ```

  $ cf service mysql100mb

  Service instance: mysql100mb
  Service: p-mysql
  Bound apps: simple-go-web-ichi
  Tags:
  Plan: 100mb
  Description: MySQL databases on demand
  Documentation url: http://docs.pivotal.io/p-mysql
  Dashboard: https://p-mysql.run.pivotaro.io/manage/instances/ec7c6b37-271f-49b8-b2f2-fb0ee7f35702

  Last Operation
  Status: update succeeded
  Message:
  Started: 2017-02-20T22:35:07Z
  Updated: 2017-02-20T22:35:07Z

  ```

## 3. サービスアクセス

サービスプランをOrgに適用する範囲を限定することも可能( limited: 特定のOrgのみ公開 )

```
$ cf service-access
getting service access as admin...
broker: p-riakcs
   service    plan        access    orgs
   p-riakcs   developer   limited   Demo

broker: p-mysql
   service   plan        access   orgs
   p-mysql   100mb-dev   all
＄ cf enable-service-access p-riakcs
＄ cf disable-service-access p-riakcs   
 #️## 既存に同じService IDなどがあれば、一旦、実績した既存のサービスを活用
```
## 4. サービスの種類　　

![image](odb.png)

* User Provided Service   
  -  Service Brokerを利用しないサービス
  [サンプル](#cups)

* Brokered Service  
  - CFとは別の環境で管理されるサービスの利用
  [サンプル](#brokered)

* Managed Service   
  - PCFと同じ環境で管理されるサービスの利用 (BOSHによるプロビジョニングを想定)
  [サンプル](#managed)  

* On-Demand Service  
  - リクエストの時点で、サービス提供のための仮想マシンを随時作成  
  [サンプル](#ondemand)

  ---
* Route Service   
  - 外部サービスへのルーティングを提供するサービス(API管理など)の利用  
  [サンプル](#route)

* Volume Service   
  - アプリケーションへのボリュームサービスの提供(PCFDevにて実装済み)  
  [サンプル](#volume)

  <a name="cups"></a>
## 5. カスタムサービス(User Provided Service)

1. cf cliの確認   

  ```
  $ cf help create-user-provided-service
  NAME:
     create-user-provided-service - Make a user-provided service instance available to CF apps

  USAGE:
     cf create-user-provided-service SERVICE_INSTANCE [-p CREDENTIALS] [-l SYSLOG_DRAIN_URL] [-r ROUTE_SERVICE_URL]

     Pass comma separated credential parameter names to enable interactive mode:
     cf create-user-provided-service SERVICE_INSTANCE -p "comma, separated, parameter, names"

     Pass credential parameters as JSON to create a service non-interactively:
     cf create-user-provided-service SERVICE_INSTANCE -p '{"key1":"value1","key2":"value2"}'

     Specify a path to a file containing JSON:
     cf create-user-provided-service SERVICE_INSTANCE -p PATH_TO_FILE

  EXAMPLES:
     cf create-user-provided-service my-db-mine -p "username, password"
     cf create-user-provided-service my-db-mine -p /path/to/credentials.json
     cf create-user-provided-service my-drain-service -l syslog://example.com
     cf create-user-provided-service my-route-service -r https://example.com

     Linux/Mac:
        cf create-user-provided-service my-db-mine -p '{"username":"admin","password":"pa55woRD"}'

     Windows Command Line:
        cf create-user-provided-service my-db-mine -p "{\"username\":\"admin\",\"password\":\"pa55woRD\"}"

     Windows PowerShell:
        cf create-user-provided-service my-db-mine -p '{\"username\":\"admin\",\"password\":\"pa55woRD\"}'

  ALIAS:
     cups

  OPTIONS:
     -l      URL to which logs for bound applications will be streamed
     -p      Credentials, provided inline or in a file, to be exposed in the VCAP_SERVICES environment variable for bound applications
     -r      URL to which requests for bound routes will be forwarded. Scheme for this URL must be https

  SEE ALSO:
     bind-service, services  
  ```

2. カスタムサービスの作成  

  > cf create-user-provided-service ichimura-db -p "username, ichimura"

  > cf create-user-provided-service ichimura-db -p '{"key1":"value1","key2":"value2"}'

  > cf create-user-provided-service ichimura-db -p /path/to/file.json

  ```
  $ cat file.json
  {"host":"www.sample.com","port":"50001","dbname":"sample","username":"test","password":"test"}
  ```

3. カスタムログ出力
(pcfdevの場合)
  > cf uups logdrain -l syslog://192.168.11.1:1234   

  アプリケーションへのバインド
  > cf bs phpdemo logdrain

  アプリケーションへのアクセス
   > curl -l phpdemo.local.pcfdev.io

  端末で確認
  >  nc -l 1234

4. ルートサービスの作成   
  > *あらかじめcf pushされている前提とします。*

  >  cf cups my-route-service -r https://phpdemo.local.pcfdev.io

  > cf bind-route-service shared-domain.example.com my-route-service --hostname my-app

  > $ cf bind-route-service local.pcfdev.io my-route-service --hostname staticdemo

  ```
  Binding route staticdemo.local.pcfdev.io to service instance my-route-service in org pcfdev-org / space pcfdev-space as admin...
  OK
  ```

## 6. サービスブローカーを試してみる

Service Brokerは、Service Broker APIを実装し、エンドポイントを提供するコンポーネントの名称.  
(それ自体が仮想マシンだったり、アプリケーションだったりする)

![broker](http://docs.pivotal.io/pivotalcf/1-9/services/images/managed-services.png)   

Service Broker APIについて、詳しくはこちら  
http://docs.pivotal.io/pivotalcf/1-9/services/api.html

1. 必要となるAPI
  - カタログ取得:   
  GET /v2/catalog
  - サービスインスタンスの作成(プロビジョニング):   
  PUT /v2/service_instances/:id
  - サービスのバインド:   
  PUT /v2/service_instances/:instance_id/service_bindings/:id
  - バインドの解除:  
  DELETE  /v2/service_instances/:instance_id/service_bindings/:id
  - サービスインスタンスの削除(アン・プロビジョニング):   
  DELETE /v2/service_instances/:id

2. サンプル   
http://docs.pivotal.io/pivotalcf/1-9/services/examples.html

  <a name="brokered"></a>
  2-1. Ruby   
  - [GitHub repo service (APP) ](https://github.com/cloudfoundry-samples/github-service-broker-ruby) :    
  GitHubを利用したService Brokerアプリケーション・サンプル   
  *要: GitHubアカウント*

  <a name="managed"></a>
  - [MySQL database service (VM) ](https://github.com/cloudfoundry/cf-mysql-release):  
  BOSHを利用してMySQL VMを作成(Database nodes, Proxy nodes, Broker nodes).   
  その後、Service BrokerとしてBroker nodes VMを登録(create-service-broker)する  
  *要: BOSHへのアクセス*

  2-2. Java
  - [Sample Spring Boot project (APP) ](https://github.com/spring-cloud-samples/cloudfoundry-service-broker)  
  *要: MongoDB*   
  Spring Bootを使ったService Brokerアプリケーションの作成   
  (以前までSpring Boot CF Service Brokerと呼ばれていたもの. 現在ではSpring Cloud - Cloud Foundry Service Brokerと呼んでいる)  
  詳細はこちら
  [Spring Cloud - Cloud Foundry Service Broker](https://github.com/spring-cloud/spring-cloud-cloudfoundry-service-broker)

  - [MySQL Java Broker (VM) ](https://github.com/cloudfoundry-community/cf-mysql-java-broker)   
  *要: BOSHへのアクセス*   
  Java版 MySQL Broker

  2-3. Go

  - [Asynchronous Service Broker for AWS EC2](https://github.com/cloudfoundry-samples/go_service_broker)   
  *要: AWSアカウント*   
  Amazon Web ServicesのAPIを呼んで、EC2のVMをプロビジョニングするService Brokerアプリケーション

3. Brokerの登録方法

  - 通常のブローカー: Standard Broker
  ```
  $ cf create-service-broker mybrokername someuser somethingsecure https://mybroker.example.com/
  ```
  - スペースに特化したブローカー: Space-Scoped Brokers
  ```
  $ cf create-service-broker mybrokername someuser somethingsecure https://mybroker.example.com/ --space-scope
  ```   
    - Space Developerとして作成した場合は、--space-scopedをOptionとしてつける必要がある

4. Service Brokerの作成における注意点   
  - 同じ名前のサービスブローカーを複数作成出来ない
  - 同じURLのサービスブローカーを複数作成出来ない
  - Service IDやPlan IDはCloud Foundry全体でユニークでなければならない  

## 7. SDKを利用したサービスブローカーの作成

<a name="ondemand"></a>
オンデマンドサービスブローカーのためのSDKも提供されております   
*要: PCF環境(PCF Devも可)*   

https://docs.pivotal.io/on-demand-service-broker

- cf create service-broker <broker name> <username> <password> <broker base URI>
- cf enable-service-access SERVICE [-p PLAN] [-o ORG]

> PWSでは下記の制限があります

> https://discuss.pivotal.io/hc/en-us/articles/230432768-When-running-a-command-the-CLI-returns-an-error-code-10003-or-error-code-403-

## 8. クラウドサービスの利用 (PCF)
AWS, Azure, GCPなどのIaaS提供サービスを、Service Brokerを利用して適用

![optional caption text](azure-pcfopsmgr.png)

![optional caption text](azure-pcfmktplace.png)


## 9. サービスタイルの管理 (PCF)
PCFユーザは、サービス・タイルの作成とサービスの管理も可能

![Tile Image](https://docs.pivotal.io/tiledev/img/your-service-here.png)

Tile Generator(PCFユーザ向け)   
https://docs.pivotal.io/tiledev/index.html

<a name="volume"><a/>
## 10. Volume Services

https://docs.cloudfoundry.org/adminguide/deploy-vol-services.html

```
properties: cc: volume_services_enabled
```

PCFDevでのVolume

```
$ cf m -s local-volume
Getting service plan information for service local-volume as admin...
OK

service plan      description                           free or paid
free-local-disk   free local filesystem for bosh-lite   free
```

サービス作成   
```
> cf create-service local-volume free-local-disk lv
Creating service instance lv in org pcfdev-org / space pcfdev-space as admin...
OK
```

サービスのバインド   
```
> cf bs phpdemo lv
Binding service lv to app phpdemo in org pcfdev-org / space pcfdev-space as admin...
OK
```

実際に確認(cf ssh phpdemo)
```
$ mount -l
none on / type aufs (rw,relatime,si=1a6a55df5df2c511,dio,dirperm1)
sysfs on /sys type sysfs (ro,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,relatime)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,mode=620,ptmxmode=666)
/dev/sda1 on /tmp/garden-init type ext4 (rw,relatime,errors=remount-ro,data=ordered)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
/dev/sda1 on /tmp/lifecycle type ext4 (ro,relatime,errors=remount-ro,data=ordered)
/dev/sda1 on /var/vcap/data/fe5d95d4-2b2b-4a0b-8d45-9d8655ab1b1d type ext4 (rw,relatime,errors=remount-ro,data=ordered)
```   

API
```
curl -k -H "X-Broker-API-Version: 2.11" -L https://admin:admin@localbroker.local.pcfdev.io/v2/catalog
```


## 11. Apigee

## 確認しましょう

- サービスブローカー
- 覚えておくべきAPI
- サービスの作成
- 新しく作る？あるものを使う？
- サービスの作成(プロビジョニング)と、サービスの割り当て(アクティベーション)
- サービスへのアクセス ( Org:組織 )
- マーケットプレイス
- サービスの開発
- ルートサービス
- Volume Service
- オンデマンドサービス
- SDK
- マイクロサービス
- Spring Cloud

## - Appendix
http://www.slideshare.net/Pivotal/custom-tile-generation-in-pcf
