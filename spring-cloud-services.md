## Spring Cloud Servicesの利用

[Pivotal Cloud Foundry](https://pivotal.io/platform)の[Spring Cloud Services](https://pivotal.io/app-framework#coordinateanything)を利用することで、

* Config Server
* Service Registry
* Circuit Breaker Dashboard

はプラットフォームが提供するため、自前で管理不要になります。
かつこれらのサービスへのアクセスをOAuth 2によって自動で認可制御されます。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/5621efaf-63e5-235a-c25b-c04f91f9e22a.png)

`config-server`と`eureka-server`は不要なので削除して構いません。

本ページで作成するソースコードとスクリプトは[こちら](https://github.com/making/metflix/tree/06_spring-cloud-services)(`06_spring-cloud-services`ブランチ)から参照可能です。
また、Spring Cloud Servicesのマニュアルは[こちら](http://docs.pivotal.io/spring-cloud-services/index.html)です。

### アプリケーション一部修正

Spring Cloud Servicesへのアクセスを自動設定するための[Spring Cloud Services Starters](https://github.com/pivotal-cf/spring-cloud-services-starters)を使うように設定します。

本稿作成時点で、Spring Cloud Servicesに対応しているSpring Bootのバージョンが1.2、Spring CloudのバージョンがAngelであるため、アプリケーションを一部変更します。

また、[Spring Cloud Services用のコンフィギュレーション](https://github.com/making/metflix-config/tree/spring-cloud-services)を使用するように`bootstrap.properties`を変更します([差分](https://github.com/making/metflix-config/compare/service-registry...spring-cloud-services))。

`manifest.yml`もPivotal Cloud Foundry用に変更します (buildpack, memory増加, Hystrix Dashboardサービスのバインド)。

#### Members

1. `membership`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/membership/pom.xml)に変更
1. `membership`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/membership/src/main/resources/bootstrap.properties)に変更
1. `membership`の`manifest.yml`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/membership/manifest.yml)に変更

#### Recommendations

1. `recommendations`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/recommendations/pom.xml)に変更
1. `recommendations`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/recommendations/src/main/resources/bootstrap.properties)に変更
1. `recommendations`の`src/main/java/com/metflix/RecommendationsApplication.java`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/recommendations/src/main/java/com/metflix/RecommendationsApplication.java)に変更
1. `recommendations`の`manifest.yml`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/recommendations/manifest.yml)に変更


#### UI

