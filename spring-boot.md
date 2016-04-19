## Spring Bootを始めよう

1. Mebership Service
1. Recommendations Service
1. UI

の順にサービスをSpring Bootで作成していきます。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/c000d7ca-fc98-c0f5-c516-04c67861d419.png)

### Membership Serviceの作成

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/1d6e62fb-06b4-a72e-d7f8-eddda08a9595.png)

#### 作業手順

1. File -> New -> Spring Starter Project
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/642499a3-0f6f-8e3d-6f65-499808937abf.png)

1. * Name : `membership`
 * Group: `com.metflix`
 * Artifact: `membership`
 * Package: `com.metflix`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/e4f01929-5c76-f477-bb25-f0db2364193b.png)

1. * Ops -> `Actucator`
 * Web -> `Web`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/b56f00fd-3317-9fc8-5a03-d692b96be2e4.png)
1. workspaceを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/22412502-2a9e-9f39-fc73-e7354e0ca3b9.png)
1. `src/main/java/com/metflix/MembershipApplication.java`を[この内容](https://github.com/making/metflix/blob/master/membership/src/main/java/com/metflix/MembershipApplication.java)に変更
1. `src/main/resources/application.properties`を[この内容](https://github.com/making/metflix/blob/master/membership/src/main/resources/application.properties)に変更

> **Tips**
> 
> 雛形プロジェクトは
> ``` console
> curl start.spring.io/starter.tgz \
>        -d groupId=com.metflix \
>        -d artifactId=membership \
>        -d packageName=com.metflix \
>        -d baseDir=membership \
>        -d dependencies=web,actuator \
>        -d applicationName=MembershipApplication | tar -xzvf -
> ```
> でも生成可能です。この場合、File -> Import -> Maven -> Existing Maven Projectsで生成したフォルダを選択し、プロジェクトをインポートしてください。

#### 動作確認

Package Explorerの`MembershipApplication.java`を右クリック -> Run As -> Spring Boot App
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/b0e427bf-c6cc-cdd6-b3ea-3b64fbbad960.png)

Consoleを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2c5d4ab3-b3fd-d0ae-5ff4-7cdd645e35f6.png)

[http://localhost:4444/api/members/making](http://localhost:4444/api/members/making)にアクセス
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/67e96d7c-1c5b-5ce7-7738-144d8f7681ea.png)

[http://localhost:4444/api/members/tichimura](http://localhost:4444/api/members/tichimura)にアクセス
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/ffbddb30-e975-9c68-7cad-6aa6e6c9745f.png)

アカウントを作成

``` console
$ curl -XPOST http://localhost:4444/api/members -H 'Content-Type: application/json' -d '{"user":"yamada","age":20}'
```

作成したアカウントにアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/63a0084b-bac0-18c4-d4fe-2e592338bb9b.png)

Boot Dashboardでアプリケーションの制御を行える。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/61186a8e-82f9-95f2-cba3-bb0ccaf429d6.png)



### Recommendations Serviceの作成


![image](https://qiita-image-store.s3.amazonaws.com/0/1852/46b64e20-b054-23a8-2ddf-565ce0bc731b.png)

#### 作業手順

1. File -> New -> Spring Starter Project
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/642499a3-0f6f-8e3d-6f65-499808937abf.png)

1. * Name : `recommendations`
 * Group: `com.metflix`
 * Artifact: `recommendations`
 * Package: `com.metflix`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/20e595bd-1c6e-03d7-cfbf-ace23b30ca00.png)

1. * Ops -> `Actucator`
 * Web -> `Web`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/b56f00fd-3317-9fc8-5a03-d692b96be2e4.png)
1. workspaceを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/2e31cf1a-1475-a684-826d-fc113c49e002.png)

