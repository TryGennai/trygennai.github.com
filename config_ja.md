---
layout: manual_ja
title: Setting / genn.ai
---

# genn.ai 設定

> ここでは、genn.aiを構成する各種設定について記載します。

## 設定ファイル

genn.aiで使用している主な設定ファイルは下記の2つです。

* gungnir.yaml (client)
* gungnir.yaml (server)


## ローカルモードと分散モード <a name="mode"></a>

ここでは、GungnirServer/TupleStoreServerのローカルモード(local)と分散モード(distributed)について記載します。

### ローカルモード

GungnirServerとTupleStoreServerが、同一のプロセスで稼働します。GungnirServer/TupleStoreServerは1つのホストでのみ実行します。

起動には下記コマンドを実行します。

    $ cd $GUNGNIR_INSTALL_DIR
    $ ./bin/gungnir-server.sh start

停止には下記コマンドを実行します。

    $ cd $GUNGNIR_INSTALL_DIR
    $ ./bin/gungnir-server.sh stop


### 分散モード

GungnirServerとTupleStoreServerが、それぞれ別プロセスとして稼働します。GungnirServer/TupleStoreServerをそれぞれ別のホスト、複数のホスト、同一筐体で複数のプロセスとして稼働させることが可能です。

それぞれの起動には下記コマンドを実行します。

    $ cd $GUNGNIR_INSTALL_DIR
    $ ./bin/gungnir-server.sh start
    $ ./bin/tuple-store-server.sh start


それぞれの停止には下記コマンドを実行します。

    $ cd $GUNGNIR_INSTALL_DIR
    $ ./bin/gungnir-server.sh stop
    $ ./bin/tuple-store-server.sh stop

分散モードにおけるGungnirServer/TupleStoreServerのクラスタ情報は、ZooKeeperアンサンブルに保存されています。

分散モードに接続するクライアントツール(gungnir, post)は、ZooKeeperアンサンブルから常に稼働中のGungnirServer/TupleStoreServerを知ります。従って、一部のGungnirServer/TupleStoreServerが障害によりクラスタから離脱しても、稼働中のサーバにのみアクセスすることで可用性を備えています。

## gungnir.yaml (client)

[クライアントツール](/cli_ja.html)(gungnir, post)に関する設定項目です。

### GungnirServerに関する設定

#### gungnir.server.host <a name="c.gungnir.server.host"></a>

[クライアントツール](/cli_ja.html)(gungnir, post)がアクセスするGungnirServerのHostを、名前解決が可能なホスト名かIPで指定します。GungnirServerが稼働しているホスト以外からのアクセス時に設定をする必要があります。

この設定値が使用されるのは、GungnirServerが **ローカルモード** で稼働している場合です。 **分散モード** で稼働している場合には、ここで設定した値は使用されず、 **cluster.zookeeper.servers** で指定したZooKeeperアンサンブルから接続先のGungnirServerの情報(host/port)を取得します。

> Default: "localhost"

#### gungnir.server.port <a name="c.gungnir.server.port"></a>

[クライアントツール](/cli_ja.html)(gungnir, post)がアクセスするGungnirServerのPort番号を指定します。GungnirServerの設定において、 **gungnir.server.port** を変更している場合に設定をする必要があります。

この設定値が使用されるのは、GungnirServerが **ローカルモード** で稼働している場合です。 **分散モード** で稼働している場合には、ここで設定した値は使用されず、 **cluster.zookeeper.servers** で指定したZooKeeperアンサンブルから接続先のGungnirServerの情報(host/port)を取得します。

> Default: 7100

### TupleStoreServerに関する設定

#### tuple.store.server.host <a name="c.tuple.store.server.host"></a>

[クライアントツール](/cli_ja.html)(gungnir, post)がアクセスするTupleStoreServerのHostを、名前解決が可能なホスト名かIPで指定します。TupleStoreServerが稼働しているホスト以外からのアクセス時に設定をする必要があります。

この設定値が使用されるのは、TupleStoreServerが **ローカルモード** で稼働している場合です。 **分散モード** で稼働している場合には、ここで設定した値は使用されず、 **cluster.zookeeper.servers** で指定したZooKeeperアンサンブルから接続先のTupleStoreServerの情報(host/port)を取得します。

