---
layout: post
title: "React Native Facebook Native Ads 奮鬥戰記"
---

## Native Ads?

文章標題就出現了兩次 Native，實在有點拗口。關於甚麼是 Native Ads（原生廣告） 可以參見 Facebook 的[官方說明](https://developers.facebook.com/docs/audience-network/native-ads)，以下引用之：

> 原生廣告能讓您在為應用程式設計完美的廣告單位時，掌控所有細節。透過「原生廣告 API」，您可決定廣告的外觀、風格、大小和位置。因為廣告的格式由您決定，所以廣告能與應用程式搭配的天衣無縫。

簡單來說就是「**將廣告和你的 App 更好的整合**」，以期能帶來**更多地的點擊**，~~騙~~更多的使用者按下去，比如最常見的就是你大狄卡留言區塊藏著的業配廣告啦，還有 twitter、Facebook 各平臺偽裝成一般貼文廣告等等。

## React Native Integration

React Native 把網頁開發的體驗帶到 App 上，不過 Facebook 沒有提供官方版的 Native Ads 模組，我們只好自行想辦法啦。我們這裡選擇的是 [CallStack](https://callstack.io) 提供的第三方模組：[`react-native-fbads`](https://github.com/callstack-io/react-native-fbads)。

## 花式踩雷

先講結論：

1. 開源專案當覺得 README 寫的不痛快的時候，去看 example code，找出範例比你多寫的地方。
2. 有問題先看 Log。Native Module 的 Log 得用各平臺原生 IDE 看，比如 XCode 或 Android Studio。雖然不想開還是得開一下
3. 噴錯誤訊息時從上游(原生模組的來源)文件查起

### SDK 設定

大多新版 React Native 原生模組的安裝都可以用 `react-native link xxx-module` 搞定，不過有些時候卻沒啥用，跑完指令之後還需要手動設定。

iOS 版 SOP：

* 加入 `node_modules/xxx-module/ios/xxx.xcodeproj` 到 Library
* 把 Product `xxx.a` 加到 Build Phase 裡
* 若有額外引用 header，更新 Header search Path

Android SOP:

* 修改 `settings.gradle`、`build.gradle`、`MainApplication.java` 等
* 檢查 `AndroidManefist.xml` 需不需要修改

大 GUY 是這樣，~~react-native-fbads 的 README 就有些簡單，關於 Android 的寫的也不多，算是一個大坑，AndroidFanefist 的修改還是看他們範例才發現的，不知道到底寫在文件的哪裡，若有人知道希望可以回報下讓我瞭解（感謝）~~。原生如果設定到 Build 成功，而且引入之後沒有噴錯基本上就 OK 了。若還有其它錯誤可以視為上游問題或 Native 層的問題（汗）

**更新：結果就寫在 README 裡，眼殘崩潰，不過還是給我遇到個新坑，參見 [callstack-io#17 (comment)](https://github.com/callstack-io/react-native-fbads/issues/17#issuecomment-269749636)**

### Audience Network 驗證流程

![audience-network-stage](https://lh3.googleusercontent.com/wbd3rYblzS8AnyIt1KPeYJcJW3oKcmk55Fa2K5wZj5d6mIkJ4Ir627vhrE4f7b3ukiim7pI92nZhZADT4C7quX_siaYWwUEOS0xeqsGpkz9k0OBHg9KGp_4xlUx0osVB_HUO4ZBb4sdziMM0bMXP7UgT1WlXx1ysG1XTcN5EHeLdtpBzBq-YUnK3yZ_gSaPJo6CusCaehMkym9FRr4hRBjyfVol4tMwZdzmtALbKMqhaiqfDkYuX8vsEeDp9Og2WhZSdigzVH8Q085LBg2AV0aG3-c5OXna50HavXEiE_T5baB-zPKoWlKiqDgflAM32_Br9DcXGxdHh7FxA6u7yS5I7Zj_Q1w5Ff_6Eo7RufuwkjnTyDxq8Y_PexEU2QhQmYAMjFNLkCQlzxzV6BRtb8oDOFc5c20c1OI2k7gagkVwXeUKfA1RBjihghVrpVMhCSbHiGohKA2ntI8Il6iXqcABQ0MWU90EmXJZaEsO7D9UU0VwC_GFWAw4aYvRQCTw7hLAm9FTQvS4okPGR47OmJpyXrf7l0Qv8VrgCEiigZos6NfmGceeBRkj8xBhv63F7WjGmT_He5DUtk8I6yce6_WRh8XQ0HwKJnkX19EHZqk02oDxzqy9PTw=w801-h353-no)

Audience Network 分成好幾個驗證階段，簡略過程如下：

1. 拿到版位 Placement ID 之後編寫 App 顯示廣告的程式碼
2. 在 Audience Network 新增應用程式
3. 用「原生模擬器」（iOS 就 Simulator，Android 要用 AVD）傳送廣告要求
4. 等待應用程式審核通過

其中第三點「**傳送廣告要求**」是最多雷的，iOS 早早就審核過了，Android 怎麽發要求 Facebook 就是無法接到，最後改用 Android Studio 原生的模擬器就一次 OK，原來是沒法用 Genymotion 的模擬器跑...

最後上 TestFlight 之後在我自己手機上無法顯示，查了一下錯誤代碼 [1001 - No Fill](https://developers.facebook.com/docs/audience-network/testing)，發現原來是手機上沒有安裝 **原生 FB App**，難怪我 Dcard 從來沒看過廣告 XDD，以下是其它不會顯示廣告狀況，從文件引用：

> * Error 1001 - No Fill. May be due to one or more of the following:
> * User not logged into Native Facebook App on Mobile Device
> * Limit Ad Tracking turned on (iOS)
> * Opt out of interest-based ads turned on (Android)
> * No Ad Inventory for current user
> * Your testing device must have the native Facebook application installed.
> * Your application should attempt to make another request after 30 seconds.
