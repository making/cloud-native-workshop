## Distributed Tracingの導入

次に、Distributed Tracingの考え方を導入し、1リクエスト中の各サービスの呼び出しに関してどこでどのくらいの時間がかかっているのかを把握しましょう。
トレージングデータを収集、検索、視覚化するサーバーとして[Zipkin](http://zipkin.io/)を利用します。
各サービスからトレージングデータを透過的に送信するクライアント(instrumentation)として[Spring Cloud Sleuth](https://cloud.spring.io/spring-cloud-sleuth/)を利用します。

本ページで作成するソースコードは[こちら](https://github.com/making/metflix/tree/07_distributed-tracing)(`07_distributed-tracing`ブランチ)から参照可能です。

### Zipkin Serverの作成

#### 作業手順

1. File -> New -> Spring Starter Project
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/642499a3-0f6f-8e3d-6f65-499808937abf.png)

1. * Name : `zipkin-server`
 * Group: `com.metflix`
 * Artifact: `zipkin-server`
 * Package: `com.metflix`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/6d22969f-ea9c-dad5-038a-52293444629e.png)

1. * Cloud Config -> `Config Client`
 * Cloud Tracing -> `Zipkin UI`, `Zipkin Server`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/af03d5b7-35f0-f1e9-2919-4eba4ffe451d.png)


1. workspaceを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/3c606669-de0e-71d6-4b13-a73e42281f87.png)

1. `src/main/java/com/metflix/ZipkinServerApplication.java`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/zipkin-server/src/main/java/com/metflix/ZipkinServerApplication.java)に変更
1. `src/main/resources/application.properties`を**削除**
1. `src/main/resources`を右クリック -> New -> File 
1. File name: `bootstrap.properties`
1. `src/main/resources/bootstrap.properties`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/zipkin-server/src/main/resources/bootstrap.properties)に変更 
1. forkした[`metflix-confg`](https://github.com/making/metflix-config)プロジェクトの[`service-registry`](https://github.com/making/metflix-config/tree/service-registry)ブランチに`zipkin-server.properties`を作成して、[この内容](https://github.com/making/metflix-config/blob/service-registry/zipkin-server.properties)に変更
<img width="733" alt="スクリーンショット 0028-06-18 18.45.53.png" src="https://qiita-image-store.s3.amazonaws.com/0/1852/cf66e9bb-8924-a8b4-c151-4582b7b65da9.png">

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/23aad312-50a5-18ba-cf6d-514fb2dd4958.png)



#### 動作確認

Package Explorerの`ZipkinServerApplication.java`を右クリック -> Run As -> Spring Boot App

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/c1644acd-0fad-0650-0a96-71da2f05a573.png)

コンソールを確認。9411番ポートで起動していない場合はConfig Serverの設定が正しくありません。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/22ca8466-6be6-6724-f41e-e74706111cc8.png)

[http://localhost:9411](http://localhost:9411)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/76b6a36f-e4f4-e285-2fec-8ab444354a32.png)


> Zipkin Serverは以下のように実行可能jarをダウンロードすることでも実行可能です。
>
> ``` console
> $ wget -O zipkin.jar 'https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=LATEST&c=exec'
> $ java -jar zipkin.jar
> ```
> ストレージの変更やプロパティの変更を行う場合は、Zipkin Server用のプロジェクトを作成した方がメンテナンスしやすいです。今回はConfig Clientの設定を追加したため、あえてプロジェクトを作成しました。

### Zipkin Client (Instrumentation)の設定

Zipkinクライアントとして、各サービスにSpring Cloud Sleuth(`org.springframework.cloud:spring-cloud-starter-zipkin`)を依存関係を追加します。この依存関係を追加することにより、Springアプリケーションからの外部へのリクエスト情報(Span)が蓄積され、`org.springframework.cloud.sleuth.zipkin.ZipkinSpanReporter`クラスが非同期にZipkin Serverに情報を送信するための設定が自動で行われます。Instrument対象は[こちら](http://cloud.spring.io/spring-cloud-sleuth/spring-cloud-sleuth.html#_integrations)。

`ZipkinSpanReporter`の実装としてデフォルトでは`org.springframework.cloud.sleuth.zipkin.HttpZipkinSpanReporter`(HTTPで送信)が使用されます。
リクエスト情報は`org.springframework.cloud.sleuth.Sampler`実装クラスによってサンプリングされます。ここでは`org.springframework.cloud.sleuth.sampler.PercentageBasedSampler`が使用され、リクエストの10%がZipkin Serverに送信されます。この割合は`spring.sleuth.sampler.percentage`プロパティで変更できます(デフォルトは`0.1`)。

#### 作業手順

##### Membership

1. `membership`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/membership/pom.xml)に変更

##### Recommendations

1. `recommendations`の`pom.xml`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/recommendations/pom.xml)に変更