> Default: "localhost"

#### tuple.store.server.port <a name="c.tuple.store.server.port"></a>

[クライアントツール](/cli_ja.html)(gungnir, post)がアクセスするTupleStoreServerのPort番号を指定します。GungnirServer/TupleStoreServerの設定において、 **tuple.store.server.port** を変更している場合に設定をする必要があります。

この設定値が使用されるのは、TupleStoreServerが **ローカルモード** で稼働している場合です。 **分散モード** で稼働している場合には、ここで設定した値は使用されず、 **cluster.zookeeper.servers** で指定したZooKeeperアンサンブルから接続先のTupleStoreServerの情報(host/port)を取得します。

> Default: 7200

### 分散モードに関する設定

#### cluster.mode <a name="c.cluster.mode"></a>

設定可能な値は **local** (ローカルモード)/ **distributed** (分散モード)です。

分散モードを指定している場合、 **gungnir.server.host**, **gungnir.server.port**, **tuple.store.server.host**, **tuple.store.server.port** の各設定値は使用されません。[クライアントツール](/cli_ja.html)(gungnir, post)が接続するGungnirServer/TupleStoreServerに関する情報は **cluster.zookeeper.servers** で指定したZooKeeperアンサンブルから取得します。

> Default: "local"

#### cluster.zookeeper.servers <a name="c.cluster.zookeeper.servers"></a>

GungnirServer/TupleStoreServerの各設定を保存するZooKeeperアンサンブルを構成するZooKeeperサーバをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。

この設定は、分散モード時にのみ使用されます。 **gungnir.server.host**, **gungnir.server.port**, **tuple.store.server.host**, **tuple.store.server.port** の各設定値は、指定したZooKeeperが構成するアンサンブルから取得して使用されます。

> Default: - "localhost:2181"

> Example:
> 
    cluster.zookeeper.servers:
      - "10.0.1.11:2181"
      - "10.0.1.12:2181"
      - "10.0.1.13:2181"

#### cluster.zookeeper.session.timeout <a name="c.cluster.zookeeper.session.timeout"></a>

分散モードにおいて、ZooKeeperアンサンブルと[クライアントツール](/cli_ja.html)(gungnir, post)間のセッションタイムアウトまでの時間をミリ秒で指定します。

デフォルト設定ではStormが使用しているセッションタイムアウト時間と同じ値を使用しています。Stormのデフォルト設定値は20000が設定されています。クライアントツールの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.session.timeout}

#### cluster.zookeeper.connection.timeout <a name="c.cluster.zookeeper.connection.timeout"></a>

分散モードにおいて、ZooKeeperアンサンブルと[クライアントツール](/cli_ja.html)(gungnir, post)間の接続時におけるタイムアウト時間をミリ秒で指定します。

デフォルト設定ではStormが使用しているコネクションタイムアウト時間と同じ値を使用しています。Stormのデフォルト設定値は15000が設定されています。クライアントツールの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.connection.timeout}

#### cluster.zookeeper.retry.times <a name="c.cluster.zookeeper.retry.times"></a>

分散モードにおいて、ZooKeeperアンサンブルと[クライアントツール](/cli_ja.html)(gungnir, post)間の接続が切れた場合、再接続を試行する回数を指定します。

デフォルト設定ではStormが使用している試行回数と同じ値を使用しています。Stormのデフォルト設定値は5が設定されています。クライアントツールの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.retry.times}

#### cluster.zookeeper.retry.interval <a name="c.cluster.zookeeper.retry.interval"></a>

分散モードにおいて、ZooKeeperアンサンブルと[クライアントツール](/cli_ja.html)(gungnir, post)間の接続が切れた場合、再接続を試行する間隔をミリ秒で指定します。

デフォルト設定ではStormが使用している間隔時間と同じ値を使用しています。Stormのデフォルト設定値は1000が設定されています。クライアントツールの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.retry.interval}

### Clientに関する設定

#### gungnir.client.response.timeout <a name="c.gungnir.client.response.timeout"></a>

