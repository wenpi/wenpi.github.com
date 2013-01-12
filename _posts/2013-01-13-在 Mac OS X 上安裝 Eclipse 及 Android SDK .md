---
layout: post
title: 在 Mac OS X 上安裝 Eclipse 及 Android SDK
tags: 日记
comment: true
published: true
description: 
---
在 Mac OS X 上安裝 Eclipse 及 Android SDK

為了開發 Android 項目，需要在我的 MacBook Air 內安裝 Eclipse。之前已經在公司的 iMac 上安裝好，但安裝過程已經不想記起了。跟 http://developer.android.com/sdk/installing.html 再做一次。

1. 到 http://www.eclipse.org/downloads/ 下載 Eclipse Classics 3.7.1
2. 到 http://developer.android.com/sdk/index.html 下載 Android SDK
3. 解壓 Eclipse 並拷到 Applications 內
4. 解壓 Android SDK 到 Eclipse 目錄下
5. 啟動 Eclipse。選 Help 中的 Install New Software，按 Add
6. 在 Name 格輸入 ADT Plugin
7. 在 Location 格輸入 https://dl-ssl.google.com/android/eclipse/

8. 按 OK
9. 點選 Developer Tools 內所有插件後點 Next
10. 按畫面指示接受所有條款
11. 安裝完成後重啟 Eclipse
12. 在 Android SDK 畫面選剛才安裝 SDK 的目錄

13. 選 Applications - eclipse - android-sdk-macosx - tools - android

14. Android SDK Manager 會自動選擇需要安裝的元件
15. 安裝所有元件