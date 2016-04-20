## Circuit Breakerの導入

次にCircuit Breakerの導入して、障害の伝播を防ぎます。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/d7b3f0be-64e0-8a03-d83b-09c8102236bb.png)

本ページで作成するソースコードは[こちら](https://github.com/making/metflix/tree/04-circuit-breaker)(`04-circuit-breaker`ブランチ)から参照可能です。


### Hystrix Commandの作成

#### 作業手順

#### Recommendations

1. `recommendations`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/04-circuit-breaker/recommendations/pom.xml)に変更
1. `recommendations`の`src/main/java/com/metflix/RecommendationsApplication.java`を[この内容](https://github.com/making/metflix/blob/04-circuit-breaker/recommendations/src/main/java/com/metflix/RecommendationsApplication.java)に変更


#### UI

1. `ui`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/04-circuit-breaker/ui/pom.xml)に変更
1. `ui`の`src/main/java/com/metflix/UiApplication.java`を[この内容](https://github.com/making/metflix/blob/04-circuit-breaker/ui/src/main/java/com/metflix/UiApplication.java)に変更

#### 動作確認

`recommendations`と`ui`を再起動。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/7a12e579-0f53-139a-6ce4-065ecdc697e4.png)

[http://localhost:8080](http://localhost:8080)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/8e1eba55-739c-4f24-bbe5-4d8490af424b.png)

次に`membership`サービスを停止。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/0a62766f-fb90-b660-3a9b-3850030a1608.png)

再度、[http://localhost:8080](http://localhost:8080)にアクセスし、Recommendationsの結果が変わっていることを確認。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/0b9c18b3-9c75-6a42-5c13-701f8cb90b57.png)

`membership`サービスを起動。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/3d04f3bd-420e-67b5-5824-3bc32311c98f.png)

しばらくして[http://localhost:8080](http://localhost:8080)にアクセスすると復旧していることを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/dde8de71-12b9-7d14-c30a-ef66c01c9008.png)

同様に`recommendations`サービスを停止させると、Recommendationsの結果が空になっていることを確認。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/fb5ad815-df20-5827-ce80-4168f8f46324.png)

動作を確認したら`recommendations`サービスを起動。

[http://localhost:3333/hystrix.stream](http://localhost:3333/hystrix.stream)にアクセス。メトリクスのイベントストリームが流れてくることを確認。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/971293f3-c060-ba6e-eb84-ed0aad9692be.png)


### Circuit Breaker Dashboardの作成


#### 作業手順

1. File -> New -> Spring Starter Project
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/642499a3-0f6f-8e3d-6f65-499808937abf.png)

1. * Name : `hystrix-dashboard`
 * Group: `com.metflix`
 * Artifact: `hystrix-dashboard`
 * Package: `com.metflix`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2311a692-5d1d-02f1-da5d-7566802822b2.png)

1. * Cloud Circuit Breaker -> `Hystrix Dashboard`
 * Cloud Config -> `Config Client`
 * Ops -> `Actuator`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/8c774962-d0cc-3e53-a204-ecc6f3018b57.png)


1. workspaceを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/e68451c3-ffe0-3f84-d6cd-6166e0d2c1ec.png)

1. `src/main/java/com/metflix/HystrixDashboardApplication.java`を[この内容](https://github.com/making/metflix/blob/04-circuit-breaker/hystrix-dashboard/src/main/java/com/metflix/HystrixDashboardApplication.java)に変更
1. `src/main/resources/application.properties`を**削除**
1. `src/main/resources`を右クリック -> New -> File 
1. File name: `bootstrap.properties`
1. `src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/04-circuit-breaker/hystrix-dashboard/src/main/resources/bootstrap.properties)に変更 

#### 動作確認

Package Explorerの`HystrixDashboardApplication.java`を右クリック -> Run As -> Spring Boot App

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/e324f409-ee24-7d5f-710f-4bcb114a69b9.png)


コンソールを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/617b4f3f-2806-2445-3aeb-0a66e7cb95ee.png)


[http://localhost:7979/hystrix](http://localhost:7979/hystrix)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2e2b16b3-273a-2b1d-fbaa-359a878b582c.png)

`http://localhost:3333/hystrix.stream`を入力してMonitor Streamをクリック。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/bbf99ff3-76a9-e9d4-b96b-0c2395619014.png)

`http://localhost:4444/hystrix.stream`も同様

> **Tips**
> 
> 複数のストリームを集約するには別途[Turbine](http://cloud.spring.io/spring-cloud-static/spring-cloud.html#_turbine)の設定が必要です。
> また、PaaSのようにスケーアウトした各インスタンスへのURLが同一な場合は`/hystrix.stream`にアクセスしてメトリクスをpullするのではなく、AMQPを使ってpushする必要があります。この場合は、別途[Turbine AMQP](http://cloud.spring.io/spring-cloud-static/spring-cloud.html#_turbine_amqp)の設定が必要です。