[クライアントツール](/cli_ja.html)(gungnir, post)がGungnirServer/TupleStoreServerからのレスポンスを待つ最大許容時間をミリ秒で指定します。指定時間内にGungnirServer/TupleStoreServerからのレスポンスが無い場合、クライアントツールは処理を中断します。

> Default: 10000

### Monitorに関する設定

#### kafka.monitor.zookeeper.servers <a name="c.kafka.monitor.zookeeper.servers"></a>

Monitor機能を使用する際に、[クライアントツール](/cli_ja.html)(gungnir)がアクセスするKafkaクラスタ情報を保持しているZooKeeperアンサンブルを構成するZooKeeperサーバをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。

> Default: - "localhost:2181"

## gungnir.yaml (server)

サーバプロセス(GungnirServer, TupleStoreServer)に関する設定項目です。

### GungnirServerに関する設定

#### gungnir.server.port <a name="s.gungnir.server.port"></a>

GungnirServerが待ち受ける(LISTENする)ポート番号を指定します。

> Default: 7100

#### gungnir.server.pid.file <a name="s.gungnir.server.pid.file"></a>

GungnirServerプロセスのPIDを出力するファイルを指定します。出力先はgungnir-serverのインストールディレクトリ直下です。

> Default: gungnir-server.pid 

#### session.timeout.secs <a name="s.session.timeout.secs"></a>

GungnirServerのセッションタイムアウト時間を秒で指定します。[クライアントツール](/cli_ja.html)(gungnir)との接続で指定した時間以上、クライアントツールからの操作が無い場合にセッションはタイムアウトします。

> Default: 3600

#### command.processor.cache.size <a name="s.command.processor.cache.size"></a>

GungnirServerにおいて、CommandProcessorのインスタンスをキャッシュするサイズを指定します。

> Default: 1024

### TupleStoreServerに関する設定

#### tuple.store.server.port <a name="s.tuple.store.server.port"></a>

TupleStoreServerが待ち受ける(LISTENする)ポート番号を指定します。

> Default: 7200

#### tuple.store.server.pid.file <a name="s.tuple.store.server.pid.file"></a>

TupleStoreServerのPIDを出力するファイルを指定します。出力先はgungnir-serverのインストールディレクトリ直下です。

**分散モード** での稼働時のみ設定値が有効になります。

> Default: tuple-store-server.pid

#### tracking.cookie.maxage <a name="s.tracking.cookie.maxage"></a>

TupleStoreServerからクライアントに送られるCOOKIEの残存時間を秒で指定します。

> Default: 864000000

#### persistent.deser.queue.size <a name="s.persistent.deser.queue.size"></a>

TupleStoreServerがクライアントからデータを受領後、Kafkaへ書き込みを行う際にシリアライズを行うまでの待機キューのサイズを指定します。シリアライズが遅延している場合、 **persistent.deser.parallelism** と共に調整を行ってください。

> Default: 1024

#### persistent.deser.parallelism <a name="s.persistent.deser.parallelism"></a>

TupleStoreServerがクライアントから受領したデータをシリアライズする並列度を指定します。シリアライズが遅延している場合、 **persistent.deser.queue.size** と共に調整を行ってください。

> Default: 32

#### persistent.deserializer <a name="s.persistent.deserializer"></a>

TupleStoreServerがシリアライズする処理クラスを指定します。現時点では、使用可能なクラスは他にありません。

> Default: org.gennai.gungnir.tuple.persistent.JsonPersistentDeserializer

#### persistent.emitter.queue.size <a name="s.persistent.emitter.queue.size"></a>

シリアライズされたTupleをKafkaに書き込む際の待機キューのサイズを指定します。Kafkaへの書き込みが遅延している場合、 **persistent.emitter.parallelism** 、Kafkaのクラスタ設定と共に調整してください。

> Default: 1024

#### persistent.emitter.parallelism <a name="s.persistent.emitter.parallelism"></a>

シリアライズされたTupleをKafkaに書き込む際の並列度を指定します。Kafkaへの書き込みが遅延している場合、 **persistent.emitter.queue.size** 、Kafkaのクラスタ設定と共に調整してください。

