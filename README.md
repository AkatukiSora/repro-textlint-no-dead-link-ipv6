# repro-textlint-no-dead-link-ipv6

This repository reproduces the issue where textlint-rule-no-dead-link does not fall back to IPv4 when IPv6 connection fails.

## Problem

textlint-rule-no-dead-link fails to fall back to IPv4 when IPv6 connection fails after obtaining an IPv6 address via AAAA record during URL resolution.

This causes false positive errors for URLs that are actually accessible.

```bash
$ pnpm exec textlint target.md

/home/akatuki-sora/git/repro-textlint-no-dead-link-ipv6/target.md
  6:13  error  https://wiki.archlinux.org/title/Main_page is dead. (request to https://wiki.archlinux.org/title/Main_page failed, reason: )  no-dead-link

✖ 1 problem (1 error, 0 warnings, 0 infos)
```

```bash
$ curl -Iv https://wiki.archlinux.org/title/Main_page

* Host wiki.archlinux.org:443 was resolved.
* IPv6: 2a01:4f9:c010:a4eb::1
* IPv4: 135.181.27.174
*   Trying [2a01:4f9:c010:a4eb::1]:443...
* Immediate connect fail for 2a01:4f9:c010:a4eb::1: ネットワークに届きません
*   Trying 135.181.27.174:443...

--- omit ---

* Connected to wiki.archlinux.org (135.181.27.174) port 443
* using HTTP/2

--- omit ---

HTTP/2 200 
server: nginx
date: Mon, 03 Nov 2025 02:53:45 GMT
content-type: text/html; charset=UTF-8
x-content-type-options: nosniff
content-language: en
accept-ch: 
vary: Accept-Encoding, Cookie
cache-control: s-maxage=18000, must-revalidate, max-age=0
last-modified: Sun, 02 Nov 2025 19:36:24 GMT
x-request-id: xxxxxxxxxxxxxxxxxxxxxxxx
x-cache: HIT
strict-transport-security: max-age=31536000; includeSubdomains; preload
alt-svc: h3=":443"; ma=3600
* Connection #0 to host wiki.archlinux.org left intact
```

## Installation

```bash
pnpm install --frozen-lockfile
```

## Usage

Configure your environment so that IPv6 connection fails (e.g., disable IPv6), then run:

```bash
pnpm exec textlint target.md
```

You will see a no-dead-link error even though the URL is actually accessible.

### Expected Behavior

When IPv6 connection fails, it should fall back to IPv4 and be able to access the URL.

## JP

このリポジトリは、textlint-rule-no-dead-linkがIPv6接続失敗時にIPv4へフォールバックしない問題を再現します。

### 問題

textlint-rule-no-dead-linkは、URLを解決する際にAAAAレコードによってIPv6アドレスを取得した場合、IPv6での接続が失敗するとIPv4へフォールバックせずにエラーになります。

これにより、実際にはアクセス可能なURLに対してno-dead-linkエラーが発生します。

```bash
$ pnpm exec textlint target.md

/home/akatuki-sora/git/repro-textlint-no-dead-link-ipv6/target.md
  6:13  error  https://wiki.archlinux.org/title/Main_page is dead. (fetch failed)  no-dead-link

✖ 1 problem (1 error, 0 warnings, 0 infos)

```

```bash
$ curl -Iv https://wiki.archlinux.org/title/Main_page

* Host wiki.archlinux.org:443 was resolved.
* IPv6: 2a01:4f9:c010:a4eb::1
* IPv4: 135.181.27.174
*   Trying [2a01:4f9:c010:a4eb::1]:443...
* Immediate connect fail for 2a01:4f9:c010:a4eb::1: ネットワークに届きません
*   Trying 135.181.27.174:443...

--- omit ---

* Connected to wiki.archlinux.org (135.181.27.174) port 443
* using HTTP/2

--- omit ---

HTTP/2 200 
server: nginx
date: Mon, 03 Nov 2025 02:53:45 GMT
content-type: text/html; charset=UTF-8
x-content-type-options: nosniff
content-language: en
accept-ch: 
vary: Accept-Encoding, Cookie
cache-control: s-maxage=18000, must-revalidate, max-age=0
last-modified: Sun, 02 Nov 2025 19:36:24 GMT
x-request-id: xxxxxxxxxxxxxxxxxxxxxxxx
x-cache: HIT
strict-transport-security: max-age=31536000; includeSubdomains; preload
alt-svc: h3=":443"; ma=3600
* Connection #0 to host wiki.archlinux.org left intact
```

### インストール

```bash
pnpm install --frozen-lockfile
```

### 使い方

IPv6接続が失敗するように環境を設定し(例: IPv6を無効化)、次のコマンドを実行します:

```bash
pnpm exec textlint target.md
```

実際にはアクセスできるURLにもかかわらず、no-dead-linkエラーが発生することを確認できます。

### 期待される動作

IPv6接続が失敗した場合に、IPv4へフォールバックしてURLにアクセスできること。
