## Config Serverの導入

次にConfig Serverを導入し、コンフィギュレーションの一元管理を

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/9c2b2738-134d-7274-63ad-49d729cc633c.png)



本ページで作成するソースコードは[こちら](https://github.com/making/metflix/tree/02-config-server)(`02-config-server`ブランチ)から参照可能です。

### Config Serverの作成

#### 作業手順

1. File -> New -> Spring Starter Project
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/642499a3-0f6f-8e3d-6f65-499808937abf.png)

1. * Name : `config-server`
 * Group: `com.metflix`
 * Artifact: `config-server`
 * Package: `com.metflix`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/93c85cef-734f-704a-2118-42fc6c23ece4.png)

1. * Cloud Config -> `Config Server`
 * Ops -> `Actuator`
 * Core -> `Security`  
![image](https://user-images.githubusercontent.com/15058885/27271443-bcefda5e-54ff-11e7-8b5a-67f81a942548.png)

1. workspaceを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/1d41a637-4c37-7049-c318-a830b13c8567.png)

1. `src/main/java/com/metflix/ConfigServerApplication.java`を[この内容](https://github.com/making/metflix/blob/02-config-server/config-server/src/main/java/com/metflix/ConfigServerApplication.java)に変更

1. `src/main/resources/application.properties`を[この内容](https://github.com/making/metflix/blob/02-config-server/config-server/src/main/resources/application.properties)に変更 

1. GitHubの[metflix-config](https://github.com/making/metflix-config)プロジェクトを自分のアカウントでFork
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/0a9be48e-95d2-2052-48e7-784457edfe76.png)

1. `application.properties`の`spring.cloud.config.server.git.uri`の値を`https://github.com/making/metflix-config.git`から自分のレポジトリに変更
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/979db871-3c62-3bd7-ca36-d4cf754994e3.png)

#### 動作確認

Package Explorerの`ConfigServerApplication.java`を右クリック -> Run As -> Spring Boot App

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/44108da3-ed21-2ff5-158b-d5072e498973.png)

コンソールを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/40f2e380-e60f-8149-243d-b8edac273188.png)

[http://localhost:8888/env](http://localhost:8888/env)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/1ea61cf5-dd6f-9216-cbbc-7fb020ce1a3f.png)

[http://localhost:8888/membership/default](http://localhost:8888/membership/default)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/9be1eb77-11b0-ca5f-430c-f85948c7dea3.png)

[http://localhost:8888/recommendations/default](http://localhost:8888/recommendations/default)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/6738d31c-c6a5-2364-2e44-dba1b41f29e9.png)

[http://localhost:8888/ui/default](http://localhost:8888/ui/default)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/684a2bc4-2ef0-99ab-e5b6-e06528c3830b.png)


### Config Clientの設定

#### 作業手順

##### Membership

1. `membership`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/02-config-server/membership/pom.xml)に変更
1. `membership`の`src/main/resources/application.properties`を**削除**
1. `membership`の`src/main/resource`を右クリック -> New -> File 
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/1ff28795-1f72-6cc8-784c-a76b3a2d57ed.png)
1. File name: `bootstrap.properties`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/10a175ec-6d42-4874-01a3-ab378f37f726.png)
1. `src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/02-config-server/membership/src/main/resources/bootstrap.properties)に変更

##### Recommendations

1. `recommendations`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/02-config-server/recommendations/pom.xml)に変更
1. `recommendations`の`src/main/resources/application.properties`を**削除**
1. `recommendations`の`src/main/resource`を右クリック -> New -> File 
1. File name: `bootstrap.properties`
1. `src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/02-config-server/recommendations/src/main/resources/bootstrap.properties)に変更

##### UI

1. `ui`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/02-config-server/ui/pom.xml)に変更
1. `ui`の`src/main/resources/application.properties`を**削除**
1. `ui`の`src/main/resource`を右クリック -> New -> File 
1. File name: `bootstrap.properties`
1. `src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/02-config-server/ui/src/main/resources/bootstrap.properties)に変更
1. `src/main/java/com/metflix/UiApplication.java`を[この内容](https://github.com/making/metflix/blob/02-config-server/ui/src/main/java/com/metflix/UiApplication.java)に変更
1. `src/main/resources/templates/index.html`を[この内容](https://github.com/making/metflix/blob/02-config-server/ui/src/main/resources/templates/index.html)に変更

#### 動作確認

`membership`、`recommendations`、`ui`を再起動

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/cc78ba6a-da3e-f9da-f977-06f2f61f9d80.png)

[http://localhost:8080](http://localhost:8080)にアクセス。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/4dcd9005-b622-4b08-a824-83f3597611c7.png)



Forkした`metflix-config`プロジェクトの`ui.properties`を編集

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/d059d414-9ac3-3060-f98b-6556da84851e.png)

以下の内容を追加

``` properties
message=It's A Beautiful Day
```

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/5e644723-92ff-7cee-311c-eb9e5ba04418.png)

以下のコマンドを実行して、GitHub上のプロパティ変更をロード

``` console
$ curl -XPOST localhost:8080/refresh
["message"]
```

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/13037851-2869-4a3c-6f94-b4c5208a4d22.png)


[http://localhost:8080](http://localhost:8080)を再読み込みして変更が反映されていることを確認(**アプリケーションの再起動は不要**)。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/5b5b0f9e-d431-464b-23ed-4ad53e60d7ad.png)

以下のコマンドで一時的なプロパティ変更も可能

``` console
$ curl -XPOST localhost:8080/env -d message="Message is updated"
{"message":"Message is updated"}
$ curl -XPOST localhost:8080/refresh
[]
```

[http://localhost:8080](http://localhost:8080)を再読み込みして変更が反映されていることを確認。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/02400789-5410-7d86-000e-04b248b9ea29.png)
