---
html:
  embed_local_images: true
  embed_svg: true
  offline: true
  toc: true
export_on_save:
  html: true
---

# BLE Capture Environment

## 利用するドングル

### 現在利用中のドングル

現在はTexas Instruments(TI (テキサス・インスツルメンツ))社の
[USBドングル](https://www.ti.com/tool/ja-jp/CC2540EMK-USB)
を利用している。

![cc2540 image](img/image.png)

このドングルは専用のツールを利用するため、比較的直観的に利用しやすいのがメリットである。

![packet sniffer image](img/image-1.png)

その反面、以下のようなデメリットが存在する。

- 専用ツール以外でキャプチャできない
- キャプチャ中にツールが落ちることがある
- キャプチャ中にログ表示が止まることがある
- パケットが著しく欠損することがある

従って、代替となるドングルを検討・用意する。

### 代替利用するドングル

nordicのnRF52840を搭載したドングルを利用する。  
具体的にはseeed XIAO-nrf52840で技適も通っている。  

### ドングルのFW書き換え

2025年7月頃までのネット記事で見かける手順は利用できなくっている(リンク先にアクセスできなくなっている)ので、自前で見つけた手順をとる。  

#### nRF Utilの準備

1. まず以下ページから`nRF Util`というツールのexeを入手する。  
  [https://www.nordicsemi.com/Products/Development-tools/nRF-Util/Download?lang=en#infotabs](https://www.nordicsemi.com/Products/Development-tools/nRF-Util/Download?lang=en#infotabs)
1. ダウンロードしたファイルをダブルクリックでインストールする。  
1. exeファイルを適当なフォルダへ配置してパスを通す。  
  例: "C:\tools\nordic\nrfutil.exe"  
1. [Programming the nRF Sniffer firmware](https://docs.nordicsemi.com/bundle/nrfutil/page/nrfutil-ble-sniffer/guides/programming_firmware.html#programming-sniffer-firmware-using-nrfutil-device)で紹介されている以下コマンドを利用して、デバイスのシリアルナンバーやCOMポートを確認できるようにする。  
  nrfutil install device

1. 以下コマンドでデバイスリストを確認できる

        PS C:\Users\kome> nrfutil device list
        WARNING: JLinkARM DLL not found. Devices that require J-Link will not be recognized correctly, and J-Link operations will not be available. Install SEGGER J-Link from https://www.segger.com/downloads/jlink/. Currently tested version: JLink_V8.66.

        1234567890123456
        Product         nRF Sniffer for Bluetooth LE
        Ports           COM5
        Traits          usb, serialPorts, nordicUsb

        Supported devices found: 1

        PS C:\Users\kome>

1. [Getting the sniffer firmware](https://docs.nordicsemi.com/bundle/nrfutil/page/nrfutil-ble-sniffer/nrfutil-ble-sniffer_0.16.0.html#getting-the-sniffer-firmware)より、FWを入手する。  

        nrfutil install ble-sniffer

1. すると以下パスへFWが配置される。  
  C:\Users\kome\\.nrfutil\share\nrfutil-ble-sniffer

#### python環境の準備

#### uf2の準備

binファイルからuf2ファイルを作成する

1. 事前手順で配置されているはずの以下圧縮ファイルを解凍しておく  
  "C:\Users\kome\\.nrfutil\share\nrfutil-ble-sniffer\firmware\sniffer_nrf52840dongle_nrf52840_4.1.1.zip"
1. uf2の[githubページ](https://github.com/microsoft/uf2)からプロジェクト全体をクローンしておく  
1. 端末(CMDやWindowsTerminalなど)からプロジェクトを開き、uf2conv.pyの配置されているフォルダをカレントディレクトリとしておく
1. [Adafruit nRF52 Bootloader](https://github.com/adafruit/Adafruit_nRF52_Bootloader?tab=readme-ov-file#making-your-own-uf2)のコマンドを参考にする。  

        nRF52840
        uf2conv.py firmware.bin -c -b 0x26000 -f 0xADA52840

1. 今回の手順では以下コマンドを実行することになる  

        python uf2conv.py "C:\Users\kome\.nrfutil\share\nrfutil-ble-sniffer\firmware\sniffer_nrf52840dongle_nrf52840_4.1.1\sniffer_nrf52840dongle_nrf52840_4.1.1.bin" -c -b 0x26000 -f 0xADA52840

#### 作成したFWを書き込む

1. seeed XIAO-nrf52840をPCに接続する。  
1. RSTボタン(Type-Cコネクタの左側)を素早く2回押す。  
1. `XIAO-SENSE`というデバイス名でPC上にウィンドウが表示されるか、ファイルエクスプローラーからアクセスできるようになる。  
1. `XIAO-SENSE`を表示したウィンドウに、作成したuf2ファイルをドラッグアンドドロップする。  

## Wiresharkでの解析

### 代表的なフィルタ

Device欄でGATT通信を補足したいアドレスを指定する。

目的 | フィルタ | 補足
--- | --- | ---
チャネルでフィルタリングする | nordic_ble.channel | 例: 37
アドバタイズパケットをアドレスでフィルタリングする | btle.advertising_address | none
pdu_typeでフィルタリングする | btle.advertising_header.pdu_type | 例: 0x05: ADV_CONNECT_REQ
