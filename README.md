# yamaha-router-config

YAMAHA NVR510 で、IPoE(DS-Lite)と PPPoE を併用してインターネットに接続します。さらに、サーバを設置して、外部に公開します。  
サーバは、ウェブサーバとマインクラフトサーバです。

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
ウェブサーバを見られるようにします（http/https両方）
```
#www,httpsという名前で設定可能です
ip filter 210001 pass * 192.168.100.111 tcp * www,https
#ポート番号でも指定可能です、複数のポート番号はカンマで区切ります
ip filter 210001 pass * 192.168.100.111 tcp * 80,443
```
マインクラフトサーバを見られるようにします
```
ip filter 210002 pass * 192.168.100.111 tcp * 25565
```

### マスカレード
ウェブサーバを見られるようにします（http/https両方）
```
#www,httpsという名前で設定可能です
nat descriptor masquerade static 1000 1 192.168.100.111 tcp www,https
#ポート番号でも指定可能です、複数のポート番号はカンマで区切ります
nat descriptor masquerade static 1000 1 192.168.100.111 tcp 80,443
```
マインクラフトサーバを見られるようにします
```
nat descriptor masquerade static 1000 2 192.168.100.111 tcp 25565
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

### 自サイトのドメインをローカルで使えるように
```
#ローカルネットワークでサーバをドメイン名で開けるようにします
ip host www.primenon.com 192.168.100.111
```

### DHCPのリース期間を24時間似変更
デフォルトだと標準リース時間・最大リース時間がどちらも72時間になっています。  
`WANのエラーカウンタがカウントアップしています。`という警告が出ることがあったので、家庭での利用ということもあり24時間に変更してます。

```
dhcp scope 1 192.168.100.2-192.168.100.191/24 expire 24:00 maxexpire 24:00
```

## 動作確認

* YAMAHA NVR-510 Rev.15.01.18 (Fri Jul 10 10:15:27 2020)
* [IIJmioひかり](https://www.iijmio.jp/imh/)
* NTT東日本
* VDSL 接続

## 構成

* __NVR510__ 192.168.100.1（デフォルト）
* __ルータ下のIP__ 192.168.100.2-192.168.100.191
* __サーバのIP__ 192.168.100.111

## 未確認

以下の環境での動作は確認していません。
* ひかり電話の接続（筆者はひかり電話を利用していないため）
* 小型 ONU を使用した接続（筆者は VDSL 環境のため）

## 参考文献

* [DS-Lite(transix)でインターネット接続 : コマンド設定](https://network.yamaha.com/setting/router_firewall/ipv6/ds-lite)
* [インターネット接続 : Web GUI設定](https://network.yamaha.com/setting/router_firewall/flets/flets_other_service/internet-gui)


## ライセンス
MIT License