> Default: 32

#### persistent.emit.tuples.max <a name="s.persistent.emit.tuples.max"></a>

シリアライズされたTupleをKafkaに書き込む際、一度に書き込むTuple数の最大値を指定します。この設定値は、 **persistent.emitter.queue.size** でサイズを指定したキューにTupleが複数滞留している場合に使用されます。通常は、キューにTupleが1つでも存在すれば、Kafkaへの書き込み処理が実行されます。

設定値より少ないTuple数でも、Tupleの合計サイズが **persistent.emit.tuples.max.size** で設定された値よりも大きくなった場合、Kafkaへの書き込みが実行されます。

> Default: 8

#### persistent.emit.tuples.max.size <a name="s.persistent.emit.tuples.max.size"></a>

シリアライズされたTupleをKafkaに書き込む際、一度に書き込むTupleの合計サイズの最大値を設定します。この設定値は、 **persistent.emitter.queue.size** でサイズを指定したキューにTupleが複数滞留している場合に使用されます。通常は、キューにTupleが1つでも存在すれば、Kafkaへの書き込み処理が実行されます。

設定値より小さな合計サイズでも、Tuple数が **persistent.emit.tuples.max** で設定された値に達している場合、Kafkaへの書き込みが実行されます。

> Default: 1024

#### persistent.emitter <a name="s.persistent.emitter"></a>

TupleStoreServerからTopologyへ、Tupleを送信する処理を行うクラスを指定します。デフォルト設定では、TupleStoreServerはKafkaにTupleを書き込みます。Kafkaに書き込みを行うことで、Tupleが一定期間保存されることになります。

gungnir-standalone.yamlを用いたStandaloneモードで起動すると、 **persistent.emitter** には **InMemoryEmitter** が適用され、Kafkaを起動することなく動作の確認を行えます。ただし **cluster.mode** 、 **storm.cluster.mode** が共に **local** の場合にのみ使用することができます。

> Default: org.gennai.gungnir.tuple.persistent.KafkaPersistentEmitter

> Example: gungnir-standalone.yamlでの設定
org.gennai.gungnir.tuple.persistent.InMemoryEmitter

### Clusterに関する設定

#### cluster.mode <a name="s.cluster.mode"></a>

GungnirServer/TupleStoreServerの起動モードを指定します。設定可能な値は **local** (ローカルモード)/ **distributed** (分散モード)です。

**ローカルモード** を指定した場合、TupleStoreServerはGungnirServerと同じプロセス内で稼働します。 **分散モード** を指定した場合、TupleStoreServerはGungnirServerと別プロセスで稼働する為、`gungnir-server.sh`とは別に`tuple-store-server.sh`で起動する必要があります。

> Default: "local"

#### cluster.zookeeper.servers <a name="s.cluster.zookeeper.servers"></a>

GungnirServer/TupleStoreServerの各設定を保持しているZooKeeperアンサンブルを構成するZooKeeperサーバをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。分散モード時にのみ使用されます。

> Default: - "localhost:2181"

#### cluster.zookeeper.session.timeout <a name="s.cluster.zookeeper.session.timeout"></a>

分散モードにおいて、ZooKeeperアンサンブルとサーバプロセス(GungnirServer, TupleStoreServer)間のセッションタイムアウトを行う時間をミリ秒で指定します。

デフォルト設定ではStormが使用しているセッションタイムアウト時間と同じ値を使用しています。Stormのデフォルト設定値は20000が設定されています。サーバプロセスの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.session.timeout}

#### cluster.zookeeper.connection.timeout <a name="s.cluster.zookeeper.connection.timeout"></a>

分散モードにおいて、ZooKeeperアンサンブルとサーバプロセス(GungnirServer, TupleStoreServer)間の接続時におけるタイムアウト時間をミリ秒で指定します。

デフォルト設定ではStormが使用しているコネクションタイムアウト時間と同じ値を使用しています。Stormのデフォルト設定値は15000が設定されています。サーバプロセスの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.connection.timeout}

