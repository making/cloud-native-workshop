# cloud-native-workshop

本ワークショップでは「Metflix」というダミー動画配信ライクなシステムをマイクロサービスアーキテクチャで構築しSpring BootとSpring Cloudの使い方を学びます。またこのマイクロサービスをCloud Foundryにデプロイする方法を学びます。

今回作成するアプリケーションは以下のような一見普通のWebアプリケーションですが、

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/4c6b6ab4-875c-2017-d6b9-81c2aaed5053.png)

フロントエンドのUIからMembership Service(会員サービス)とRecommendations Service(リコメンデーションサービス)が呼び出されています。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/a987c8a0-8d97-1f5b-12e5-c07bebf8fec7.png)

Membership ServiceとRecommendations ServiceはREST APIであり、以下のような呼び出し関係になっています。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2288f2a3-08e3-949f-89bd-4ec33e58b963.png)

このマイクロサービスアーキテクチャを支えるために、

* Config Service
* Service Registry
* Circuit Breaker

を使用します。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/c30432b0-2a63-fdd6-71ba-d8071cd55c76.png)

このシステムをSpring BootとSpring Cloud (+ Netflix OSS)で作成します。

個々のサービスをSpring Bootで開発し、Config Service、Service Registry、Circuit BreakerをSpring Cloudで実現でします。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/ffa8c4ce-6470-c116-1eee-1e92c95b3019.png)


なお、本サンプルコード作成にあたり、SpringOne2GX 2015で発表された「[Spring Cloud at Netflix](https://github.com/netflix-spring-one)」の内容を利用しています。

### Schedule

* [事前準備](prerequisite.md)
* AM 10:00 - AM 10:50 Introduction
* AM 10:50 - AM 11:00 Break
* AM 11:00 - AM 12:00 [Spring Boot](spring-boot.md)
* AM 12:00 - PM 1:00 Lunch
* PM 1:00 - PM 1:30 [Config Server](config-server.md)
* PM 1:30 - PM 2:00 [Service Registry](service-registry.md)
* PM 2:00 - PM 2:30 [Circuit Breaker](circuit-breaker.md)
* PM 2:30 - PM 2:45 Break
* PM 2:45 - PM 3:45 [Deploy to Cloud Foundry](cloud-foundry.md)
* PM 3:45 - PM 4:00 Break
* PM 4:00 - PM 5:00 [Spring Cloud Services](spring-cloud-services.md)

