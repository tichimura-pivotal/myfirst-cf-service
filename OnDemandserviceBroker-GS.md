# はじめてのCloud Foundry
--- オンデマンド・サービス編 ---

## オンデマンドサービスとは？

ユーザがサービスインスタンスを要求時(cf create-service)に、オンデマンドでサービスインスタンスを作成する機能。従来は、リソースの共有を前提として、サービスの払い出しも同期での処理だった。  
オンデマンドサービスにより、アプリケーションへのサービスインスタンスのリソース確保がしやすくなり、リクエスト処理も非同期となり、よりユーザ主導での操作が可能となった。

![ODB](http://docs.pivotal.io/svc-sdk/odb/img/ODB-create-service.png)


## オンデマンドサービスのローカル環境でのサンプル

原文はこちら  
``
https://docs.pivotal.io/svc-sdk/odb/0-17/getting-started.html
``

### PCF Dev install  

- Pivotal Networkに移動
``
http://network.pivotal.io  
``

- PCF Dev 0.28.0を選択 ( 2017/9/20現在 )

- ダウンロードしたファイルを解凍して、実行  

> 以下はMacOSの場合  

```
$ ./pcfdev-v0.28.0+PCF1.11.0-osx
Plugin successfully upgraded. Current version: 0.28.0. For more info run: cf dev help
```

### bosh cliのインストール

環境に応じて、bosh cliをインストール  
``
https://bosh.io/docs/cli-v2.html#install
``

### bosh-liteのセットアップ

- gitから必要なソースをclone  

  ``
  git clone https://github.com/cloudfoundry/bosh-deployment ~/bosh-deployment-odb
  ``

- bosh liteの立ち上げ
> 以下の場合、192.168.50.6で立ち上がる

```
bosh create-env ~/bosh-deployment-odb/bosh.yml \
  --state ./state.json \
  -o ~/bosh-deployment-odb/virtualbox/cpi.yml \
  -o ~/bosh-deployment-odb/virtualbox/outbound-network.yml \
  -o ~/bosh-deployment-odb/bosh-lite.yml \
  -o ~/bosh-deployment-odb/bosh-lite-runc.yml \
  -o ~/bosh-deployment-odb/jumpbox-user.yml \
  --vars-store ./creds.yml \
  -v director_name="Bosh Lite Director" \
  -v internal_ip=192.168.50.6 \
  -v internal_gw=192.168.50.1 \
  -v internal_cidr=192.168.50.0/24 \
  -v outbound_network_name=NatNetwork
```

実際のログ

```
$ bosh create-env ~/bosh-deployment-odb/bosh.yml
   --state ./state.json \
   -o ~/bosh-deployment-odb/virtualbox/cpi.yml \
   -o ~/bosh-deployment-odb/virtualbox/outbound-network.yml \
   -o ~/bosh-deployment-odb/bosh-lite.yml \
   -o ~/bosh-deployment-odb/bosh-lite-runc.yml \
   -o ~/bosh-deployment-odb/jumpbox-user.yml \
   --vars-store ./creds.yml \
   -v director_name="Bosh Lite Director" \
   -v internal_ip=192.168.50.6 \
   -v internal_gw=192.168.50.1 \
   -v internal_cidr=192.168.50.0/24 \
   -v outbound_network_name=NatNetwork
Deployment manifest: '/Users/ichimt1/bosh-deployment-odb/bosh.yml'
Deployment state: './state.json'

Started validating
  Downloading release 'bosh'... Finished (00:00:45)
  Validating release 'bosh'... Finished (00:00:00)
  Downloading release 'bosh-virtualbox-cpi'... Finished (00:00:16)
  Validating release 'bosh-virtualbox-cpi'... Finished (00:00:00)
  Downloading release 'bosh-warden-cpi'... Finished (00:00:19)
  Validating release 'bosh-warden-cpi'... Finished (00:00:00)
  Downloading release 'os-conf'... Finished (00:00:00)
  Validating release 'os-conf'... Finished (00:00:00)
  Downloading release 'garden-runc'... Finished (00:00:22)
  Validating release 'garden-runc'... Finished (00:00:00)
  Validating cpi release... Finished (00:00:00)
  Validating deployment manifest... Finished (00:00:00)
  Downloading stemcell... Finished (00:01:22)
  Validating stemcell... Finished (00:00:00)
Finished validating (00:03:10)

Started installing CPI
  Compiling package 'golang_1.7/21609f611781e8586e713cfd7ceb389cee429c5a'... Finished (00:00:17)
  Compiling package 'virtualbox_cpi/e293cbbb8359fd2cbbb9777b7b91fd142ab6c688'... Finished (00:00:13)
  Installing packages... Finished (00:00:02)
  Rendering job templates... Finished (00:00:00)
  Installing job 'virtualbox_cpi'... Finished (00:00:00)
Finished installing CPI (00:00:33)

Starting registry... Finished (00:00:00)
Uploading stemcell 'bosh-vsphere-esxi-ubuntu-trusty-go_agent/3445.7'... Finished (00:00:11)

Started deploying
  Creating VM for instance 'bosh/0' from stemcell 'sc-96b048ee-deef-4ce2-7173-792f8dc4f81a'... Finished (00:00:01)
  Waiting for the agent on VM 'vm-8e6f15a6-9e2d-49f2-5d3e-01c1acb8dc00' to be ready... Finished (00:00:31)
  Creating disk... Finished (00:00:00)
  Attaching disk 'disk-9a85ca73-b857-4904-5f6c-5000ea217bfa' to VM 'vm-8e6f15a6-9e2d-49f2-5d3e-01c1acb8dc00'... Finished (00:00:04)
  Rendering job templates... Finished (00:00:04)
  Compiling package 'golang/6b1bfba0bc0455c57678161490ce4fa4fd5f70f4'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'golang_1.7/c82ff355bb4bd412a4397dba778682293cd4f392'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'apparmor/d2a9bb24b85a144e874e7627a5fefb4e9b8b30f3'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'libseccomp/9909b2f6b2390c5117515c082ce4b2d586cdfb91'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'ruby/29bd4c7578b245737446c04add67568c116a5c63'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'mysql/b7e73acc0bfe05f1c6cbfd97bf92d39b0d3155d5'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'libpq/826813f983d38b4b4a95bb8a3df1a2d0efab14b0'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'golang_1.7/21609f611781e8586e713cfd7ceb389cee429c5a'... Finished (00:00:26)
  Compiling package 'guardian/afbc6756a4bc2b113d9ba828e7a989c6c3fb1b3c'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'warden_cpi/85ec34ac14547f685ff6609207365c8b34200b84'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'davcli/5f08f8d5ab3addd0e11171f739f072b107b30b8c'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'busybox/eeb02f3df20cef2d7609e33fb4102ce884969acc'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'tar/1c85c20331b39d35be32116a20b1fa2e28725cbe'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'runc/6ce5d747ecc0c821cf79d3c13548bb30fa33caf0'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'iptables/70cd40ad87371de73d6bdfc34967e94422fc2cc4'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'verify_multidigest/8fc5d654cebad7725c34bb08b3f60b912db7094a'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'postgres/3b1089109c074984577a0bac1b38018d7a2890ef'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'postgres-9.4/1da82648840de67015d379264846a447118261a7'... Skipped [Package already compiled] (00:00:00)
  Compiling package 's3cli/bb1c1976d221fdadf13a6bc873896cd5e2433580'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'nats/63ae42eb73527625307ff522fb402832b407321d'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'nginx/57ca1d048957399c500e0f5fd3275ed4c6d4f762'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'bosh-gcscli/83d331c7b6d04de64cd5257a47e1e92021cb4c8a'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'director/bcbcd57fd903a7fd865545866f12e9352c2fb32f'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'garden-idmapper/68d9b8a73a5b05c5907ded18bdd4ab74f7d1f303'... Skipped [Package already compiled] (00:00:00)
  Compiling package 'virtualbox_cpi/e293cbbb8359fd2cbbb9777b7b91fd142ab6c688'... Finished (00:00:20)
  Compiling package 'health_monitor/aa43dacd332bda1131b141aada0ca45b4302273c'... Skipped [Package already compiled] (00:00:00)
  Updating instance 'bosh/0'... Finished (00:00:25)
  Waiting for instance 'bosh/0' to be running... Finished (00:00:10)
  Running the post-start scripts 'bosh/0'... Finished (00:00:01)
Finished deploying (00:02:13)

Stopping registry... Finished (00:00:00)
Cleaning up rendered CPI jobs... Finished (00:00:00)

Succeeded
```

### Bosh aliasの作成

```
$ bosh alias-env vbox -e 192.168.50.6 --ca-cert <(bosh int ./creds.yml --path /director_ssl/ca)
Using environment '192.168.50.6' as anonymous user

Name      Bosh Lite Director                    
UUID      d8beb213-502a-4f6f-a6e7-263f64edf2ff  
Version   263.2.0 (00000000)                    
CPI       warden_cpi                            
Features  compiled_package_cache: disabled      
          config_server: disabled               
          dns: disabled                         
          snapshots: disabled                   
User      (not logged in)                       

Succeeded

```

### Bosh liteへの接続確認

```
$ export BOSH_CLIENT=admin
$ export BOSH_CLIENT_SECRET=`bosh int ./creds.yml --path /admin_password`
$ bosh -e vbox env
Using environment '192.168.50.6' as client 'admin'

Name      Bosh Lite Director                    
UUID      d8beb213-502a-4f6f-a6e7-263f64edf2ff  
Version   263.2.0 (00000000)                    
CPI       warden_cpi                            
Features  compiled_package_cache: disabled      
         config_server: disabled               
         dns: disabled                         
         snapshots: disabled                   
User      admin                                 

Succeeded

```
### bosh ssh用にルートを確保
> あるいは、bin/add-routeをbosh-liteのrepositoryから取得

```
$ sudo route add -net 10.244.0.0/16 192.168.50.6
Password:
add net 10.244.0.0: gateway 192.168.50.6
```

### stemcellをアップロード

```
$ bosh -e vbox upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3262.2
Using environment '192.168.50.6' as client 'admin'

Task 1

Task 1 | 22:41:56 | Update stemcell: Downloading remote stemcell (00:01:55)
Task 1 | 22:43:51 | Update stemcell: Extracting stemcell archive (00:00:03)
Task 1 | 22:43:54 | Update stemcell: Verifying stemcell manifest (00:00:00)
Task 1 | 22:43:55 | Update stemcell: Checking if this stemcell already exists (00:00:00)
Task 1 | 22:43:55 | Update stemcell: Uploading stemcell bosh-warden-boshlite-ubuntu-trusty-go_agent/3262.2 to the cloud (00:00:10)
Task 1 | 22:44:07 | Update stemcell: Save stemcell bosh-warden-boshlite-ubuntu-trusty-go_agent/3262.2 (e7a2442d-b36d-4a35-4a04-0a8b13ad18a3) (00:00:01)

Task 1 Started  Wed Sep 20 22:41:56 UTC 2017
Task 1 Finished Wed Sep 20 22:44:08 UTC 2017
Task 1 Duration 00:02:12
Task 1 done

Succeeded

```

### create release

releaseの作成

```
$ bosh -e vbox create-release --name=kafka-example-service
Added dev release 'kafka-example-service/0+dev.2'

Name         kafka-example-service  
Version      0+dev.2                
Commit Hash  6c7003f                

Job                                                        Digest                                    Packages       
admin_tools/633e34fe1c14b4d107bc083cbccb09b9d5409a54       56fe3ef61048cfb4f1c2c0fd953a14bcca273a37  kafka          
                                                                                                     jre            
                                                                                                     topic_manager  
kafka_server/306f5a32325fb0528c4879c341c75bbc7c1e79e8      7f22e127d05b08dfdd6472b23b21d7177ac3433d  common         
                                                                                                     kafka          
                                                                                                     jre            
smoke_tests/51a9cec047d9278aa81aec0e4348540964fe1e1e       b441b83ca285da823c85a1cc8c6b798f7ad74159  common         
                                                                                                     kafka          
                                                                                                     jre            
zookeeper_server/3b3d8585414d328eb4827ba8df11dc46e0a71460  7e9869522ec4105c036b13ee45b2ecec5a91ea40  common         
                                                                                                     kafka          
                                                                                                     jre            

4 jobs

Package                                                 Digest                                    Dependencies  
common/721e3ac91fd304555f85700d3334be625a59f7c5         ea9a0ae3ee5fc39bfd0ec8c9c51b647d2190bec8  -             
jre/7a3630bcb5828e709af1458e3820e9195896147c            8d57c2be2c95f2af2317f2009a77e72e33231b6f  -             
kafka/19b391f16f78880c9ccef4f6048626c2382c9575          bc255acceafddd99fc7db74801aa170e8c4321ce  -             
topic_manager/70fda9430ec0166d3f2ad8be3639bd9b38e165ed  d7bf4f334a1b534f56f2a9416022dabe937ba697  -             

4 packages

Succeeded
```


### Bosh liteへreleaseをアップロード

bosh loginしている状態で実行
> bosh -e vbox envでadminでログインしているか確認  

```
$ bosh -e vbox upload-release
Using environment '192.168.50.6' as client 'admin'

######################################################### 100.00% 190.30 MB/s 0s
Task 2

Task 2 | 22:55:15 | Extracting release: Extracting release (00:00:00)
Task 2 | 22:55:15 | Verifying manifest: Verifying manifest (00:00:00)
Task 2 | 22:55:15 | Resolving package dependencies: Resolving package dependencies (00:00:00)
Task 2 | 22:55:15 | Creating new packages: common/721e3ac91fd304555f85700d3334be625a59f7c5 (00:00:00)
Task 2 | 22:55:15 | Creating new packages: jre/7a3630bcb5828e709af1458e3820e9195896147c (00:00:01)
Task 2 | 22:55:16 | Creating new packages: kafka/19b391f16f78880c9ccef4f6048626c2382c9575 (00:00:00)
Task 2 | 22:55:16 | Creating new packages: topic_manager/70fda9430ec0166d3f2ad8be3639bd9b38e165ed (00:00:00)
Task 2 | 22:55:16 | Creating new jobs: admin_tools/633e34fe1c14b4d107bc083cbccb09b9d5409a54 (00:00:00)
Task 2 | 22:55:16 | Creating new jobs: kafka_server/306f5a32325fb0528c4879c341c75bbc7c1e79e8 (00:00:00)
Task 2 | 22:55:16 | Creating new jobs: smoke_tests/51a9cec047d9278aa81aec0e4348540964fe1e1e (00:00:00)
Task 2 | 22:55:16 | Creating new jobs: zookeeper_server/3b3d8585414d328eb4827ba8df11dc46e0a71460 (00:00:00)
Task 2 | 22:55:16 | Release has been created: kafka-example-service/0+dev.3 (00:00:00)

Task 2 Started  Wed Sep 20 22:55:15 UTC 2017
Task 2 Finished Wed Sep 20 22:55:16 UTC 2017
Task 2 Duration 00:00:01
Task 2 done

Succeeded

```

## Step 3: Set Up the Kafka Example Service Adapter

- git clone  
``
$ git clone https://github.com/pivotal-cf-experimental/kafka-example-service-adapter-release.git
``

- 最新にアップデート  
```
$ cd kafka-example-service-adapter-release
$ git submodule update --init --recursive
```

- bosh create releaseの実行    
```
$ bosh -e vbox create-release --name kafka-example-service-adapter
Blob download 'go/go1.7.3.linux-amd64.tar.gz' (83 MB) (id: 8328b5bd-7249-4c87-bdb6-5cb5bb4f1ee2 sha1: ead40e884ad4d6512bcf7b3c7420dd7fa4a96140) started
Blob download 'go/go1.7.3.linux-amd64.tar.gz' (id: 8328b5bd-7249-4c87-bdb6-5cb5bb4f1ee2) finished
Adding job 'kafka-service-adapter/cc7533bf545024d8aa68366849a49bfe00a789be'...
Added job 'kafka-service-adapter/cc7533bf545024d8aa68366849a49bfe00a789be'
-- Started downloading 'go/cce4270e614be07d19c9e15ba795472a12e1489c' (sha1=5ce57dc9a376be68afcd62474559d20b4741489e)
-- Finished downloading 'go/cce4270e614be07d19c9e15ba795472a12e1489c' (sha1=5ce57dc9a376be68afcd62474559d20b4741489e)
Adding package 'odb-service-adapter/59bfe2b60cc9140a610e6a18a9b127c5aef09dab'...
Added package 'odb-service-adapter/59bfe2b60cc9140a610e6a18a9b127c5aef09dab'

Added dev release 'kafka-example-service-adapter/0.15.0-rc-1+dev.1'

Name         kafka-example-service-adapter  
Version      0.15.0-rc-1+dev.1              
Commit Hash  eb78a9d                        

Job                                                             Digest                                    Packages             
kafka-service-adapter/cc7533bf545024d8aa68366849a49bfe00a789be  85c08efeabc6fb3de030dde4d19870fd52279fb3  odb-service-adapter  

1 jobs

Package                                                       Digest                                    Dependencies  
go/cce4270e614be07d19c9e15ba795472a12e1489c                   5ce57dc9a376be68afcd62474559d20b4741489e  -             
odb-service-adapter/59bfe2b60cc9140a610e6a18a9b127c5aef09dab  70930c9d74f1e1d63ca4f6ae610545d01b8dab71  go            

2 packages

Succeeded
jpxxichimt1m2:kafka-example-service-adapter-release ichimt1$
```

- bosh upload releaseの実行

```
$ bosh -e vbox upload-release
Using environment '192.168.50.6' as client 'admin'

######################################################### 100.00% 199.09 MB/s 0s
Task 3

Task 3 | 23:02:04 | Extracting release: Extracting release (00:00:00)
Task 3 | 23:02:04 | Verifying manifest: Verifying manifest (00:00:00)
Task 3 | 23:02:04 | Resolving package dependencies: Resolving package dependencies (00:00:00)
Task 3 | 23:02:04 | Creating new packages: go/cce4270e614be07d19c9e15ba795472a12e1489c (00:00:01)
Task 3 | 23:02:05 | Creating new packages: odb-service-adapter/59bfe2b60cc9140a610e6a18a9b127c5aef09dab (00:00:00)
Task 3 | 23:02:05 | Creating new jobs: kafka-service-adapter/cc7533bf545024d8aa68366849a49bfe00a789be (00:00:00)
Task 3 | 23:02:05 | Release has been created: kafka-example-service-adapter/0.15.0-rc-1+dev.1 (00:00:00)

Task 3 Started  Wed Sep 20 23:02:04 UTC 2017
Task 3 Finished Wed Sep 20 23:02:05 UTC 2017
Task 3 Duration 00:00:01
Task 3 done

Succeeded

```

### Step 4. ODB releaseのアップロード

- on-demand service broker のアップロード

```
$ bosh -e vbox upload-release on-demand-service-broker-0.17.0.tgz
Using environment '192.168.50.6' as client 'admin'

######################################################### 100.00% 161.76 MB/s 0s
Task 4

Task 4 | 23:03:07 | Extracting release: Extracting release (00:00:01)
Task 4 | 23:03:08 | Verifying manifest: Verifying manifest (00:00:00)
Task 4 | 23:03:08 | Resolving package dependencies: Resolving package dependencies (00:00:00)
Task 4 | 23:03:08 | Creating new packages: broker-post-start/92910ee9d3ebd99c7b18dca4df71622c52cdcb85 (00:00:00)
Task 4 | 23:03:08 | Creating new packages: broker/def8cb8b4322019a079d446118b52655434b4b13 (00:00:00)
Task 4 | 23:03:08 | Creating new packages: broker_utils/20110a05ed6aaf6e7af8d86cd13b93012660bc9d (00:00:00)
Task 4 | 23:03:08 | Creating new packages: cf-cli/183e06ec37abcdf675eadc9a7b1d303d6d59896a (00:00:00)
Task 4 | 23:03:08 | Creating new packages: collect-service-metrics/b28c453bddfa7dbf8f34785e35f7bcdfe086c835 (00:00:00)
Task 4 | 23:03:08 | Creating new packages: common/aab45ca3aa88ff57055aca210e4b8a6d85224662 (00:00:00)
Task 4 | 23:03:08 | Creating new packages: delete-all-service-instances-and-deregister-broker/67b5067c2def1e66f7f6b4ff70e7e0fb73b9c6da (00:00:00)
Task 4 | 23:03:08 | Creating new packages: delete-all-service-instances/4f8a383d753053f83af13150bf0b03600d226a5a (00:00:00)
Task 4 | 23:03:08 | Creating new packages: go/2ba01a92a613790f3670901ccfefea1dbe9eb2ce (00:00:01)
Task 4 | 23:03:09 | Creating new packages: orphan-deployments/c609b1c2a955232060afaf39a027c893d2fba1d2 (00:00:00)
Task 4 | 23:03:09 | Creating new packages: upgrade-all-service-instances/09ca5eb6510a8bd51292b73e6d9932f47fc85fdc (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: broker/e65f76fbc4e29bf98c0e07b49bb0f9791dbc1a3b (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: delete-all-service-instances-and-deregister-broker/dee48e394427676bd8548ed39605a912559f6f84 (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: delete-all-service-instances/0af2d8b69cedc7ddc786ef61b0e1d0f9579fe0fc (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: deregister-broker/428f41f8ff2ae9227df5a9ee2dd3eaa372dff0b9 (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: orphan-deployments/8d2806d26c10f7716bc89e3ad9ed22b7d3de4eba (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: register-broker/3391f31af7eeb3d8291233de4b6b4519ba5b7190 (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: service-metrics-adapter/95ba128d068eaa0598dbd534e81db7aac134af56 (00:00:00)
Task 4 | 23:03:09 | Creating new jobs: upgrade-all-service-instances/f723151989e0b4631c3cc29a70b3989a2c7d70ba (00:00:00)
Task 4 | 23:03:09 | Release has been created: on-demand-service-broker/0.17.0 (00:00:00)

Task 4 Started  Wed Sep 20 23:03:07 UTC 2017
Task 4 Finished Wed Sep 20 23:03:09 UTC 2017
Task 4 Duration 00:00:02
Task 4 done

Succeeded

```

## Step 5: Create a BOSH Deployment

### deploy cloud config

- 新しくディレクトリを作成して、クラウド(IaaS or Virtualbox)上での構成情報をcloud_config.ymlにて指定

cloud_config.yml
```
vm_types:
- name: container
  cloud_properties: {}

networks:
- name: kafka
  type: manual
  subnets:
  - range: 10.244.1.0/24
    gateway: 10.244.1.1
    az: lite
    cloud_properties: {}

disk_types:
- name: ten
  disk_size: 10_000
  cloud_properties: {}

azs:
- name: lite
  cloud_properties: {}

compilation:
  workers: 2
  reuse_compilation_vms: true
  network: kafka
  az: lite
  cloud_properties: {}
```

- upload cloud configの実施

```
$ bosh -e vbox update-cloud-config cloud_config.yml
Using environment '192.168.50.6' as client 'admin'

+ azs:
+ - cloud_properties: {}
+   name: lite

+ vm_types:
+ - cloud_properties: {}
+   name: container

+ compilation:
+   az: lite
+   cloud_properties: {}
+   network: kafka
+   reuse_compilation_vms: true
+   workers: 2

+ networks:
+ - name: kafka
+   subnets:
+   - az: lite
+     cloud_properties: {}
+     gateway: 10.244.1.1
+     range: 10.244.1.0/24
+   type: manual

+ disk_types:
+ - cloud_properties: {}
+   disk_size: 10000
+   name: ten

Continue? [yN]: y

Succeeded
```

- Director のステータス確認
> docs.pivotal.ioではbosh v1でのオペレーションなので注意
> 下記は、bosh v2でのオペレーション

```
$ bosh -e vbox env
Using environment '192.168.50.6' as client 'admin'

Name      Bosh Lite Director                    
UUID      d8beb213-502a-4f6f-a6e7-263f64edf2ff  
Version   263.2.0 (00000000)                    
CPI       warden_cpi                            
Features  compiled_package_cache: disabled      
          config_server: disabled               
          dns: disabled                         
          snapshots: disabled                   
User      admin                                 

Succeeded

```

- Bosh上に展開（デプロイ）する際の内容を記述したマニュフェストファイルの作成

deployment_manifest.yml
> service-releaseについては、versionの指定が必要
> version: 0+dev.3 # 取得するにはdeployするのが一番？

```
name: kafka-on-demand-broker

director_uuid: d8beb213-502a-4f6f-a6e7-263f64edf2ff

releases:
- name: &broker-release on-demand-service-broker
  version: latest
- name: &service-adapter-release kafka-example-service-adapter
  version: latest
- name: &service-release kafka-example-service
  version: latest

stemcells:
- alias: trusty
  os: ubuntu-trusty
  version: 3262.2

instance_groups:
- name: broker
  instances: 1
  vm_type: container
  persistent_disk_type: ten
  stemcell: trusty
  azs: [lite]
  networks:
  - name: kafka
  jobs:
  - name: kafka-service-adapter
    release: *service-adapter-release
  - name: admin_tools
    release: *service-release
  - name: broker
    release: *broker-release
    properties:
      port: 8080
      username: broker #or replace with your own
      password: password #or replace with your own
      disable_ssl_cert_verification: true
      bosh:
        url: https://192.168.50.6:25555
        authentication:
          basic:
            username: admin
            password: <bosh admin password>
      cf:
        url: https://api.local.pcfdev.io
        authentication:
          url: https://uaa.local.pcfdev.io
          user_credentials:
            username: admin
            password: admin
      service_adapter:
        path: /var/vcap/packages/odb-service-adapter/bin/service-adapter
      service_deployment:
        releases:
        - name: *service-release
          version: 0+dev.3 # 取得するにはdeployするのが一番？
          jobs: [kafka_server, zookeeper_server]
        stemcell:
          os: ubuntu-trusty
          version: 3262.2
      service_catalog:
        id: D94A086D-203D-4966-A6F1-60A9E2300F72
        service_name: kafka-service-with-odb
        service_description: Kafka Service
        bindable: true
        plan_updatable: true
        tags: [kafka]
        plans:
        - name: small
          plan_id: 11789210-D743-4C65-9D38-C80B29F4D9C8
          description: A Kafka deployment with a single instance of each job and persistent disk
          instance_groups:
          - name: kafka_server
            vm_type: container
            instances: 1
            persistent_disk_type: ten
            azs: [lite]
            networks: [kafka]
          - name: zookeeper_server
            vm_type: container
            instances: 1
            persistent_disk_type: ten
            azs: [lite]
            networks: [kafka]
          properties:
            auto_create_topics: true
            default_replication_factor: 1

update:
  canaries: 1
  canary_watch_time: 30000-180000
  update_watch_time: 30000-180000
  max_in_flight: 4
```

- BoshへのDeploy

> docs.pivotal.ioではbosh v1でのオペレーション
> bosh v2ではdeploymentを省略して、deployを実行

```
$ bosh -e vbox -d kafka-on-demand-broker deploy deployment_manifest.yml
Using environment '192.168.50.6' as client 'admin'

Using deployment 'kafka-on-demand-broker'

+ azs:
+ - cloud_properties: {}
+   name: lite

+ vm_types:
+ - cloud_properties: {}
+   name: container

+ compilation:
+   az: lite
+   cloud_properties: {}
+   network: kafka
+   reuse_compilation_vms: true
+   workers: 2

+ networks:
+ - name: kafka
+   subnets:
+   - az: lite
+     cloud_properties: {}
+     gateway: 10.244.1.1
+     range: 10.244.1.0/24
+   type: manual

+ disk_types:
+ - cloud_properties: {}
+   disk_size: 10000
+   name: ten

+ stemcells:
+ - alias: trusty
+   os: ubuntu-trusty
+   version: '3262.2'

+ releases:
+ - name: on-demand-service-broker
+   version: 0.17.0
+ - name: kafka-example-service-adapter
+   version: 0.15.0-rc-1+dev.1
+ - name: kafka-example-service
+   version: 0+dev.3

+ update:
+   canaries: 1
+   canary_watch_time: 30000-180000
+   max_in_flight: 4
+   update_watch_time: 30000-180000

+ director_uuid: d8beb213-502a-4f6f-a6e7-263f64edf2ff

+ instance_groups:
+ - azs:
+   - lite
+   instances: 1
+   jobs:
+   - name: kafka-service-adapter
+     release: kafka-example-service-adapter
+   - name: admin_tools
+     release: kafka-example-service
+   - name: broker
+     properties:
+       bosh:
+         authentication:
+           basic:
+             password: "<redacted>"
+             username: "<redacted>"
+         url: "<redacted>"
+       cf:
+         authentication:
+           url: "<redacted>"
+           user_credentials:
+             password: "<redacted>"
+             username: "<redacted>"
+         url: "<redacted>"
+       disable_ssl_cert_verification: "<redacted>"
+       password: "<redacted>"
+       port: "<redacted>"
+       service_adapter:
+         path: "<redacted>"
+       service_catalog:
+         bindable: "<redacted>"
+         id: "<redacted>"
+         plan_updatable: "<redacted>"
+         plans:
+         - description: "<redacted>"
+           instance_groups:
+           - azs:
+             - "<redacted>"
+             instances: "<redacted>"
+             name: "<redacted>"
+             networks:
+             - "<redacted>"
+             persistent_disk_type: "<redacted>"
+             vm_type: "<redacted>"
+           - azs:
+             - "<redacted>"
+             instances: "<redacted>"
+             name: "<redacted>"
+             networks:
+             - "<redacted>"
+             persistent_disk_type: "<redacted>"
+             vm_type: "<redacted>"
+           name: "<redacted>"
+           plan_id: "<redacted>"
+           properties:
+             auto_create_topics: "<redacted>"
+             default_replication_factor: "<redacted>"
+         service_description: "<redacted>"
+         service_name: "<redacted>"
+         tags:
+         - "<redacted>"
+       service_deployment:
+         releases:
+         - jobs:
+           - "<redacted>"
+           - "<redacted>"
+           name: "<redacted>"
+           version: "<redacted>"
+         stemcell:
+           os: "<redacted>"
+           version: "<redacted>"
+       username: "<redacted>"
+     release: on-demand-service-broker
+   name: broker
+   networks:
+   - name: kafka
+   persistent_disk_type: ten
+   stemcell: trusty
+   vm_type: container

+ name: kafka-on-demand-broker

Continue? [yN]: y

Task 5

Task 5 | 00:00:36 | Preparing deployment: Preparing deployment (00:00:00)
Task 5 | 00:00:36 | Preparing package compilation: Finding packages to compile (00:00:00)
Task 5 | 00:00:36 | Compiling packages: broker_utils/20110a05ed6aaf6e7af8d86cd13b93012660bc9d
Task 5 | 00:00:36 | Compiling packages: go/2ba01a92a613790f3670901ccfefea1dbe9eb2ce
Task 5 | 00:01:00 | Compiling packages: broker_utils/20110a05ed6aaf6e7af8d86cd13b93012660bc9d (00:00:24)
Task 5 | 00:01:00 | Compiling packages: topic_manager/70fda9430ec0166d3f2ad8be3639bd9b38e165ed (00:00:01)
Task 5 | 00:01:01 | Compiling packages: jre/7a3630bcb5828e709af1458e3820e9195896147c (00:00:17)
Task 5 | 00:01:18 | Compiling packages: kafka/19b391f16f78880c9ccef4f6048626c2382c9575 (00:00:03)
Task 5 | 00:01:21 | Compiling packages: go/cce4270e614be07d19c9e15ba795472a12e1489c
Task 5 | 00:01:31 | Compiling packages: go/2ba01a92a613790f3670901ccfefea1dbe9eb2ce (00:00:55)
Task 5 | 00:01:32 | Compiling packages: broker-post-start/92910ee9d3ebd99c7b18dca4df71622c52cdcb85
Task 5 | 00:01:48 | Compiling packages: go/cce4270e614be07d19c9e15ba795472a12e1489c (00:00:27)
Task 5 | 00:01:48 | Compiling packages: broker/def8cb8b4322019a079d446118b52655434b4b13
Task 5 | 00:01:49 | Compiling packages: broker-post-start/92910ee9d3ebd99c7b18dca4df71622c52cdcb85 (00:00:17)
Task 5 | 00:01:49 | Compiling packages: odb-service-adapter/59bfe2b60cc9140a610e6a18a9b127c5aef09dab (00:00:16)
Task 5 | 00:02:07 | Compiling packages: broker/def8cb8b4322019a079d446118b52655434b4b13 (00:00:19)
Task 5 | 00:02:07 | Creating missing vms: broker/d367c9a9-e75f-45f6-86ff-d5cf394b008d (0) (00:00:37)
Task 5 | 00:02:44 | Updating instance broker: broker/d367c9a9-e75f-45f6-86ff-d5cf394b008d (0) (canary) (00:00:55)

Task 5 Started  Thu Sep 21 00:00:36 UTC 2017
Task 5 Finished Thu Sep 21 00:03:39 UTC 2017
Task 5 Duration 00:03:03
Task 5 done

Succeeded

```


- instanceの確認

```
$ bosh -e vbox instances
Using environment '192.168.50.6' as client 'admin'

Task 6. Done

Deployment 'kafka-on-demand-broker'

Instance                                     Process State  AZ    IPs         
broker/d367c9a9-e75f-45f6-86ff-d5cf394b008d  running        lite  10.244.1.2  

1 instances

Succeeded

```


## Step 6: Create a Service Broker on PCF Dev

### login to pcfdev

### Create service broker for kafka
```
$ cf create-service-broker kafka-broker broker password http://10.244.1.2:8080
Creating service broker kafka-broker as admin...
OK
```

### Check the status and enable the access


```
$ cf service-access -b kafka-broker
Getting service access for broker kafka-broker as admin...
broker: kafka-broker
   service                  plan    access   orgs
   kafka-service-with-odb   small   none
$ cf enable-service-access kafka-service-with-odb
Enabling access to all plans of service kafka-service-with-odb for all orgs as admin...
OK

```

### Check the marketplace

cf marketplace( or cf m)
```
$ cf m
Getting services from marketplace in org pcfdev-org / space pcfdev-space as admin...
OK

service                  plans             description
kafka-service-with-odb   small             Kafka Service
local-volume             free-local-disk   Local service docs: https://github.com/cloudfoundry-incubator/local-volume-release/
p-mysql                  512mb, 1gb        MySQL databases on demand
p-rabbitmq               standard          RabbitMQ is a robust and scalable high-performance multi-protocol messaging broker.
p-redis                  shared-vm         Redis service to provide a key-value store

```

### Create Service

```
$ cf cs kafka-service-with-odb small k2
Creating service instance k2 in org pcfdev-org / space pcfdev-space as admin...
OK

Create in progress. Use 'cf services' or 'cf service k2' to check operation status.
```

```
$ cf service k2

Service instance: k2
Service: kafka-service-with-odb
Bound apps:
Tags:
Plan: small
Description: Kafka Service
Documentation url:
Dashboard: http://example_dashboard.com/83046608-77f3-4279-8572-0dd446684215

Last Operation
Status: create succeeded
Message: Instance provisioning completed
Started: 2017-09-21T05:09:53Z
Updated: 2017-09-21T05:11:56Z
```

```
$ bosh -e vbox instances
Using environment '192.168.50.6' as client 'admin'

Task 51. Done

Deployment 'kafka-on-demand-broker'

Instance                                     Process State  AZ    IPs         
broker/06b9c2cb-4ef1-406c-8625-25290153b874  running        lite  10.244.1.2  

1 instances


Task 52. Done

Deployment 'service-instance_83046608-77f3-4279-8572-0dd446684215'

Instance                                               Process State  AZ    IPs         
kafka_server/4d583c8d-0e5d-4b8b-845a-0c888e62e2e8      running        lite  10.244.1.3  
zookeeper_server/ebf97065-4a0c-44ad-9d40-bbe8c2847b59  running        lite  10.244.1.4  

2 instances

Succeeded

```

### ODB コンセプト
https://docs.pivotal.io/svc-sdk/odb/0-17/concepts.html
