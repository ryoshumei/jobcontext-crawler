# jobContext 公開ベータ

jobContext は、日本で仕事を探す人に、企業の事実と求人の情報を届ける MCP（AI アシスタントが外部のサービスにつながるための仕組み）サーバーです。Claude などの AI アシスタントから接続して使います。個人が運営する公開ベータ版です。現在、会員登録は要らず、無料で使えます。

本サービスに接続して使った時点で、[利用規約・プライバシーポリシー](https://github.com/ryoshumei/jobcontext-crawler/blob/main/TERMS.md)（[出典表示](https://github.com/ryoshumei/jobcontext-crawler/blob/main/SOURCES.md)を含みます）を契約の内容とすることに同意したものとみなします。使う前にお読みください。お問い合わせは[フォーム](https://docs.google.com/forms/d/e/1FAIpQLScM4FLqRFKj7jgp7uOGodR2xHrExO5YKKJGMgsX5k_vVEhrTQ/viewform)（ログイン不要）へどうぞ。

接続先の公開アドレス：

```text
https://beta.jobcontext.app/mcp
```

- Claude からつなぐ：「カスタマイズ > コネクタ」で「+ 追加」→「カスタムコネクタを追加」を選びます。名前（例：jobContext）と公開アドレスを入れ、認証は「サインインなし」を選んで「追加」します。手順の詳細は、[Anthropic のヘルプ](https://support.claude.com/ja/articles/11175166)にあります。
- ChatGPT から使う場合は、OpenAI の説明に従ってください。運営者が動作を確かめているのは Claude だけです。つながるかどうか、どの機能を使えるかは、AI アシスタントの種類やプランによって異なります。

クローラー（jobcontext-study）についての説明は、この下にあります。

---

# jobcontext-study（jobContext のクローラー）

User-Agent に `jobcontext-study` を含むアクセスについての説明です。

## 何をしているか

- 日本の企業が自社サイトや採用管理システム（ATS）で公開している求人情報を集めています。
- 集めた求人は、「jobContext」で提供します。jobContext は、求職者が AI アシスタントを通じて求人を探し、比べられるようにするサービスで、個人が運営する公開ベータ版です。利用規約とプライバシーポリシーは<https://github.com/ryoshumei/jobcontext-crawler/blob/main/TERMS.md>にあります。
- 求人を探すために、採用ページや、採用管理システムが一般に公開している求人の情報（ログインやキーの要らないもの）を読みます。
- 求人を更新するため、各社の採用ページを繰り返し読み直します。巡回の間隔は決めていません。
- jobContext が表示する求人には、元のページへのリンクと取得日（元のページを最後に読んだ日）を付けます。応募や確認は元のページで行う前提です。

## アクセスの仕方

- User-Agent：`jobcontext-study/0.1 (+https://github.com/ryoshumei/jobcontext-crawler)`
- 現在の送信元 IP：`35.200.3.59`（Google Cloud 東京リージョン）。jobcontext-study を名乗っていても、ほかの IP からのアクセスは jobContext のものではありません。
- robots.txt に従います。`User-agent: jobcontext-study` のグループがあればそれに、なければ `User-agent: *` のグループに従います（RFC 9309）。
- robots.txt が読めないときは、そのサイトから何も取得しません。robots.txt がないときは、制限がないものとして扱います。
- 同じサイトや採用管理システムへのリクエストは、間隔をあけて行います。robots.txt に Crawl-delay があれば、それに従います。
- 429・503 では Retry-After に従って待ちます。
- 拒否されたと判断したホストには、原則として以後アクセスしません。
- ログイン、フォームの送信、CAPTCHA や WAF の回避は一切しません。

## AI クローラー向けの指定について

- jobcontext-study は、robots.txt のうち `jobcontext-study` と `*` のグループだけを、自分への指定として読みます。GPTBot、ClaudeBot など、ほかのクローラーだけを対象にした禁止の指定は、jobcontext-study には当てはまらないものとして扱います。
- そうしたサイトの求人も、jobContext は、ほかの求人と同じように提供しています。jobContext による取得を止めたい場合は、`User-agent: jobcontext-study` を指定してください（下の「止めたい・載せたくないとき」を参照）。

## 保存するもの・しないもの

- 保存するもの：求人ページの本文と項目（職種、勤務地、給与など）、URL、取得日。
- 取り除くもの：ページ内のメールアドレス、電話番号、担当者名などの連絡先。保存する前に機械的に取り除きます。完全ではないため、残っているのを見つけたら[お問い合わせフォーム](https://docs.google.com/forms/d/e/1FAIpQLScM4FLqRFKj7jgp7uOGodR2xHrExO5YKKJGMgsX5k_vVEhrTQ/viewform)からお知らせください（公開される GitHub の Issue には、個人の情報を書かないでください）。
- 集めないもの：応募者や社員など、個人の情報。

## jobContext が利用者に返すもの

- 現在の公開版の jobContext は、求人から読み取った項目、短い引用（要件や条件が書かれた行、検索結果の抜粋）、元のページへのリンクと取得日などを返しています。求人の全文は返していません。

## 止めたい・載せたくないとき

- robots.txt で止められます。

  ```
  User-agent: jobcontext-study
  Disallow: /
  ```

  次の巡回からは取得せず、それまでに表示していた求人も、表示を止めます。
- 求人の掲載停止のご依頼は、[お問い合わせフォーム](https://docs.google.com/forms/d/e/1FAIpQLScM4FLqRFKj7jgp7uOGodR2xHrExO5YKKJGMgsX5k_vVEhrTQ/viewform)（ログイン不要）か、このリポジトリの Issue（公開されます。個人の情報は書かず、お問い合わせフォームをお使いください）でお知らせください。jobContext での表示を止めます。表示を止めても、巡回は続くことがあります。アクセスそのものを止めたい場合は、robots.txt で止めてください。
- アクセスを拒否された場合、それを回避して取得しようとはしません。そのサイトの求人は表示を止めます。

## 連絡先

[お問い合わせフォーム](https://docs.google.com/forms/d/e/1FAIpQLScM4FLqRFKj7jgp7uOGodR2xHrExO5YKKJGMgsX5k_vVEhrTQ/viewform)（Google フォーム、ログイン不要）か、このリポジトリの [Issue](https://github.com/ryoshumei/jobcontext-crawler/issues) へどうぞ。日本語・英語どちらでも構いません。Issue は公開されます。個人の情報は、お問い合わせフォームにお書きください。

---

## English

`jobcontext-study` is the crawler of jobContext, a public beta service run by an individual. It helps job seekers find and compare, through their AI assistants, the openings that companies in Japan publish on their own career sites and applicant tracking systems. The postings jobContext shows carry a link to the original page and the day that page was last read. Terms and privacy policy: <https://github.com/ryoshumei/jobcontext-crawler/blob/main/TERMS.md>.

- **User-Agent:** `jobcontext-study/0.1 (+https://github.com/ryoshumei/jobcontext-crawler)`
- **Current source IP:** `35.200.3.59` (Google Cloud, Tokyo). A request from any other address is not from jobContext, even when it calls itself `jobcontext-study`.
- **How often:** it reads each company's careers pages again from time to time to keep the postings current, with no fixed interval. It reads careers pages and the job information that applicant tracking systems make publicly available (the kind that needs no login or key).
- **Rules:**
  - It obeys robots.txt: the group for `jobcontext-study`, or the `*` group when there is none (RFC 9309). If robots.txt cannot be read, it fetches nothing from the site; a missing robots.txt means no rules.
  - It leaves a gap between requests to the same site or system, and follows a Crawl-delay when one is set.
  - It honours Retry-After on 429/503. As a rule, a host that refuses it is not visited again.
  - No logins, no form submissions, no CAPTCHA or WAF circumvention.
- **Rules for AI crawlers:** a robots.txt rule that names only other crawlers (GPTBot, ClaudeBot, ...) is not read as a rule for `jobcontext-study`, and jobContext serves the postings of such sites like any other. To stop it, name `jobcontext-study`.
- **What it stores:** posting text and fields, the URL and the fetch date. Contact details (e-mail addresses, phone numbers, contact persons) are removed before storage, by rules that can miss some. No personal data about individuals is collected.
- **What jobContext serves:** the current public version serves the fields read from a posting, short quotes (the lines of requirements and conditions, a short excerpt in search results), the link and the fetch date, and not the full text.
- **To opt out:** add `User-agent: jobcontext-study` / `Disallow: /` to robots.txt; the next round fetches nothing, and the postings shown until then are taken down. To have postings taken down, use the [contact form](https://docs.google.com/forms/d/e/1FAIpQLScM4FLqRFKj7jgp7uOGodR2xHrExO5YKKJGMgsX5k_vVEhrTQ/viewform) (no sign-in needed) or open an issue here (issues are public, so put no personal information in them); the crawler may still visit unless robots.txt stops it. A site that refuses the crawler is not worked around: its postings are taken down.
- **Contact:** the [contact form](https://docs.google.com/forms/d/e/1FAIpQLScM4FLqRFKj7jgp7uOGodR2xHrExO5YKKJGMgsX5k_vVEhrTQ/viewform) (Google Forms, no sign-in needed) or the [issues of this repository](https://github.com/ryoshumei/jobcontext-crawler/issues), in Japanese or English. Issues are public: use the form for anything personal.
