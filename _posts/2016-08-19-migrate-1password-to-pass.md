---
layout: post
title: 從 1Password 搬家到 pass
comments: true
published: true
---

## Why?

引用[官網][1]的說法，pass 是一套 「標準 unix 密碼管理工具」（the standard unix password manager），簡單好用。雖然 1Password 我已經設定用 Dropbox 同步，應該還算安全，不過多個開源選擇也好。

pass 也有社群版套件，在手機上也可以用，引用一下官網：

> The community has even produced a [cross-platform GUI client](http://qtpass.org/), an [Android app](https://github.com/zeapo/Android-Password-Store), an [iOS app](https://github.com/davidjb/pass-ios#readme), a [Firefox plugin](https://github.com/jvenant/passff#readme), a [Windows client](https://github.com/mbos/Pass4Win), a pretty [Python QML app](https://github.com/TheLastProject/Pext), a nice [Go GUI app](https://github.com/cortex/gopass),  an [interactive console UI](https://github.com/Kwpolska/upass), Alfred integration [(1)](https://github.com/CGenie/alfred-pass) [(2)](https://github.com/MatthewWest/pass-alfred) [(3)](https://github.com/johanthoren/simple-pass-alfred), a [dmenu script](https://git.zx2c4.com/password-store/tree/contrib/dmenu), [OS X integration](https://git.zx2c4.com/password-store/tree/contrib/pass.applescript), [git credential integration](https://github.com/languitar/pass-git-helper), and even an [emacs package](https://git.zx2c4.com/password-store/tree/contrib/emacs).

## 安裝 pass

在 macOS 上十分容易，先裝好 Homebrew，然後：

```bash
brew install pass
```

pass 也提供 shell completion 可以用，bash 預設就會裝好了，`fish` 需要在 `~/.config/fish/config.fish` 裡加上：

```bash
source /usr/local/share/fish/vendor_completions.d/pass.fish
```

## 設定 pass

完成之後我們要看一下有沒有 gpg 金鑰，如果沒有的話要新增個

```bash
gpg --gen-key # 產生 gpg key
```

記住產生時輸入的 key phrase，每次解鎖密碼都會用到（就跟 1Password 一樣）。用 `gpg --list-keys` 查看剛剛產生的 key，輸出像這樣：

```bash
/Users/username/.gnupg/pubring.gpg
-------------------------------
pub   2048R/A534B400 2016-08-18
uid                  My Name <your_email@gmail.com>
sub   2048R/E1001945 2016-08-18
```

public id 就是 `A534B400`，拿這組 id 來初始化 pass

```bash
pass init A534B400
```

完成！之後就可以用 pass 指令管理密碼了，可以下一些測試的指令試試，比如 insert 一組 foo 的密碼：

```bash
pass insert foo
```

然後顯示 foo 的密碼

```bash
pass show foo
```

## 由 1Password 匯入

官網上就寫上匯入 1password 的 [ruby script][2] 😍 ，直接使用就行了。在 1Password 匯出 txt 時，記得勾選 `Include Column Labels`，如下圖：

![1password-export-option](https://i.imgur.com/YsoUQcv.png)

把匯出的檔案和匯入腳本準備好，跑一下：

```
ruby 1password2pass.rb /path/to/1password_exported.txt
```

密碼就通通匯入至 `~/.password-store` 底下囉~

## 基礎使用

pass 的管理可以說是相當自由，通通放在 `~/.password-store` 目錄，底下可以建立任意的目錄分類，或是乾脆不分 XD

一些基本指令：

```bash
pass show PROFILE # 印出 PROFILE 的密碼
pass -c PROFILE # 複製 PROFILE 的密碼到剪貼簿，如果不想印到 terminal
pass edit PROFILE # 編輯密碼
pass ls # 列出所有密碼設定檔
```

我做了以下 alias，方便快速密碼搜尋：

```bash
alias passgrep="pass ls | grep -i"
```

就可以用 `passgrep` 來搜尋現有設定檔了。

## Pass for iOS

![pass](https://i.imgur.com/tWUOkAo.png)

pass 也有 iOS 的客端軟體可以用，還不用 jailbreak Q_Q 照著 [wiki](https://github.com/mssun/passforios/wiki#quick-start-guide-for-pass-for-ios) 設定就可以了，我自己在 GitHub 開一個 private repo，然後設定 ssh key，再由手機 pull 下來。下面秀一些截圖：

![passforios-1](https://lh3.googleusercontent.com/yttCY9Onc6P_z-1O5m5g6LQI2AtXUn8-p9SeoYDLJgbziTBvBgKGXA1Q1EaUM-MwG16QhAfgIli0DTy2HvT6JGutPvd-7g1lI1tn5RB60PA0dwIxAFYHhS4L3RsJ3JV1hyWp-adEaNLPj8M5Ja9h1AIU-lZ1QkYLZM9bCBCORGIPAspD2f3a7mtJ3-PyVE4KyqwXxn_9CIMZQprAiz8Msi9aSJ_IJZEnBIFq2JAo_H7hejKK2NASCUDET2eHiuV9U5e4bf90-OG53ODs3jJ4WiHbgeNuGqqynJ9YYgqSPmdtNhsrYcwNrXKsvtY_H80UPmvvTbkKj1Obwg93hQBRLFWQU08CdZY7Q0jrtRSK8qC3LCjdfMx_6_-yAnklym5J5jGyKEPAtHZ-yPYYwibmg-9LCFQtr_2edv9740DJO1CcrxV0ufhajkqB2rSd2eAJSwH6LjwfHe6Y4INpGPiYX69odirAjFQ6B-BzYGsGyjgNOPg3A8l2XqH5psHZ1oIZUmYAAmhR_Nlv44-JZDpdTE9KayjKeMTPe8ZBWd6-fQq8QRjanX48QVzVsOmFrONOaYQO7ni1O0yCYV6E6hBfGnZTcxP_TEXf9qISvM9O5BRQ5SnMOdUfBuoGlA=w438-h776-no)

![passforios-2](https://lh3.googleusercontent.com/lpSzfHVslm8B57tYU_Vwu0t3cMAn6kR5M4cTItZ0H3gM-BaC3VLRbwOeL7iZO9OfG6fDU0w5pATbq5_bm2JaMyDqVDDcyNdVuYxk_nvcjQL1EHweu7brbXXQaHPe26wGi4MvuT7UPrIbPtCFyyHXv2bONJcOALLuCqGuQJeJRJrEe2FM-1HHlf7Ox2zuUYew58r1PHpyEjD0WoYP8nvHPxMLCC5V_Bp470xL2jP3ikD6FCtU3O2_oDIzFd-4BHVCkPlwGKLLzYN-aXwJ47PIqSDspunUIfGDEcTIYPa2scAZemR0bLo6F6irP27_tEAfACshnKqBFsYJ0ZMii1YdPv27End-hjEqXCO5P8Ot2D_c-Hjfh6Gtq1B6tWU8Ka3DMcyuKKuY8YfCcuPvBYuP5vx3vfenhOWljDuiKvmyxUykWcWqJ9-dMVu4pqQM22wWs82h8k44agkPqIbn7nxK3SZLY7p65B2iGjIGuIv8f2-Ehepnu4-_8i4A_dGcMWc2VGAriJRUSzfzC7-N8dTGdUcK1RcMhv8AceRQKNZCShy4ny9lGlFmsZ0sgIxo2U_ig9abcUX3fPGguYrmL8p0SrQu1_w5ZEbrj2Vhd8ENw85KyizOpWhak8kO9A=w438-h776-no)

![passforios-3](https://lh3.googleusercontent.com/ROHUYtj3ggs3qHPSOCL9emx0zanp1mYoRCJVyxKfRjobO8JsORfn_bFXkVPNuswQ1EnQYnPgM04u8Eo87OFnL68RKQD7kGUo7I0BOO1ogewTxTnK2VcGaqr-p4LlOR3KlnAqDWakcuYr-gE6nsPU7po-mMQY5SCoGPy9cRjlA33Nee8orgUt_VbY2YO99f9BQIgVbfsc3_uVYT0F7tbtyAOQj7A1LSitOTgKiJygyLQPJQjKMsVaH0gg9L0xCvgw8sJjv5jVi15OK0flvqzH5mivR1ntbUIqqKNlYk4Xstcv6x1kJQp1g49nkayRylZznwPSn0aFn4-XsSWI3Oanesjd5hjmJEzj3aWKkUzaqjJVyx0dScMJfU8fLiRFOzjgbedoGvy7a8Tdo45MW6BQPFjn-La4ypf1P-cd2GbWdaozX9Yb5_ynUCAKhfNdDiwuEBpt0EbolBUQF8FU1ELWsZZx82MUvdWlNlazH3p503JowOt3ZgxWdG5T0YQWliJgz2Jo_tLvHez0gXSevu9ZufEPPJwGpgXjlB5MiOjTDa9Rki4dvPmpeQHJzCzoaWjWbrs88SOLnwGWNggwTrHDcZlEKDdTiv9vn8W69VtaWrV5Ly11V3231rFjhw=w438-h776-no)

用起來就跟原本 1Password 有 87% 像啦 XD

（完）

[1]: https://www.passwordstore.org/
[2]: https://git.zx2c4.com/password-store/tree/contrib/importers/1password2pass.rb
