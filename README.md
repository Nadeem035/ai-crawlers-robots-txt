# AI Crawlers & robots.txt

A maintained list of the crawlers AI companies use, what each one is for, and ready-to-use `robots.txt` rules to allow or block them.

**Check your own site in seconds:** [AI Crawler Checker](https://seopeck.com/tool/ai-crawler-checker) reads your live robots.txt and shows which of these crawlers are allowed, blocked or partly blocked.

---

## Contents

- [Training crawlers vs search crawlers](#training-crawlers-vs-search-crawlers)
- [The crawler list](#the-crawler-list)
- [robots.txt templates](#robotstxt-templates)
- [How robots.txt matching works](#how-robotstxt-matching-works)
- [What robots.txt cannot do](#what-robotstxt-cannot-do)
- [Machine-readable list](#machine-readable-list)
- [Contributing](#contributing)

## Training crawlers vs search crawlers

Not every AI crawler does the same job, and the difference matters when you decide what to block.

| Kind (`kind` in the JSON) | What it does | Example tokens | If you block it |
|---|---|---|---|
| **Training** (`training`) | Collects content to train or improve AI models | `GPTBot`, `ClaudeBot`, `CCBot`, `Meta-ExternalAgent`, `Amazonbot`, plus the control tokens `Google-Extended` and `Applebot-Extended`\* | Your future content stays out of new training data. Content collected earlier is not removed. |
| **Search** (`search`) | Finds pages to show and cite in AI search answers | `OAI-SearchBot`, `Claude-SearchBot`, `PerplexityBot` | Your pages stop appearing as sources in that assistant's answers. |
| **User-triggered** (`user`) | Fetches a page because a user asked the assistant to | `ChatGPT-User`, `Claude-User`, `Perplexity-User` | The assistant cannot open your page when a user shares or asks about it. |
| **Search engine** (`engine`) | Normal web search crawlers, listed for comparison only | `Googlebot`, `Bingbot` | You disappear from that search engine. Do not block these to opt out of AI. |

\* `Google-Extended` and `Applebot-Extended` are **control tokens, not crawlers**: no bot visits your site with these names. Google and Apple crawl with `Googlebot` and `Applebot` and check these tokens to decide whether your content may be used for AI. Blocking them opts you out of those AI uses without affecting normal search. They are grouped under Training because that is what blocking them controls; `crawlers.json` marks them with `"control_token": true`.

A common middle ground for publishers: **block training, allow search and user-triggered crawlers**, so you keep citations and visits.

## The crawler list

| User-agent token | Operator | Kind | Purpose | Official docs |
|---|---|---|---|---|
| `GPTBot` | OpenAI | Training | Collects content to train OpenAI's models | [OpenAI crawlers](https://platform.openai.com/docs/bots) |
| `OAI-SearchBot` | OpenAI | Search | Finds pages to show in ChatGPT search results | [OpenAI crawlers](https://platform.openai.com/docs/bots) |
| `ChatGPT-User` | OpenAI | User-triggered | Fetches a page when a ChatGPT user asks for it | [OpenAI crawlers](https://platform.openai.com/docs/bots) |
| `ClaudeBot` | Anthropic | Training | Collects content to train Claude models | [Anthropic crawlers](https://support.anthropic.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler) |
| `Claude-SearchBot` | Anthropic | Search | Finds pages to improve Claude's search results | [Anthropic crawlers](https://support.anthropic.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler) |
| `Claude-User` | Anthropic | User-triggered | Fetches a page when a Claude user asks for it | [Anthropic crawlers](https://support.anthropic.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler) |
| `Google-Extended` | Google | Training (control token) | Controls use of your content for Gemini training and grounding; does not affect Google Search | [Google crawlers](https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers) |
| `Applebot-Extended` | Apple | Training (control token) | Controls use of your content to train Apple's AI models; does not affect Applebot | [About Applebot](https://support.apple.com/en-us/119829) |
| `PerplexityBot` | Perplexity | Search | Indexes pages for Perplexity's answers | [Perplexity crawlers](https://docs.perplexity.ai/guides/bots) |
| `Perplexity-User` | Perplexity | User-triggered | Fetches a page when a Perplexity user asks for it | [Perplexity crawlers](https://docs.perplexity.ai/guides/bots) |
| `Meta-ExternalAgent` | Meta | Training | Collects content to train Meta's AI models | Meta's web crawler documentation |
| `CCBot` | Common Crawl | Training | Builds the open Common Crawl dataset, which many AI models are trained on | [CCBot](https://commoncrawl.org/ccbot) |
| `Bytespider` | ByteDance | Training | Collects content for ByteDance, including AI training | — |
| `Amazonbot` | Amazon | Training | Collects content to improve Amazon's products and services; Amazon says it may be used to train Amazon AI models. Search experiences such as Alexa use a separate token, `Amzn-SearchBot` | [Amazonbot](https://developer.amazon.com/amazonbot) |

For comparison, the two main search engine crawlers (kind `engine`, not in `crawlers.json` so that a script blocking every entry never blocks search): `Googlebot` ([docs](https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers)) and `Bingbot` ([docs](https://www.bing.com/webmasters/help/which-crawlers-does-bing-use-8c184ec0)). Bing's index also powers Copilot answers. Blocking either removes you from that search engine.

> Operators add and rename crawlers from time to time. Always confirm against the official docs before relying on a rule. Pull requests with updates are welcome.

## robots.txt templates

Copy the one that matches your policy into the `robots.txt` file at the root of your domain (for example `https://example.com/robots.txt`). Keep your existing rules for other crawlers and your `Sitemap:` line.

| Template | Policy |
|---|---|
| [`templates/block-ai-training.txt`](templates/block-ai-training.txt) | Block AI training, allow AI search and user-triggered fetches (the common middle ground) |
| [`templates/block-all-ai.txt`](templates/block-all-ai.txt) | Block every AI crawler listed here, keep normal search engines |
| [`templates/allow-all.txt`](templates/allow-all.txt) | Allow everything, with a sitemap line |

Example, blocking training only:

```text
User-agent: GPTBot
User-agent: ClaudeBot
User-agent: Google-Extended
User-agent: Applebot-Extended
User-agent: Meta-ExternalAgent
User-agent: CCBot
User-agent: Bytespider
User-agent: Amazonbot
Disallow: /

User-agent: *
Allow: /

Sitemap: https://example.com/sitemap.xml
```

Need a full robots.txt with per-crawler rules and a crawl delay? Use the [Robots.txt Generator](https://seopeck.com/tool/robotstxt-generator).

## How robots.txt matching works

These rules come from the Robots Exclusion Protocol, [RFC 9309](https://www.rfc-editor.org/rfc/rfc9309):

1. **A crawler obeys only the group that names it.** If `GPTBot` has its own group, it ignores the `User-agent: *` group completely. A crawler not named anywhere falls back to `*`.
2. **Several `User-agent` lines can share one group**, as in the example above.
3. **Within a group, the longest matching rule wins.** `Disallow: /` plus `Allow: /blog/` means the blog is allowed and everything else is blocked. If an `Allow` and a `Disallow` rule are the same length, `Allow` wins.
4. **`*` matches any sequence of characters and `$` anchors the end of the URL**, for example `Disallow: /*.pdf$`.
5. **Names are case-insensitive**, but use the exact spelling from the official docs to be safe.
6. **No robots.txt (a 404) means everything is allowed.** A server error (5xx) tells crawlers to stay away until it is fixed.

Google's own [robots.txt introduction](https://developers.google.com/search/docs/crawling-indexing/robots/intro) is a good plain-language reference.

## What robots.txt cannot do

- **It is a request, not a lock.** The major AI companies say their crawlers respect it, but a badly behaved bot can ignore it. To stop a crawler for certain, block it at your server, firewall or CDN as well.
- **It is public.** Anyone can read your robots.txt, so never use it to hide private pages. Protect those with authentication.
- **It does not remove content already collected.** It only affects future crawling.
- **It does not remove pages from search results.** To keep a page out of search, use a `noindex` meta tag or header on a page crawlers are allowed to fetch.

Longer explanation with examples: [Should you block AI crawlers? A plain guide to robots.txt and AI bots](https://seopeck.com/blog/should-you-block-ai-crawlers-robots-txt).

## Machine-readable list

[`crawlers.json`](crawlers.json) contains the same list as the table above (token, operator, kind, purpose, docs URL, and `control_token`, which is `true` for tokens that are not real crawlers), for use in scripts, server rules or your own tools. `kind` is one of `training`, `search` or `user`, the same categories the [AI Crawler Checker](https://seopeck.com/tool/ai-crawler-checker) uses.

## Contributing

Spotted a new crawler, a renamed one, or a changed purpose? Open an issue or a pull request with a link to the operator's official documentation. Entries without an official source are marked as such.

## License

[CC0 1.0](LICENSE): public domain. Copy and adapt freely; a link back is appreciated but not required.

---

Maintained by [Nadeem Akram](https://seopeck.com/page/about-us) at [SEOpeck](https://seopeck.com), a collection of free SEO, developer and web tools.
