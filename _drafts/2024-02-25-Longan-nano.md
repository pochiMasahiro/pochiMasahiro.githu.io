---
layout: post
title: 
date: 2024-02-25 19:18
category: 
author: 
tags: []
summary: 
---

# Longan Nano を遊ぶ

Longan NanoというRISC-VなSTM32っぽいMCUがある。正確にはGD32VなんちゃらってMCUで、Longan nanoはちっこいフルカラー液晶が付いた開発用ボードみたいなやつ。

でもこれすっごい安い。秋月でたったの800円!!!

液晶付いてるのにだよ!?

ただもう作ってないのかも…

そんなことも無いのかも。

なんだかよくわかんにゃい。

とりあえず追加で10台くらい買っておきたいところ。

ただ、もう飽きられたのかインターネッツの情報は古いし、開発元のSipeedの情報も古くて役に立たんこと多し。

それに、個人的にはArduino苦手なので、Arduinoを使わない開発環境がほしい。

そんなわけで、公式なのかはわからないけど、なんか結構整った開発環境であるNuclei Studioがあるので、それを使った開発方法をメモとして記します。

ちなみにここでは3社出てきます。

- Sipeed： Longan Nanoの設計・製造元。他にもいろんなマイコンボードだったり、FPGAボードだったり作ってるみたい。興味深い製品多し。
- GigaDevice： Longan Nanoに乗ってるマイコンGD32VGD32VF103CBT6の開発・製造元。一応Arm系、Cortexのマイコンもあるみたい。STM32系と同じピン配置のマイコン作ってるっぽい。型番がそれっぽい笑。
- Nuclei System： 今回使う開発環境Nuclei Studio の開発元。RISC-Vの開発環境を作ってるみたい。初めて知った。Nuclei-SDKにはGigaDeviceのが多いから、そういうことなのかな。

# 開発環境の準備

インターネットにあふれるplatformioを使ったやつは使えないことはないけど…だいぶきついと思います。自分の環境が特殊なのもあるのですが、普段の開発環境をWSL上に組んでいるので、platformioとの相性がすこぶる悪すぎました。また、Sipeed公式ドキュメントの手順ではもう動かないと思います。そんなこんな1週間くらいすったもんだしてたのですが、諦めてNuclei Studioというのを導入しました。快適すぎる。

## Nuclei Studioのダウンロード

[Nuclei Toolchain](https://www.nucleisys.com/download.php) にアクセスします。この中でNuclei Studioをダウンロードしてきます。自分の環境に合わせてダウンロードしてください。Zipになっているので、適当な場所で解凍します。

解凍したフォルダの中にNuclei Studio.exeがあるので、それを起動します。特にインストールは要らないらしい。

Workspaceは適当な場所にどうぞ。とりあえず日本語入っていても大丈夫そうでした。中身Eclipseだしね。

まだ、SDKが入っていないので、SDKをインストールします。

メニューバー > RV-Tools > NPK Package Managementを開きます。

Software Development Kit > nuclei > skd-nuclei_SDK からお好きなバージョンのSDKをDownloadしてください。とりあえずは最新版でいいんじゃないでしょうか?

## プロジェクトの作成

プロジェクトの作成は File > New > New Nuclei RISC-V C/C++ Projectをクリックしてください。

今回はLongan nanoなので、Giga Device > Sipeed Longan　Nano Board sdk-nuclei_sdk @0.5.0を選択します。

Project Exampleが必ず必要みたいなので、Simple Helloworld Demoが一番シンプルな感じでいいと思います。

## HALのドキュメントが無い

HALのドキュメントが見つけられていません。ボードのドキュメントもありません。

とりあえずSDKのソースを漁っていくしかなさそうです。

今後に期待かな。

とりあえずLEDがゲーミングになるコードを次に上げておきます。

適当にビルドしてHexファイルできていたら多分大丈夫です。

```main.c

#include "nuclei_sdk_soc.h"
#include "gd32vf103c_longan_nano.h"

int main(void)
{
    gd_led_init(LED_G);
    gd_led_init(LED_B);
    gd_led_init(LED_R);
    gd_led_off(LED_G);
    gd_led_off(LED_B);
    gd_led_off(LED_R);
    while (1)
    {
    	// G
        gd_led_on(LED_G);
        delay_1ms(100);
        gd_led_off(LED_G);
        delay_1ms(100);

        // B
        gd_led_on(LED_B);
        delay_1ms(100);
        gd_led_off(LED_B);
        delay_1ms(100);

        // R
        gd_led_on(LED_R);
        delay_1ms(100);
        gd_led_off(LED_R);
        delay_1ms(100);

        // G+B
        gd_led_on(LED_G);
        gd_led_on(LED_B);
        delay_1ms(100);
        gd_led_off(LED_G);
        gd_led_off(LED_B);
        delay_1ms(100);

        // G+R
        gd_led_on(LED_G);
        gd_led_on(LED_R);
        delay_1ms(100);
        gd_led_off(LED_G);
        gd_led_off(LED_R);
        delay_1ms(100);

        // R+B
        gd_led_on(LED_R);
        gd_led_on(LED_B);
        delay_1ms(100);
        gd_led_off(LED_R);
        gd_led_off(LED_B);
        delay_1ms(100);

        // R+G+B
        // G+B
        gd_led_on(LED_G);
        gd_led_on(LED_B);
        gd_led_on(LED_R);
        delay_1ms(100);
        gd_led_off(LED_G);
        gd_led_off(LED_B);
        gd_led_off(LED_R);
        delay_1ms(100);


    }
    return 0;
}

```

## マイコンへのダウンロード

マイコンへのダウンロードは今回はDFUというのを使います。よくわかってません。そのうちデバッガー経由にしたいです。

リンク切れ著しく謎が謎を呼ぶ世界になっているのですが、ディレクトリトラーバスで掘ったDFUツールをダウンロードできるリンクが以下になります。

[下载站 - Sipeed](https://dl.sipeed.com/shareURL/others/Longan_nano/Nano/Tools)

この中の`GD32_MCU_Dfu_Tool_V3.8.1.5784_1.rar`をダウンロードして解答してください。

この中にはドライバーも入っています。インストールツールがあるので、管理者モードで起動してドライバーをインストールしておきましょう。

`GD32 MCU Dfu Tool`を起動します。その状態でUSBケーブルを使ってPCとLongan nanoを接続します。多分Dfuツールからはまだ何も見えません。その状態で先にDownload to Device側にHexファイルをセットしておきます。

その状態でLongan nanoボードの`BOOT0ボタン`を押しながら、`RESETボタン`を押します。その状態で2～3秒維持します。その後`RESETボタン`、`BOOT0ボタン`の順にボタンから指を離します。

そうするとDfuツール側にLongan nanoボードが認識されると思います。

その状態で`OK`をクリックするとプログラムのダウンロードが始まります。プログレスバーが100%まで到達すれば終わりです。

ボードには何も起きていないと思います。`RESETボタン`を押すと、LEDがカラフルにチカチカしだすと思います。こうなったら成功です!!

Longan Nanoのプログラムができました!!

次は液晶を扱えればなぁ…と思います。その前にタイマーとかのペリフェラルを試そうかなぁ…。

割り込みを試してみようかな。そっちのほうが大事そう。

まぁ、そんな感じです。