1. `ui`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/ui/pom.xml)に変更
1. `ui`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/ui/src/main/resources/bootstrap.properties)に変更
1. `ui`の`src/main/java/com/metflix/UiApplication.java`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/ui/src/main/java/com/metflix/UiApplication.java)に変更
1. `ui`の`manifest.yml`を[この内容](https://github.com/making/metflix/blob/06_spring-cloud-services/ui/manifest.yml)に変更


### アプリケーションのビルド

`membership`、`recommendations`、`ui`プロジェクトをビルドします


``` console
$ cd $WORKSHOP/membership
$ ./mvnw clean package -Dmaven.test.skip=true
```

``` console
$ cd $WORKSHOP/recommendations
$ ./mvnw clean package -Dmaven.test.skip=true
```

``` console
$ cd $WORKSHOP/ui
$ ./mvnw clean package -Dmaven.test.skip=true
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

### Pivotal Cloud Foundryへデプロイ

#### ログイン

※ アカウントの作成は講師が行います。

``` console
$ cf login -a api.run.pez.pivotal.io
```


#### Service (Spring Cloud Services)の作成

##### Config Serverサービスの作成

GitのURLを`https://github.com/making/metflix-config`から「[Config Serverの導入](config-server.md)」でForkしたURLに変更してください。

``` console
$ cf create-service p-config-server standard config-server -c '{"git":{"uri":"https://github.com/making/metflix-config", "label":"spring-cloud-services"}}' 
```

##### Service Registryサービスの作成

``` console
$ cf create-service p-service-registry standard eureka-server
```

##### Hystrix Dashboardサービスの作成

``` console
$ cf create-service p-circuit-breaker-dashboard standard hystrix-dashboard
```

### Pivotal Cloud Foundryへアプリケーションをデプロイ

Manifestマニフェストファイルを使ってPivotal Cloud Foundryにjarをデプロイします。

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

※ Spring Cloud Servicesの各サービスは初期化に時間がかかり、初期化完了前にアプリケーションにバインドしようとするとエラー(`Server error, status code: 502, error code: 10001, message: Service broker error: Service instance is not running and available for binding.`)が発生します。しばらく待ってから再度`cf push`してください。

> **Tips**
> 
> [スクリプトによる作業自動化](#スクリプトによる作業自動化)の準備を行えば、
> 
> ``` console
> $ cd $WORKSHOP
> $ ./scripts/deploy.sh
> ```
> 
> を実行することで、Serviceの作成及び、アプリケーションのデプロイが行われます。

### 動作確認

`cf apps`、`cf services`の結果が以下のようになっていることを確認。

``` console
$ cf apps

name                    requested state   instances   memory   disk   urls   
recommendations-tmaki   started           1/1         768M     1G     recommendations-tmaki.cfapps.pez.pivotal.io   
ui-tmaki                started           1/1         768M     1G     ui-tmaki.cfapps.pez.pivotal.io   
membership-tmaki        started           1/1         768M     1G     membership-tmaki.cfapps.pez.pivotal.io   
```

``` console
$ cf services

name                service                       plan       bound apps                                          last operation   
eureka-server       p-service-registry            standard   recommendations-tmaki, ui-tmaki, membership-tmaki   create succeeded   
config-server       p-config-server               standard   recommendations-tmaki, ui-tmaki, membership-tmaki   create succeeded   
hystrix-dashboard   p-circuit-breaker-dashboard   standard   recommendations-tmaki, ui-tmaki                     create succeeded 
```

[http://ui-tmaki.cfapps.pez.pivotal.io](http://ui-tmaki.cfapps.pez.pivotal.io)にアクセス (`tmaki`を自分のアカウントまたはイニシャルに変更してください)。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/de0265b0-e3c9-69e5-5c49-70e73841b9fc.png)


以下のコマンドで、システムに負荷をかけてください。

``` console
$ while true;do curl -u making:metflix http://ui-tmaki.cfapps.pez.pivotal.io > /dev/null; done
```


[Apps Manager](https://apps.run.pez.pivotal.io)にログイン。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/105dd0ec-260a-5bc4-9958-45e85f9e942c.png)

自分のSpaceを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/8c83bec0-53da-f894-3421-49e00fcda247.png)

`config-server`のManageリンクをクリック

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/d2157386-88fe-83c0-cfc7-3b3a569f12d7.png)

`eureka-server`のManageリンクをクリック

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/97b1cfda-8dd5-2ce6-455e-978d18082502.png)


`hystrix-dashboard`のManageリンクをクリック。Turbineが有効になっており、Hystrixのストリームが集約されていることを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/e8caed31-f951-478a-296a-d8f2bb9834d5.png)

### Membershipサービスのスケールアウト

Membershipサービスを3台にスケールアウトさせましょう。

``` console
$ cf scale -i 3 membership-tmaki
```

Service Registryのインスタンス数が3になっていることを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/50c48cfc-b3aa-6113-f32b-e994bdfc8135.png)

Circuit Breaker Dashboardのスループットが変わらない（=メトリクスが集約されている)ことを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/5e406d84-c097-dfa3-5e8e-6c78b95cf11e.png)


### スクリプトによる作業自動化

* アプリケーションのビルド
* Spring Cloud Servicesの作成とアプリケーションのデプロイ

は[こちら](https://github.com/making/metflix/tree/06_spring-cloud-services/scripts)のスクリプトを使えば自動で行えます。

``` console
$ cd $WORKSHOP
$ rm -rf scripts
$ mkdir scripts
$ cd scripts
$ wget https://github.com/making/metflix/raw/06_spring-cloud-services/scripts/common.sh
$ wget https://github.com/making/metflix/raw/06_spring-cloud-services/scripts/build.sh
$ wget https://github.com/making/metflix/raw/06_spring-cloud-services/scripts/deploy.sh
$ wget https://github.com/making/metflix/raw/06_spring-cloud-services/scripts/cleanup.sh
$ chmod +x *.sh
$ cd ..
```

`common.sh`に定義されている変数`suffix`を`tmaki`から自分のアカウントまたはイニシャルに変更してください。
また、変数`git_url`を`https://github.com/making/metflix-config`から「[Config Serverの導入](config-server.md)」でForkしたURLに変更してください。

以下のコマンドでデプロイしたアプリケーションの削除とUser Provided Serviceの削除を行うことができます。

``` console
$ cd $WORKSHOP
$ ./scripts/cleanup.sh
```
