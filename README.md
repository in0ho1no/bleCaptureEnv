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

現在はTexas Instruments(TI (テキサス・インスツルメンツ))社のUSBドングルを利用している。

https://www.ti.com/tool/ja-jp/CC2540EMK-USB

![cc2540 image](img/image.png)

このドングルは専用のツールを利用するため、比較的直観的に利用しやすいのがメリットである。

![alt text](img/image-1.png)

その反面、以下のようなデメリットが存在する。

- 専用ツール以外でキャプチャできない(バイナリを解析すれば他ツールで表示確認することもできるが。)
- キャプチャ中にツールが落ちることがある(何らかの閾値を超過していると思われる)
- キャプチャ中にログ表示が止まることがある(intervalの問題と思われる)
- パケットが著しく欠損することがある(USBドングル周辺の環境に依存している可能性が高い)
