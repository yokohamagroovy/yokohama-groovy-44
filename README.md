# yokohama-groovy-44
Yokohama.groovy #44 #yokohamagroovy https://connpass.com/event/45101/

## 概要

DockerとGroovyを使って遊ぼう!

* Groovyのイメージを作ってみよう
* Groovyで作ったプロダクトをDockerで動かしてみよう
* Gradleを使ってDockerを動かしてみよう

## 準備

### Dockerのインストール

それぞれのOSに応じたdockerを[インストール手順](https://www.docker.com/products/docker)を見ながら入れてみましょう。

### インストールの確認

[Step 2. Check versions of Docker Engine, Compose, and Machine](https://docs.docker.com/docker-for-mac/#/step-2-check-versions-of-docker-engine-compose-and-machine)で、各コマンドを実行して表示されていればOKです。

## Dockerについて

### 概要

[こちら](http://www.slideshare.net/zembutsu/introduction-to-docker-management-and-operations-2nd)のスライドを使って概要を説明します。

### dockerコマンドについて

```sh
docker --help
```

各コマンドについては、[Docker コマンド](http://docs.docker.jp/engine/reference/commandline/index.html#docker)を見ながら説明します。

## Javaのイメージについて

今回は、`openjdk`をベースにします。

https://hub.docker.com/_/openjdk/

```sh
docker pull openjdk
```

pullしたイメージのjavaのversionを出力してみましょう。

```sh
docker run openjdk java -version
```

コンテナの中に入ってみましょう。

```sh
docker run -it openjdk
```

```sh
java -version
```

コンテナから抜けて、停止しましょう。

```sh
exit
```


## Groovyのイメージを作ってみよう

### Dockerfileを書いてみる

Dockerfileを書く前に、[Dockerfile リファレンス](http://docs.docker.jp/engine/reference/builder.html#dockerfile)を見ておいてください。

また、書いている途中に気になったら[Dockerfile を書くベスト・プラクティス](http://docs.docker.jp/engine/userguide/eng-image/dockerfile_best-practice.html)を見るのをお勧めします。

公式の[Groovyのインストール手順](http://groovy-lang.org/install.html#_install_binary)をDockerfileでやってみましょう。

参考になるDockerfileとして[こちら](https://gist.github.com/melix/e4b63fd684e63713c162) がありますが、こちらの中身はちょっと古いので、アップデートしつつ書いてみましょう。

出来上がるとこんな感じの[Dockerfile](01.create_groovy_docker_image/Dockerfile)になります。


### build & run!

Dockerfileが書き上がったら、イメージを作りましょう。

[`build`コマンド](http://docs.docker.jp/engine/reference/commandline/build.html)を参考にしてください。

```sh
docker build -t grimrose/docker-groovy:2.4.7 .
```

イメージが出来たら、コンテナを立ち上げてみましょう。

`run`コマンドを動かす際はいろいろなオプションが用意されていますので、[Docker run リファレンス](http://docs.docker.jp/engine/reference/run.html#docker-run)を参考にしてください。

```sh
docker run --rm -it grimrose/docker-groovy
```

```sh
java -version
```

```sh
groovy -version
```

```sh
exit
```

## Groovyで作ったプロダクトをDockerで動かしてみよう

### groovy scriptを動かしてみる

作ったGroovy Scriptを`COPY`してみましょう。

出来上がるとこんな感じの[Dockerfile](02.run_groovy_script/Dockerfile)になります。

`WORKDIR`については、[こちら](http://docs.docker.jp/engine/reference/builder.html#workdir)を見てください。

イメージを作成します。

```sh
docker build -t grimrose/yokohama-groovy-44:script .
```

コンテナを立ち上げてみましょう。

```sh
docker run --rm grimrose/yokohama-groovy-44:script
```

scriptが実行されて、表示されていればOKです。


### Gradleでfat jarにしてDockerで動かしてみる

[Shadow plugin](https://plugins.gradle.org/plugin/com.github.johnrengelman.shadow)を使ってfat jarにしたものを動かしてみましょう。

Shadow pluginの使い方は[ここ](http://imperceptiblethoughts.com/shadow/)を参考にします。

生成したfat jarを`COPY`して `java -jar`で動くようにしてみましょう。

出来上がるとこんな感じの[Dockerfile](03.run_fat_jar/Dockerfile)になります。

gradleでjarを作成します。

```sh
gradle clean build shadowJar
```

dockerでイメージを作成します。

```sh
docker build -t grimrose/yokohama-groovy-44:jar .
```

コンテナを立ち上げてみましょう。

```sh
docker run --rm grimrose/yokohama-groovy-44:jar 
```

jarが実行されて、結果が表示されていればOKです。


## Gradleを使ってDockerを動かしてみよう

Gradleでdockerに関するpluginsは[こちら](https://plugins.gradle.org/search?term=docker)です。

### GradleのタスクでDockerイメージを作ってみよう

今回は、[bmuschko/gradle-docker-plugin](https://github.com/bmuschko/gradle-docker-plugin)を利用します。

Javaのアプリケーションのイメージを作る場合は、[こちら](https://github.com/bmuschko/gradle-docker-plugin/blob/master/README.asciidoc#java-application-plugin)を参考にしてください。

dockerを[remote API](https://docs.docker.com/engine/reference/api/docker_remote_api/)を介して動かしてみる場合は、[こちら](https://github.com/bmuschko/gradle-docker-plugin/blob/master/README.asciidoc#remote-api-plugin)を参考にしてください。


## 小ネタ

タグの付いていないイメージを消す場合、このコマンドを実行します。

```sh
docker rmi $(docker images -f "dangling=true" -q)
```
