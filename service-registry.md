## Service Registryの導入

これまで各サービスのREST APIにアクセスするために物理的なURL(ホスト名 + ポート番号)を直接記述していましたが、サービスをService Registryに登録させることにより、論理的なURL(サービスID)でサービスにアクセスできるようにします。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/22156b30-224a-a93d-d77a-92c773803314.png)


本ページで作成するソースコードは[こちら](https://github.com/making/metflix/tree/03-service-registry)(`03-service-registry`ブランチ)から参照可能です。


### Eureka Serverの作成

#### 作業手順

1. File -> New -> Spring Starter Project
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/642499a3-0f6f-8e3d-6f65-499808937abf.png)

1. * Name : `eureka-server`
 * Group: `com.metflix`
 * Artifact: `eureka-server`
 * Package: `com.metflix`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/e0591ec2-0c42-5dd2-2277-6964a2a08d22.png)

1. * Cloud Config -> `Config Client`
 * Cloud Discovery -> `Eureka Server`
 * Ops -> `Actuator`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/ce2f3274-a5ac-520b-7334-f683fec95372.png)

1. workspaceを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/bd7454a2-f36b-b9a1-cbc5-0155fea1aab7.png)

1. `src/main/java/com/metflix/EurekaServerApplication.java`を[この内容](https://github.com/making/metflix/blob/03-service-registry/eureka-server/src/main/java/com/metflix/EurekaServerApplication.java)に変更
1. `src/main/resources/application.properties`を**削除**
1. `src/main/resources`を右クリック -> New -> File 
1. File name: `bootstrap.properties`
1. `src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/03-service-registry/eureka-server/src/main/resources/bootstrap.properties)に変更 

#### 動作確認

Package Explorerの`ConfigServerApplication.java`を右クリック -> Run As -> Spring Boot App

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2d8b20d2-53e1-6246-46e5-7f09501db079.png)


コンソールを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/5d34f076-1bb4-661e-4925-7400bb3a8a73.png)

[http://localhost:8761](http://localhost:8761)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/5416f09e-62e5-05be-e994-a44da61147c9.png)



### Eureka Clientの設定

#### 作業手順



##### Membership

1. `membership`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/03-service-registry/membership/pom.xml)に変更
1. `membership`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/03-service-registry/membership/src/main/resources/bootstrap.properties)に変更
1. `membership`の`src/main/java/com/metflix/MembershipApplication.java`を[この内容](https://github.com/making/metflix/blob/03-service-registry/membership/src/main/java/com/metflix/MembershipApplication.java)に変更

##### Recommendations

1. `recommendations`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/03-service-registry/recommendations/pom.xml)に変更
1. `recommendations`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/03-service-registry/recommendations/src/main/resources/bootstrap.properties)に変更
1. `recommendations`の`src/main/java/com/metflix/RecommendationsApplication.java`を[この内容](https://github.com/making/metflix/blob/03-service-registry/recommendations/src/main/java/com/metflix/RecommendationsApplication.java)に変更


##### UI

1. `ui`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/03-service-registry/ui/pom.xml)に変更
1. `ui`の`src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/03-service-registry/ui/src/main/resources/bootstrap.properties)に変更
1. `ui`の`src/main/java/com/metflix/UiApplication.java`を[この内容](https://github.com/making/metflix/blob/03-service-registry/ui/src/main/java/com/metflix/UiApplication.java)に変更

#### 動作確認

`membership`、`recommendations`、`ui`を再起動

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/fca24355-f7ee-13c3-ec02-810fcb9df433.png)

[http://localhost:8761](http://localhost:8761)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/8b3bd476-bae8-4b30-cee1-7b4be9cd7d37.png)

[http://localhost:8080](http://localhost:8080)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/1df21287-d81a-b3d5-f9c1-41a049fd0320.png)

### Membershipサービスのスケールアウト

Membershipサービスを3台にスケールアウトさせましょう。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/1f40a35a-d1c4-78a6-a338-d0758931c892.png)

``` console
$ cd $WORKSHOP/membership
$ ./mvnw clean package
$ PORT=4445 java -jar target/membership-0.0.1-SNAPSHOT.jar
```

別ターミナルでも

``` console
$ cd $WORKSHOP/membership
$ PORT=4446 java -jar target/membership-0.0.1-SNAPSHOT.jar
```

[http://localhost:8761](http://localhost:8761)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/c0b41df4-c6bc-5b7e-e16b-74e47f698fab.png)


[http://localhost:8080](http://localhost:8080)に何度かアクセスして、各Membershipサービスのログを見てそれぞれにアクセスがあることを確認してください。