#### cluster.zookeeper.retry.times <a name="s.cluster.zookeeper.retry.times"></a>

分散モードにおいて、ZooKeeperアンサンブルとサーバプロセス(GungnirServer, TupleStoreServer)間の接続が切れた場合、再接続を試行する回数を指定します。

デフォルト設定ではStormが使用している試行回数と同じ値を使用しています。Stormのデフォルト設定値は5が設定されています。サーバプロセスの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.retry.times}

#### cluster.zookeeper.retry.interval <a name="s.cluster.zookeeper.retry.interval"></a>

分散モードにおいて、ZooKeeperアンサンブルとサーバプロセス(GungnirServer, TupleStoreServer)間の接続が切れた場合、再接続を試行する間隔をミリ秒で指定します。

デフォルト設定ではStormが使用している間隔時間と同じ値を使用しています。Stormのデフォルト設定値は1000が設定されています。サーバプロセスの設定値のみ変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更をしてください。

> Default: ${storm.zookeeper.retry.interval}

### 管理サーバに関する設定

#### gungnir.admin.server.port <a name="s.gungnir.admin.server.port"></a>

管理サーバが待ち受ける(LISTENする)ポート番号を指定します。この設定を有効にした場合にのみ管理サーバが起動されます。

> Default: 9192

ブラウザもしくはcurl等で、下記のようにサーバプロセスに関するレポートを取得する事ができます。

> Example:
    http://127.0.0.1:9192/stats.txt

取得したレポートでは下記項目等を確認する事ができます。

* リクエスト数
* リクエストのレイテンシ


#### gungnir.admin.server.backlog <a name="s.gungnir.admin.server.backlog"></a>

クライアントからの接続が、HTTPサーバ(管理サーバ用)に受け入れられるのを待機するキューに入れるTCP接続の最大数を指定します。

> Default: 100

### Stormに関する設定

#### storm.cluster.mode <a name="s.storm.cluster.mode"></a>

Stromの稼働モードを指定します。指定可能な値は **local** (Stormのworkerを、GungnirServerのプロセス内にスレッドで起動する)もしくは、 **distributed** (稼働しているStormクラスタを利用する)です。

> Default: "local"

#### storm.nimbus.host <a name="s.storm.nimbus.host"></a>

稼働しているStormクラスタのNimbusホストを、名前解決ができるホスト名もしくはIPで指定します。 **storm.cluster.mode** が **distributed** である場合にのみ使用されます。

> Default: "localhost"

### Topologyに関する設定

#### topology.workers <a name="s.topology.workers"></a>

投入するTopologyが稼働するStormのworker数のデフォルト値を指定します。`SET`文を使用することで、各Topology毎に変更することが可能です。

> Default: 1

#### topology.status.check.times <a name="s.topology.status.check.times"></a>

投入されたTopologyの起動もしくは停止時に、Topologyの状態を確認する回数の最大値を指定します。GungnirServerは、この設定値と **topology.status.check.interval** の設定値を乗じた時間内に、Topologyの状態変更を検知できない場合、処理をロールバックします。

> Default: 20

#### topology.status.check.interval <a name="s.topology.status.check.interval"></a>

投入されたTopologyの起動もしくは停止時に、Topologyの状態を確認する間隔をミリ秒で指定します。GungnirServerは、この設定値と **topology.status.check.times** の設定値を乗じた時間内に、Topologyの状態変更を検知できない場合、処理をロールバックします。

> Default: 2000

#### default.parallelism <a name="s.default.parallelism"></a>

Operatorの並列度のデフォルト値を指定します。クエリに`parallelism`句を記述している場合、クエリに記述された値が使用されます。クエリで`parallelism`句が記述されていないOperatorには、設定値が適用されます。この設定項目は`SET`文でTopology毎に設定する事が可能です。

> Default: 1

### Metastoreに関する設定

#### metastore <a name="s.metastore"></a>

GungnirServerのメタ情報を格納するのに使用するクラスを指定します。

