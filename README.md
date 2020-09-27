# yamaha-router-config

YAMAHA NVR510 で、IPoE(DS-Lite)と PPPoE を併用して、ウェブサーバを外部に公開します。

## ファイル

* [my.rtfg](my.rtfg) - 筆者が使用している設定ファイル

## カスタマイズ

環境にあわせて変更してください。

```
#契約しているプロバイダのアカウント情報を設定します
 pp auth myname [プロバイダ提供アカウント名] [プロバイダ提供パスワード]
```

```
#AFTRのIPv6アドレスを設定します
 tunnel endpoint address XXXX:XXXX::XXXX:XXX
```
下記を参考に設定します。
* NTT東日本エリア
    * 2404:8e00::feed:100
    * 2404:8e00::feed:101
* NTT西日本エリア
    * 2404:8e01::feed:100
    * 2404:8e01::feed:101

```
#フィルターを設定して、サーバを外側から見られるようにします
#https(443)を使用しない場合はwwwのみでOK
ip filter 210001 pass * 192.168.100.111 tcp * www,https
#ポート番号でも指定可能です、複数のポート番号はカンマで区切ります
ip filter 210001 pass * 192.168.100.111 tcp * 80,443
```

```
#外からのアクセスを192.168.100.111に向けます
#https(443)を使用しない場合はwwwのみでOK
nat descriptor masquerade static 1000 1 192.168.100.111 tcp www,https
#ポート番号でも指定可能です、複数のポート番号はカンマで区切ります
nat descriptor masquerade static 1000 1 192.168.100.111 tcp 80,443
```

```
#MACアドレスからIPアドレスを固定します
dhcp scope bind 1 192.168.100.111 ethernet XX:XX:XX:XX:XX:XX
```
Ubuntu 18.04 では ClientID を表示するので、一般的な MAC アドレスを返すように変更します。

/etc/dhcp/dhclient.conf に下記を追記します。
```
send dhcp-client-identifier = hardware;
```

```
#ローカルネットワークでウェブサーバをドメインで開けるようにします
ip host www.primenon.com 192.168.100.111
```

## 動作環境

* [IIJmioひかり](https://www.iijmio.jp/imh/)
* NTT東日本
* VDSL 接続
* YAMAHA NVR-510 Rev.15.01.18 (Fri Jul 10 10:15:27 2020)

## 構成

* NVR-510👉192.168.100.1（デフォルト）
* ルータ下のIP👉192.168.100.2-192.168.100.191
* ウェブサーバのIP👉192.168.100.111

## 未確認

* ひかり電話の接続（筆者はひかり電話を利用していないため）
* 小型 ONU を使用した接続（筆者は VDSL 環境のため）

## 参考文献

* [pppoe-gui.rtfg](sample/pppoe-gui.rtfg) - WebGUI画面で作成される PPPoE 設定ファイル
* [dslite.rtfg](sample/dslite.rtfg) - YAMAHA公式サイトで公開されている DS-Lite 接続用サンプル設定ファイル
* [pppoe.rtfg](sample/pppoe.rtfg) - YAMAHA公式サイトで公開されている PPPoE 接続用サンプル設定ファイル

## ライセンス
MIT License