##### UI

#### 動作確認

1. `membership`、`recommendations`、`ui`を再起動

[http://localhost:8080](http://localhost:8080)に何回かアクセスしてください。

ログに`[appname,traceId,spanId,exportable]`が含まれることを確認してください。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/cc6bab4b-68bc-448f-ba63-13f49b3f68a0.png)

[http://localhost:9411](http://localhost:9411)にアクセスし、「`Find Traces`」ボタンを押してください。

<img width="680" alt="スクリーンショット 0028-06-18 20.05.58.png" src="https://qiita-image-store.s3.amazonaws.com/0/1852/0eb07fde-0240-89b7-a33e-f0a679c2ef5e.png">

複数のTraceが表示されます。一つクリックしてください。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/37bc3e13-f046-ed5d-fc24-bcb1e846199b.png)

選択したTraceの中の各Spanでどのくらいの時間を要しているのかが視覚的にわかります(「`Expand All`」ボタンを押すと見やすいです)。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2bbea156-eca0-276e-960b-5c91ef18a03f.png)


![image](https://qiita-image-store.s3.amazonaws.com/0/1852/c5e1ed12-f2e1-f94e-685e-fcac9e0aca63.png)

「Dependencies」タブをクリックするとサービスの依存関係がわかります。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/f91b18b0-26ad-2a85-8f14-5a268e59af20.png)


次にサンプリング率を変更して全てのリクエストが収集されるようにしましょう。

forkした[`metflix-confg`](https://github.com/making/metflix-config)プロジェクトの[`service-registry`](https://github.com/making/metflix-config/tree/service-registry)ブランチの`application.properties`に

``` properties
spring.sleuth.sampler.percentage=1.0
```

を追加してください。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/746f1fe6-c87e-068d-6149-24be9e2f8e0f.png)

各サービスを再起動するか`/refresh`にPOSTでリクエストを送ってください。開発中はこの設定が便利です。

### Cloud Foundryにデプロイ

「[Cloud Foundryにデプロイ](cloud-foundry.md)」でデプロイしたサービスにZipkin Serverを連携させましょう。

#### Zipkin Serverのデプロイ

``` console
$ cd $WORKSHOP/zipkin-server
$ ./mvnw clean package -Dmaven.test.skip=true
```

`zipkin-server`プロジェクト直下に`manifest.yml`を作成して[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/zipkin-server/manifest.yml)を設定し、`cf push`でデプロイします。(`tmaki`の部分は自分のアカウント名またはイニシャルに置き換えてください。)

``` console
$ cf push zipkin-server-tmaki
```

次にこのZipkin ServerをService Instanceとして他のサービスにバインドできるようにUser Provider Serviceを作成します。

``` console
$ cf create-user-provided-service zipkin-server -p '{"uri":"http://zipkin-server-tmaki.cfapps.io"}'
```

#### Membership, Recommendations, UIのデプロイ

`membership`, `recommendations`, `ui`の接続先のZipkin ServerのURLをUser Provided Serviceの`credintials`の値を使うように、Config Serverのプロパティをを設定します。またCloud Foundry上では`spring.sleuth.sampler.percentage`が`0.1`になるように設定します。
Cloud Foundryで起動する場合にのみ適用されるように`application-cloud.properties`を、[この内容](https://github.com/making/metflix-config/blob/service-registry/application-cloud.properties)を変更してください。


![image](https://qiita-image-store.s3.amazonaws.com/0/1852/ded2c553-29ed-b641-c1c3-bc4e492553bf.png)


次に各サービスの`manifest.yml`に`zipkin-server`サービスをバインドするように設定します。


##### Membership
`manifest.yml`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/membership/manifest.yml)に変更。

``` console
$ cd $WORKSHOP/membership
$ ./mvnw clean package -Dmaven.test.skip=true
$ cf push membership-tmaki
```

##### Recommendations
`manifest.yml`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/recommendations/manifest.yml)に変更。

