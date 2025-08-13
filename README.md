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

![alt text](img/image-1.png)

その反面、以下のようなデメリットが存在する。

- 専用ツール以外でキャプチャできない
- キャプチャ中にツールが落ちることがある
- キャプチャ中にログ表示が止まることがある
- パケットが著しく欠損することがある

従って、代替となるドングルを検討・用意する。