1. `src/main/java/com/metflix/RecommendationsApplication.java`を[この内容](https://github.com/making/metflix/blob/master/recommendations/src/main/java/com/metflix/RecommendationsApplication.java)に変更
1. `src/main/resources/application.properties`を[この内容](https://github.com/making/metflix/blob/master/recommendations/src/main/resources/application.properties)に変更

> **Tips**
> 
> 雛形プロジェクトは
> ``` console
> curl start.spring.io/starter.tgz \
>        -d groupId=com.metflix \
>        -d artifactId=recommendations \
>        -d packageName=com.metflix \
>        -d baseDir=recommendations \
>        -d dependencies=web,actuator \
>        -d applicationName=RecommendationsApplication | tar -xzvf -
> ```
> でも作成可能です。

#### 動作確認

Package Explorerの`RecommendationsApplication.java`を右クリック -> Run As -> Spring Boot App

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/1f4b9fc1-6e1f-b725-e373-c150a3cc7f49.png)

Consoleを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/ec81c23c-d7b9-0d9c-cf54-65c36ab421e6.png)

[http://localhost:3333/api/recommendations/making](http://localhost:3333/api/recommendations/making)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/e5fc5994-9e9c-5f1f-5908-5d6313cae7a2.png)

Membershipサービスにアクセスがあることを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/b077db35-37cd-5ba5-12f9-40fe827018d0.png)


[http://localhost:3333/api/recommendations/tichimura](http://localhost:3333/api/recommendations/tichimura)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/9daf2eeb-d6ba-aac8-f8bb-83a69b4a0658.png)

### UIの作成

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/f8dce7cf-0e1d-2910-7f27-f92a128abb02.png)

#### 作業手順

1. File -> New -> Spring Starter Project
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/642499a3-0f6f-8e3d-6f65-499808937abf.png)

1. * Name : `ui`
 * Group: `com.metflix`
 * Artifact: `ui`
 * Package: `com.metflix`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/5afbc202-33f6-077d-cc9b-2a879905514a.png)


1. * Core -> `Security`
 * Ops -> `Actucator`
 * Web -> `Web`
 * Template Engines -> `Thymeleaf`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/a6b922ac-5f30-897a-8312-263311084910.png)

1. workspaceを確認
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/892c14ab-4ccc-a474-2136-f6ac9637442c.png)

1. `src/main/java/com/metflix/UiApplication.java`を[この内容](https://github.com/making/metflix/blob/master/ui/src/main/java/com/metflix/UiApplication.java)に変更
1. `src/main/resources/application.properties`を[この内容](https://github.com/making/metflix/blob/master/ui/src/main/resources/application.properties)に変更
1. `src/main/resource/templates`を右クリック -> New -> File
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/610f6cd8-1828-98ee-13c0-436531ff3aa9.png)
1. Filename: `index.html`
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/8e13987c-1c85-4c42-f674-c6144fc52f5b.png)
1. `src/main/resources/templates/index.html`を[この内容](https://github.com/making/metflix/blob/master/ui/src/main/resources/templates/index.html)に変更

> **Tips**
> 
> 雛形プロジェクトは
> ``` console
> curl start.spring.io/starter.tgz \
>        -d groupId=com.metflix \
>        -d artifactId=ui \
>        -d packageName=com.metflix \
>        -d baseDir=ui \
>        -d dependencies=web,thymeleaf,security,actuator \
>        -d applicationName=UiApplication | tar -xzvf -
> ```
> でも作成可能です。


### 動作確認

Package Explorerの`UiApplication.java`を右クリック -> Run As -> Spring Boot App

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/a2dfaf28-0ced-eea1-335c-55df59cdddcf.png)

コンソールを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/bf43ccdd-fcc0-f44e-ba85-e5528a4f990f.png)

[http://localhost:8080](http://localhost:8080)にアクセス

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/560242ca-3477-1f95-de59-e0ea83864ed3.png)


* ユーザー名: `making` (他のユーザー名でも可)
* パスワード: `metflix`

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/a66d0daa-a731-343f-64df-2a544960cb50.png)

MembershipサービスとRecommendationsサービスにアクセスがあることを確認

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/e022f986-0052-c5aa-8440-21e707c3fd03.png)

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/c7f1868b-ad25-6d27-a0dd-f0351f65499b.png)
