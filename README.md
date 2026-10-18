# jobcontext-study（jobContext のクローラー）

User-Agent に `jobcontext-study` を含むアクセスについての説明です。

## 何をしているか

- 日本の企業が自社サイトや採用管理システム（ATS）で公開している求人情報を集めています。
- 目的は「jobContext」です。求職者が AI アシスタントから求人を探し、比べられるようにするサービスで、個人が開発中の試験版です。
- 表示する求人には、必ず元のページへのリンクと取得日を付けます。応募や確認は元のページで行う前提です。

## アクセスの仕方

- User-Agent：`jobcontext-study/0.1 (+https://github.com/ryoshumei/jobcontext-crawler)`
- 送信元 IP：`35.200.3.59`（Google Cloud 東京リージョン）
- robots.txt に従います（`User-agent: jobcontext-study` または `*` の指定）。
- 間隔：
  - robots.txt に Crawl-delay があれば、それに従います。
  - なければ、同じホストへのリクエストは 1.1 秒以上あけます。
- 429・503 では Retry-After に従います。拒否（403・429）が続いたホストには、それ以上アクセスしません。
- ログイン、フォームの送信、CAPTCHA や WAF の回避は一切しません。

## 保存するもの・しないもの

- 保存するもの：求人ページの本文と項目（職種、勤務地、給与など）、URL、取得日。
- 取り除くもの：ページ内のメールアドレスや電話番号などの連絡先。保存する前に取り除きます。
- 集めないもの：応募者や社員など、個人の情報。

## 止めたい・載せたくないとき

- robots.txt で止められます。

  ```
  User-agent: jobcontext-study
  Disallow: /
  ```

- 求人の掲載停止や削除のご依頼は、このリポジトリの Issue でお知らせください。jobContext での表示を止め、「企業のご依頼により非表示」と記録します。
- アクセスを拒否された場合、それを回避して取得しようとはしません。

## 連絡先

このリポジトリの [Issue](https://github.com/ryoshumei/jobcontext-crawler/issues) へどうぞ。日本語・英語どちらでも構いません。

---

## English

`jobcontext-study` is the crawler of jobContext, an experimental service built by an individual. It helps job seekers find and compare openings that companies in Japan publish on their own career sites and applicant tracking systems. Every posting it shows links to the original page, with the date it was fetched.

- **User-Agent:** `jobcontext-study/0.1 (+https://github.com/ryoshumei/jobcontext-crawler)`
- **Source IP:** `35.200.3.59` (Google Cloud, Tokyo).
- **Rules:**
  - It obeys robots.txt (`jobcontext-study` or `*`).
  - It follows Crawl-delay when one is set; otherwise it waits at least 1.1 s between requests to the same host.
  - It honours Retry-After, and stops visiting a host that keeps refusing it (403/429).
  - No logins, no form submissions, no CAPTCHA or WAF circumvention.
- **What it stores:** posting text and fields, the URL and the fetch date. Contact details (e-mail addresses, phone numbers) are removed before storage. No personal data about individuals.
- **To opt out:** add `User-agent: jobcontext-study` / `Disallow: /` to robots.txt. To have postings removed, open an issue here.
- **Contact:** [issues of this repository](https://github.com/ryoshumei/jobcontext-crawler/issues).