``` console
$ cd $WORKSHOP/recommendations
$ ./mvnw clean package -Dmaven.test.skip=true
$ cf push recommendations-tmaki
```

##### UI
`manifest.yml`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing/ui/manifest.yml)に変更。

``` console
$ cd $WORKSHOP/ui
$ ./mvnw clean package -Dmaven.test.skip=true
$ cf push ui-tmaki
```

#### 動作確認

[http://ui-tmaki.cfapps.io](http://ui-tmaki.cfapps.io)にアクセス (tmakiを自分のアカウントまたはイニシャルに変更してください)。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/b6cf24c7-56f1-daae-8b0e-c2b9dfd2d8cd.png)

[http://zipkin-server-tmaki.cfapps.io](http://zipkin-server-tmaki.cfapps.io)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/f86fc70b-2cb3-fcb7-ecd0-3b0b1872df47.png)

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2bd913cd-1434-750b-888b-adabd41bf96c.png)


#### StrageをMySQLに変更

デフォルトではZipkin ServerのStrageはインメモリ実装(`zipkin.storage.InMemoryStorage`)なので、再起動するとデータが揮発します。また、アプリケーションのヒープを圧迫するので、開発中以外には利用できません。

ZipkinはStrageの実装に[MySQL](https://github.com/openzipkin/zipkin/tree/master/zipkin-storage/mysql)、[Cassandra](https://github.com/openzipkin/zipkin/tree/master/zipkin-storage/cassandra)、[Elasticsearch](https://github.com/openzipkin/zipkin/tree/master/zipkin-storage/elasticsearch)を選択できます。

ストレージをMySQLに変更しましょう。依存関係に`io.zipkin.java:zipkin-autoconfigure-storage-mysql`を追加すれば自動で設定されます。(`zipkin-server`プロジェクトの`pom.xml`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing-mysql/zipkin-server/pom.xml)に変更してください。)

Zipkin ServerのストレージとしてMySQLを使うためのプロパティをConfig Serverに設定します。Cloud Foundryで起動する場合にのみ適用されるように`zipkin-server-cloud.properties`を作成して、[この内容](https://github.com/making/metflix-config/blob/service-registry/zipkin-server-cloud.properties)を設定してください。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/7f41f790-92c2-ed25-5f6f-e5f06c30f2a8.png)


Pivotal Web Servicesで使えるMySQLサービス(`cleardb`)のフリープラン(`spark`)で`zipkin-mysql`というService Instanceを作成します。

``` console
$ cf create-service cleardb spark zipkin-mysql
```

`zipkin-mysql`サービスをバインドするように`manifest.yml`を[この内容](https://github.com/making/metflix/blob/07_distributed-tracing-mysql/zipkin-server/manifest.yml)に変更して、`cf push`でデプロイしましょう。

``` console
$ cd $WORKSHOP/zipkin-server
$ ./mvnw clean package -Dmaven.test.skip=true
$ cf push zipkin-server-tmaki
```


これで、Zipkin Serverのデータが永続化され、再起動しても消えません。

> ZipkinのストレージとしてのMySQLは[パフォーマンスの問題](https://github.com/openzipkin/zipkin#mysql)が知られています。高負荷下での利用を検討している場合は、CassandraやElasticsearchも検討してください。Pivotal Web ServicesではElasticsearchのサービスとして`searchly`を利用できます。
> 
> ``` console
> $ cf create-service searchly starter zipkin-elasticsearch
> ``` 
> `zipkin-autoconfigure-storage-mysql`の代わりに`zipkin-storage-elasticsearch`を使用してください。

### Spring Cloud Streamとの連携
本章で説明したZipkin ClientはHTTPのみに対応していましたが、`spring-cloud-sleuth-zipkin-stream`を使用することによりSpring Cloud Streamによるメッセージングのトレーシングも可能になります。またZipkin ServerへのレポートもSpring Cloud StreamがサポートするbinderであるRabbitMQまたはKafkaを経由して行われます。

この場合は、Zipkin Serverの実装は`ZipkinServerApplication`クラスに`@EnableZipkinServer`の代わりに`@EnableZipkinStreamServer`をつけます。