設定可能な値は、デフォルト設定の **MongoDbMetaStore** と **InMemoryMetaStore** です。 **InMemoryMetaStore** はGungnirServerが起動している間のみ利用可能なMetastoreです。GungnirServerを再起動すると各メタ情報は消失します。メタ情報を永続的にするには **MongoDbMetaStore** を使用してください。 **MongoDbMetaStore** を使用する場合、 **metastore.mongodb.servers** と共に指定する必要があります。

> Default: org.gennai.gungnir.metastore.MongoDbMetaStore

#### metastore.mongodb.servers <a name="s.metastore.mongodb.servers"></a>

メタ情報の格納に **MongoDbMetaStore** を使用する場合に、接続先のMongoDBのホストをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。

> Default: - "localhost:27017"

### TupleStoreに関する設定

#### kafka.brokers <a name="s.kafka.brokers"></a>

TupleStoreに使用するKafkaのBrokerをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。

> Default: - "localhost:9092"

#### kafka.required.acks <a name="s.kafka.required.acks"></a>

TupleStoreServerがKafkaにTupleを書き込む際に、どの時点で応答を返すかを設定します。設定可能な値は 0, 1, -1 です。
詳細は[Kafkaのドキュメント](http://kafka.apache.org/documentation.html)を参照してください。

> Default: 1

#### kafka.producer.type <a name="s.kafka.producer.type"></a>

TupleStoreServerがKafkaにTupleを書き込む際の処理を指定します。同期(sync)/非同期(async)を指定できます。

> Default: "sync"

#### kafka.auto.commit.interval <a name="s.kafka.auto.commit.interval"></a>

TupleStoreServerがKafkaに書き込みを行う際に、AutoCommitを実行する間隔をミリ秒で指定します。

> Default: 10000

#### kafka.zookeeper.servers <a name="s.kafka.zookeeper.servers"></a>

TupleStoreServerが書き込むKafkaのクラスタ情報を保持するZooKeeperアンサンブルを構成するZooKeeperサーバをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。

> Default: - "localhost:2181"

#### kafka.zookeeper.session.timeout <a name="s.kafka.zookeeper.session.timeout"></a>

TupleStoreServerが、Kafkaのクラスタ情報を保持するZooKeeperアンサンブルとのセッションを、タイムアウトする時間をミリ秒で指定します。

> Default: 6000

#### kafka.zookeeper.connection.timeout <a name="s.kafka.zookeeper.connection.timeout"></a>

TupleStoreServerが、Kafkaのクラスタ情報を保持するZooKeeperアンサンブルに接続を試みる際のタイムアウト時間をミリ秒で指定します。

デフォルト設定ではGungnirServerがStormのクラスタ情報を保持するZooKeeperアンサンブルとの設定と同じ値を使用します。Stormのデフォルト設定値は15000が設定されています。サーバプロセスの設定値のみを変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更してください。

> Default: ${storm.zookeeper.connection.timeout}

#### kafka.zookeeper.retry.times <a name="s.kafka.zookeeper.retry.times"></a>

Kafkaクラスタの情報を保持するZooKeeperアンサンブル間とのセッションが切断された場合に、再接続を試行する回数を指定します。

デフォルト設定ではStormが使用している試行回数と同じ値を使用しています。Stormのデフォルト設定値は5が設定されています。サーバプロセスの設定値のみを変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更してください。

> Default: ${storm.zookeeper.retry.times}

#### kafka.zookeeper.retry.interval <a name="s.kafka.zookeeper.retry.interval"></a>

Kafkaクラスタの情報を保持するZooKeeperアンサンブル間とのセッションが切断された場合に、再接続を試行する間隔をミリ秒で指定します。

デフォルト設定ではStormが使用している間隔時間と同じ値を使用しています。Stormのデフォルト設定値は1000が設定されています。サーバプロセスの設定値のみを変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更してください。

> Default: ${storm.zookeeper.retry.interval}

### Monitorに関する設定

#### monitor.enabled <a name="s.monitor.enabled"></a>

Monitor機能を使用可能とするかを指定します。

> Default: false

#### kafka.monitor.brokers <a name="s.kafka.monitor.brokers"></a>

Monitor機能に使用するKafkaクラスタのBrokerをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。

> Default: - "localhost:9092"

#### kafka.monitor.required.acks <a name="s.kafka.monitor.required.acks"></a>

Monitor機能を使用する際に、KafkaクラスタからAckをどのタイミングで受け取るかを設定します。詳細に関しては[Kafkaのドキュメント](http://kafka.apache.org/documentation.html)を参照してください。

> Default: 1

#### kafka.monitor.auto.commit.interval <a name="s.kafka.monitor.auto.commit.interval"></a>

Monitor機能において、KafkaのAutoCommitを実行する間隔をミリ秒で指定します。

> Default: 10000

#### kafka.monitor.zookeeper.servers <a name="s.kafka.monitor.zookeeper.servers"></a>

Monitor機能を使用する際にアクセスするKafkaクラスタの情報を保持するZooKeeperアンサンブルをリスト形式で指定します。

> Default: - "localhost:2181"

#### kafka.monitor.zookeeper.session.timeout <a name="s.kafka.monitor.zookeeper.session.timeout"></a>

Monitor機能を使用する際に、Kafkaクラスタの情報を保持するZooKeeperアンサンブルとの接続に適用されるセッションタイムアウトをミリ秒で指定します。

デフォルト設定ではStormが使用しているセッションタイムアウト時間と同じ値を使用しています。Stormのデフォルト設定値は20000が設定されています。Monitor機能使用時の設定値のみを変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更してください。

> Default: ${storm.zookeeper.session.timeout}

#### kafka.monitor.zookeeper.connection.timeout <a name="s.kafka.monitor.zookeeper.connection.timeout"></a>

Monitor機能を使用する際に、アクセスするKafkaクラスタの情報を保持するZooKeeperアンサンブルとの接続におけるタイムアウト時間をミリ秒で指定します。

デフォルト設定ではStormが使用しているコネクションタイムアウト時間と同じ値を使用しています。Stormのデフォルト設定値は15000が設定されています。Monitor機能使用時の設定値のみを変更したい場合は、この設定項目を変更してください。Stormの設定値と共に変更したい場合は、Stormをインストールしたディレクトリ配下にあるconf/storm.yamlにて変更してください。

> Default: ${storm.zookeeper.connection.timeout}

#### kafka.monitor.zookeeper.sync.timeout <a name="s.kafka.monitor.zookeeper.sync.timeout"></a>

Monitor機能を使用する際に、アクセスするKafkaクラスタの情報を保持するZooKeeperアンサンブルとの接続に適用される同期処理時間のタイムアウトをミリ秒で指定します。

> Default: 2000

### Operatorに関する設定

#### spout.operator.queue.size <a name="s.spout.operator.queue.size"></a>

`SPOUT`オペレータが内部に保持するキューのサイズを指定します。

> Default: 1024

#### emit.operator.queue.size <a name="s.emit.operator.queue.size"></a>

`EMIT`オペレータが内部に保持するキューのサイズを指定します。

> Default: 1024

#### emit.operator.emit.tuples.max <a name="s.emit.operator.emit.tuples.max"></a>

`EMIT`オペレータが保持するキューに同時に複数のタプルが到着している場合、一度に書き出すタプルの最大数を指定します。

> Default: 8

### Processorに関する設定

#### kafka.spout.fetch.size <a name="s.kafka.spout.fetch.size"></a>

**Kafka Spout Processor** が、Kafkaから一度に取得するデータのサイズを指定します。

> Default: 1048576

#### kafka.spout.fetch.interval <a name="s.kafka.spout.fetch.interval"></a>

**Kafka Spout Processor** が、Kafkaからデータを取得する間隔をミリ秒で指定します。

> Default: 1000

#### kafka.spout.offset.behind.max <a name="s.kafka.spout.offset.behind.max"></a>

**Kafka Spout Processor** において、オフセットが書き込まれない最大の許容時間を指定します。

> Default: 9223372036854775807

#### kafka.spout.state.update.interval <a name="s.kafka.spout.state.update.interval"></a>

**Kafka Spout Processor** が、状態を更新する間隔をミリ秒で指定します。

> Default: 2000

#### kafka.spout.topic.replication.factor <a name="s.kafka.spout.topic.replication.factor"></a>

Topologyの投入時に、 **Kafka Spout Processor** がKafkaに作成するトピックのレプリケーション数を指定します。

> Default: 1

#### kafka.spout.read.brokers.retry.times <a name="s.kafka.spout.read.brokers.retry.times"></a>

**Kafka Spout Processor** がKafkaからデータを取得する際に、担当するパーティションのリーダー情報を取得するのに試行する回数を指定します。

> Default: 5

#### kafka.spout.read.brokers.retry.interval <a name="s.kafka.spout.read.brokers.retry.interval"></a>

**Kafka Spout Processor** がKafkaからデータを取得する際に、担当するパーティションのリーダー情報を取得するのに試行する間隔をミリ秒で指定します。

> Default: 1000

#### kafka.spout.partition.operation.retry.times <a name="s.kafka.spout.partition.operation.retry.times"></a>

**Kafka Spout Processor** がKafkaからデータを取得する時、Kafkaクラスタの状態によってパーティション情報が変更されている場合があります。その際に、 **Kafka Spout Processor** にて再度Kafkaのパーティション情報を取得する試行回数を指定します。

> Default: 5

#### kafka.spout.partition.operation.retry.interval <a name="s.kafka.spout.partition.operation.retry.interval"></a>

**Kafka Spout Processor** がKafkaからデータを取得する時、Kafkaクラスタの状態によってパーティション情報が変更されている場合があります。その際に、 **Kafka Spout Processor** にて再度Kafkaのパーティション情報を取得する試行の間隔時間をミリ秒で指定します。

> Default: 1000

#### mongo.fetch.servers <a name="s.mongo.fetch.servers"></a>

`JOIN`句において **Mongo Fetch Processor** を用いたい際に、結合するデータの読み込み元となるMongoDBサーバをリスト形式で指定します。`[host|IP]:[port]`の書式で指定します。

> Default: - "localhost:27017"

#### kafka.emit.brokers <a name="s.kafka.emit.brokers"></a>

`EMIT`句において **Kafka Emit Processor** を用いた際に、出力先となるKafkaクラスタのBrokerをリスト形式で指定します。

> Default: - "localhost:9092"

#### kafka.emit.required.acks <a name="s.kafka.emit.required.acks"></a>

`EMIT`句において **Kafka Emit Processor** を用いた際に、出力先となるKafkaクラスタからAckをどのタイミングで受け取るかを指定します。詳細に関しては[Kafkaのドキュメント](http://kafka.apache.org/documentation.html)を参照してください。

> Default: 1

#### mongo.persist.servers <a name="s.mongo.persist.servers"></a>

EMIT句において **Mongo Persist Processor** を用いた際に、出力先となるMongoDBサーバをリスト形式で指定します。

> Default: - "localhost:27017"

### Metricsに関する設定

#### metrics.reporters <a name="s.metrics.reporters"></a>

GungnirServer/TupleStoreServerにおけるメトリクス情報を取得するクラス、取得間隔秒、出力先を指定します。

> Default:
>
    - reporter:org.gennai.gungnir.metrics.CsvMetricsReporter
      interval.secs: 60
      output.dir: ${gungnir.home}/logs

> Default: 60

#### topology.metrics.enabled <a name="s.topology.metrics.enabled"></a>

StormのTopologyに関するメトリクス情報を取得するかを指定します。Topologyのメトリクス情報は、Stormをインストールしたディレクトリ配下のlogsディレクトリに出力されます。

> Default: false

#### topology.metrics.consumer <a name="s.topology.metrics.consumer"></a>

StormのTopologyに関するメトリクス情報を取得するクラスを指定します。

> Default: backtype.storm.metric.LoggingMetricsConsumer

#### topology.metrics.consumer.parallelism <a name="s.topology.metrics.consumer.parallelism"></a>

StormのTopologyに関するメトリクス情報を取得する実行処理の並列度を指定します。

> Default: 1

#### topology.metrics.interval.secs <a name="s.topology.metrics.interval.secs"></a>

StormのTopologyに関するメトリクス情報を取得する間隔を秒で指定します。

> Default: 60
