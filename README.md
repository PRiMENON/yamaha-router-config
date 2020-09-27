# yamaha-router-config

YAMAHA NVR510 で、IPoE(DS-Lite)と PPPoE を併用して、ウェブサーバを外部に公開します。

## ファイル

* [my.rtfg](my.rtfg)

## カスタマイズ

環境にあわせて変更してください。

### プロバイダ設定
```
#契約しているプロバイダのアカウント情報を設定します
 pp auth myname [プロバイダ提供アカウント名] [プロバイダ提供パスワード]
```

### AFTR
```
#AFTRのIPv6アドレスを設定します
 tunnel endpoint address XXXX:XXXX::XXXX:XXX
```
[INTERNET MULTIFEED のAFTRのIPv6アドレス設定について](https://www.mfeed.ad.jp/transix/dslite/yamaha.html)を参考に設定します。

### フィルター
```
#フィルターを設定して、サーバを外側から見られるようにします
#https(443)を使用しない場合はwwwのみでOK
ip filter 210001 pass * 192.168.100.111 tcp * www,https
#ポート番号でも指定可能です、複数のポート番号はカンマで区切ります
ip filter 210001 pass * 192.168.100.111 tcp * 80,443
```

### マスカレード
```
#外からのアクセスを192.168.100.111に向けます
#https(443)を使用しない場合はwwwのみでOK
nat descriptor masquerade static 1000 1 192.168.100.111 tcp www,https
#ポート番号でも指定可能です、複数のポート番号はカンマで区切ります
nat descriptor masquerade static 1000 1 192.168.100.111 tcp 80,443
```

### ウェブサーバのIPアドレスを固定させるため DHCP の予約アドレスを設定する
```
#MACアドレスからIPアドレスを固定します
dhcp scope bind 1 192.168.100.111 ethernet XX:XX:XX:XX:XX:XX
```
Ubuntu 18.04 では ClientID を表示するので、一般的な MAC アドレスを返すように変更します。

/etc/dhcp/dhclient.conf に下記を追記します。
```
send dhcp-client-identifier = hardware;
```

### 自サイトのドメインをローカルでも使えるように
```
#ローカルネットワークでウェブサーバをドメインで開けるようにします
ip host www.primenon.com 192.168.100.111
```

## 動作確認

* YAMAHA NVR-510 Rev.15.01.18 (Fri Jul 10 10:15:27 2020)
* [IIJmioひかり](https://www.iijmio.jp/imh/)
* NTT東日本
* VDSL 接続

## 構成

* NVR510👉192.168.100.1（デフォルト）
* ルータ下のIP👉192.168.100.2-192.168.100.191
* ウェブサーバのIP👉192.168.100.111

## 未確認

以下の環境での動作は確認していません。
* ひかり電話の接続（筆者はひかり電話を利用していないため）
* 小型 ONU を使用した接続（筆者は VDSL 環境のため）

## 参考文献

* [DS-Lite(transix)でインターネット接続 : コマンド設定](https://network.yamaha.com/setting/router_firewall/ipv6/ds-lite)
* [インターネット接続 : Web GUI設定](https://network.yamaha.com/setting/router_firewall/flets/flets_other_service/internet-gui)


## ライセンス
MIT License
