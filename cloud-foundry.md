## Cloud Foundryにデプロイ

ここまで作成してきたサービスをCloud Foundryにデプロイします。

まずは全てのサービスを停止してください。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/281de1b2-85c5-bd8a-a611-ebe1a488e409.png)

今回は[Pivotal Web Services](https://run.pivotal.io/)にデプロイしますが、無償枠ではアプリケーション合計でメモリを2GBまでしか使用できないため、各アプリケーションに以下のメモリを配分します。併せて、アプリケーションのURLも記載します。

**以降、`tmaki`の部分を自分のアカウント名またはイニシャルに置き換えてください。**

| Application | Memory (MB) | URL |
|-----------|------------|------|
| `config-server` | 340 | http://config-server-tmaki.cfapps.io |
| `eureka-server` | 358 | http://eureka-server-tmaki.cfapps.io |
| `membership` | 450 | http://member-tmaki.cfapps.io |
| `recommendations` | 450 | http://recommendations-tmaki.cfapps.io |
| `ui` | 450 | http://ui-tmaki.cfapps.io |

`hystrix-dashboard`はメモリ不足のためデプロイしません。
またデプロイするアプリケーションに割くメモリ量が小さいため、動作が不安定です。

本ページで作成するソースコードとスクリプトは[こちら](https://github.com/making/metflix/tree/05_cloud-foundry)(`05_cloud-foundry`ブランチ)から参照可能です。

#### Pivotal Web Servicesにログイン

``` console
$ cf login -a api.run.pivotal.io
```

#### User Provided Serviceの作成


まずはデプロイするConfig ServerとEureka Serverをバックエンドサービスとして、Membershipサービス、Recommendationsサービス、UIサービスにアタッチできるように、User Provided Serviceを作成します。

``` console
$ cf create-user-provided-service config-server -p '{"uri":"http://config-server-tmaki.cfapps.io"}'
$ cf create-user-provided-service eureka-server -p '{"uri":"http://eureka-server-tmaki.cfapps.io"}'
```

> **Tips**
> 
> [スクリプトによる作業自動化](#スクリプトによる作業自動化)の準備を行えば、
> 
> ``` console
> $ cd $WORKSHOP
> $ ./scripts/deploy.sh
> ```
> 
> を実行することで、アプリケーションのデプロイと同時にUser Provided Serviceの作成もできるため、ここではUser Provided Serviceの作成をスキップしても構いません。

#### `bootstrap.properties`の変更

Config Serverとしてバックエンドサービスを使えるように`bootstrap.properties`を変更します。

1. `eureka-sever`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/05_cloud-foundry/eureka-server/src/main/resources/bootstrap.properties)に変更
1. `membership`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/05_cloud-foundry/membership/src/main/resources/bootstrap.properties)に変更
1. `recommendations`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/05_cloud-foundry/recommendations/src/main/resources/bootstrap.properties)に変更
1. `ui`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/05_cloud-foundry/ui/src/main/resources/bootstrap.properties)に変更

### アプリケーションのビルド

`bootstrap.properties`を変更したら全てのプロジェクトをビルドします

``` console
$ cd $WORKSHOP/config-server
$ ./mvnw clean package -Dmaven.test.skip=true
$ java -jar target/config-server-0.0.1-SNAPSHOT.jar
```

``` console
$ cd $WORKSHOP/eureka-server
$ ./mvnw clean package -Dmaven.test.skip=true
$ java -jar target/eureka-server-0.0.1-SNAPSHOT.jar
```

``` console
$ cd $WORKSHOP/membership
$ ./mvnw clean package -Dmaven.test.skip=true
$ java -jar target/membership-0.0.1-SNAPSHOT.jar
```

``` console
$ cd $WORKSHOP/recommendations
$ ./mvnw clean package -Dmaven.test.skip=true
$ java -jar target/recommendations-0.0.1-SNAPSHOT.jar
```

``` console
$ cd $WORKSHOP/ui
$ ./mvnw clean package -Dmaven.test.skip=true
$ java -jar target/ui-0.0.1-SNAPSHOT.jar
```

> **Tips**
> 
> [スクリプトによる作業自動化](#スクリプトによる作業自動化)の準備を行えば、
> 
> ``` console
> $ cd $WORKSHOP
> $ ./scripts/build.sh
> ```
> 
> を実行することで、全アプリケーションのビルドが行われます。


### Manifestファイルのダウンロード

Cloud Foundryに各アプリケーションをデプロイするためのManifestマニフェストファイルを用意します。

[`config-server`の`manifest.yml`](https://github.com/making/metflix/blob/05_cloud-foundry/config-server/manifest.yml)

``` console
$ cd $WORKSHOP/config-server
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/config-server/manifest.yml
```

[`eureka-server`の`manifest.yml`](https://github.com/making/metflix/blob/05_cloud-foundry/eureka-server/manifest.yml)

``` console
$ cd $WORKSHOP/eureka-server
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/eureka-server/manifest.yml
```

[`membership`の`manifest.yml`](https://github.com/making/metflix/blob/05_cloud-foundry/membership/manifest.yml)

``` console
$ cd $WORKSHOP/membership
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/membership/manifest.yml
```

[`recommendations`の`manifest.yml`](https://github.com/making/metflix/blob/05_cloud-foundry/recommendations/manifest.yml)

``` console
$ cd $WORKSHOP/recommendations
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/recommendations/manifest.yml
```

[`ui`の`manifest.yml`](https://github.com/making/metflix/blob/05_cloud-foundry/ui/manifest.yml)

``` console
$ cd $WORKSHOP/ui
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/ui/manifest.yml
```

### Cloud Foundryへアプリケーションをデプロイ

Manifestマニフェストファイルを使ってCloud Foundryにjarをデプロイします。

``` console
$ cd $WORKSHOP/config-server
$ cf push
```

``` console
$ cd $WORKSHOP/eureka-server
$ cf push
```

``` console
$ cd $WORKSHOP/membership
$ cf push
```

``` console
$ cd $WORKSHOP/recommendations
$ cf push
```

``` console
$ cd $WORKSHOP/ui
$ cf push
```



> **Tips**
> 
> [スクリプトによる作業自動化](#スクリプトによる作業自動化)の準備を行えば、
> 
> ``` console
> $ cd $WORKSHOP
> $ ./scripts/deploy.sh
> ```
> 
> を実行することで、User Provided Service及び、アプリケーションのデプロイが行われます。


### 動作確認

[http://ui-tmaki.cfapps.io](http://ui-tmaki.cfapps.io)にアクセス (`tmaki`を自分のアカウントまたはイニシャルに変更してください)。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/47c85439-418e-6fea-6e01-4816243a1241.png)


### スクリプトによる作業自動化

* アプリケーションのビルド
* User Provided Serviceの作成とアプリケーションのデプロイ

は[こちら](https://github.com/making/metflix/tree/05_cloud-foundry/scripts)のスクリプトを使えば自動で行えます。

``` console
$ cd $WORKSHOP
$ mkdir scripts
$ cd scripts
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/scripts/common.sh
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/scripts/build.sh
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/scripts/deploy.sh
$ wget https://github.com/making/metflix/raw/05_cloud-foundry/scripts/cleanup.sh
$ chmod +x *.sh
$ cd ..
```

`common.sh`に定義されている変数`suffix`を`tmaki`から自分のアカウントまたはイニシャルに変更してください。


以下のコマンドでデプロイしたアプリケーションの削除とUser Provided Serviceの削除を行うことができます。

``` console
$ cd $WORKSHOP
$ ./scripts/cleanup.sh
```
