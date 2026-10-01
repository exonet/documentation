# How does BotStopper work?

This page explains what BotStopper does, who it is for, and what you notice as a visitor or website owner. It is written for everyone. Where useful, there is a short technical tip for webmasters (marked **Tip for webmasters**).

Deze pagina is ook beschikbaar in het Nederlands: [HOE_WERKT_BOTSTOPPER.md](HOE_WERKT_BOTSTOPPER.md).

## In short

BotStopper is a gatekeeper that sits in front of your website. Before a visitor sees the website, BotStopper checks whether there is a real browser on the other end. This usually happens automatically in a fraction of a second. At most, the visitor briefly sees a page saying "Verifying your browser before continuing" and is then sent on.

The goal is to stop **automated scrapers**, especially crawlers that harvest websites to train AI models. They can flood a website with thousands of requests per minute. That makes the site slow or unreachable for real visitors, and it costs computing power and bandwidth.

BotStopper is the commercial edition of the open-source project [Anubis](https://anubis.techaro.lol) by Techaro.

## How the check works

### The computing puzzle (proof-of-work)

A visitor who needs to be checked gets a small computing puzzle. The browser solves it on its own using JavaScript. For a single visitor this is negligible work, usually less than a second. For a scraper that wants to fetch millions of pages, it adds up to an enormous amount of computing power. This makes mass scraping expensive, while regular visitors barely notice it.

Once the puzzle is solved, the browser receives a cookie. With that cookie, the visitor does not need to be checked again for a week.

> **Tip for webmasters:** the puzzle is a SHA-256 hash that must start with a certain number of zeros. The number of zeros is the *difficulty*. Each extra zero makes the puzzle about 16 times harder on average. The cookie is a signed JWT, so a visitor cannot forge it.

### Not everyone gets the same treatment

BotStopper does not give everyone the same puzzle. Each request is assessed against a **policy**, a list of rules. Each rule has one of four outcomes:

| Outcome   | Meaning                                                                    |
| --------- | -------------------------------------------------------------------------- |
| ALLOW     | Let through immediately, without a check.                                  |
| DENY      | Refuse immediately. The visitor sees an error page.                        |
| CHALLENGE | Present a puzzle.                                                          |
| WEIGH     | Make no decision, but add or subtract "suspicion points" and continue. |

The rules are checked from top to bottom. The first rule that results in ALLOW, DENY, or CHALLENGE decides. WEIGH rules only add points. If no decision is made along the way, the total number of points decides what happens:

| Points     | What happens                                                    |
| ---------- | --------------------------------------------------------------- |
| 0 or fewer | Let through.                                                    |
| 1 to 9     | A very light check without JavaScript (an automatic redirect). |
| 10 to 19   | A light computing puzzle (difficulty 2).                        |
| 20 to 29   | A harder puzzle (difficulty 4).                                 |
| 30 or more | The hardest puzzle (difficulty 6).                              |

A regular browser gets 10 points simply because it presents itself as a browser. A normal visitor therefore gets a light puzzle that is solved almost instantly.

> **Tip for webmasters:** points are assigned based on the `User-Agent`. Anything with `Mozilla` or `Opera` in its User-Agent (which covers all common browsers) gets +10. Scrapers in particular often pretend to be a browser. A request that honestly identifies itself as a tool, such as `curl` or an RSS reader, gets no points and therefore passes through, unless another rule stops it.

## What is blocked?

With the default settings, the following requests are **refused**:

- **AI crawlers and AI assistants.** Known bots that collect websites for AI training, AI search engines, and AI assistants that fetch a page on behalf of a user. Examples are GPTBot, ClaudeBot, ChatGPT-User, PerplexityBot, Bytespider, Amazonbot, Meta-ExternalAgent, and dozens of others. By default this is set to the strictest level (`aggressive`).
- **Headless browsers.** Browsers without a screen that are controlled by a program, such as HeadlessChrome and Lightpanda. These are used almost exclusively for automation.
- **Known problematic scrapers.** Among others, a specific American AI scraper, xAI's "code review" crawler, and crawlers from the Alibaba and Huawei clouds.

The following requests get **extra points**, and therefore a harder puzzle:

- **Suspicious browser versions.** A User-Agent claiming to be Internet Explorer, Windows 95/98, Windows CE, an iPod, or "Windows NT 11.0" (+20 points). Real visitors hardly use these anymore, but scrapers still often claim them.
- **Requests via Cloudflare Workers** (+15 points). This is a popular way to hide scrapers.
- **Firefox AI link previews** (+5 points).

> **Tip for webmasters:** by default, "refusing" returns an error page with HTTP status **200**, not 403. This is a deliberate choice in the Anubis default policy: it gives a scraper no clear signal that it has been blocked. Keep this in mind when you look at status codes in logs or monitoring.

## What is not blocked?

A website must remain findable and usable. That is why BotStopper lets the following through **without a check**:

- **Search engines.** Googlebot, Bingbot, Applebot, DuckDuckBot, Qwant, Yandex, Kagi, Mojeek, Marginalia, Common Crawl, the Internet Archive (Wayback Machine), Arquivo.pt, and Wikimedia (for citations). A bot is only let through if **both** its name **and** its IP address match what the search engine officially publishes. A scraper pretending to be Googlebot therefore does not simply get past.
- **Standard files** that other systems need: `robots.txt`, `sitemap.xml`, favicons, and everything under `/.well-known/` (for example for SSL certificates and `security.txt`).
- **Trusted services.** Requests from the IP addresses of Exonet itself and of services that often send webhooks: GitHub, GitLab, Bitbucket, Klarna, Sentry, and Stripe. This keeps payment confirmations and deploy notifications working.
- **Internal networks.** Requests from private addresses (for example `10.x.x.x` or `192.168.x.x`).
- **JSON APIs.** Requests to a path starting with `/api/` that explicitly ask for JSON (`Accept: application/json`).
- **Tools that honestly identify themselves.** As described above: `curl`, monitoring tools, RSS readers, and most link-preview bots of chat apps get through, as long as they do not pretend to be a browser and are not on a block list.

> **Tip for webmasters:** does an integration (webhook, monitoring, app) still get a puzzle? It cannot solve the puzzle and receives an HTML page instead of the expected response. Check which User-Agent and IP address the service uses. Ask us to add the IP address, path, or User-Agent to the exceptions. This can be done per domain.

## What does a visitor notice?

- **On the first visit**, a check page appears very briefly, usually for less than a second. The visitor is then automatically sent on to the page they wanted to see.
- **Then nothing for a week.** The cookie stays valid for 7 days.
- **A different IP address means another check.** The cookie is tied to the visitor's IP address. Someone who switches from Wi-Fi to 4G/5G therefore gets another (short) check. This prevents a valid cookie from being passed on to a network of scrapers.
- **JavaScript is required.** Without JavaScript, the browser cannot solve the puzzle. Visitors with JavaScript disabled, or with some very strict privacy extensions, therefore cannot continue.
- **Cookies must be enabled.** The cookie is purely functional: it only contains proof that the puzzle was solved. It is not used to track visitors, and no third party is involved.
- **Neutral appearance.** By default, the check page has a neutral grey style with English text, because the same page is used for many different websites. Colors, images, titles, and footer text can be customized per website. See [CUSTOM_TEMPLATES.md](CUSTOM_TEMPLATES.md).

## Key design decisions

**Enabled per website.** BotStopper is enabled per domain with the `managed_challenge` setting. This can be done for all domains of a customer at once, or for a single website. A single domain can also be excluded again. Domains that only redirect to another address are never protected, because there is nothing to protect there.

**One domain, one cookie.** The cookie applies to the whole domain, including subdomains. Someone who has visited `example.com` does not need to solve a puzzle again for `www.example.com`.

**A separate policy per website.** Each website gets its own set of rules. The default is the same for everyone, but per domain the AI block can, for example, be made less strict (`moderate` or `permissive`), an extra IP address can be allowed, or a specific path can be opened up.

> **Tip for webmasters:** the three levels of the AI block differ in who gets through:
>
> - `aggressive` (default) blocks everything AI-related, including AI search engines and AI assistants that open a page on behalf of a user.
> - `moderate` blocks training crawlers, but lets AI search bots and AI assistants from OpenAI, Perplexity, and Mistral through, provided they come from their official IP addresses.
> - `permissive` additionally lets OpenAI's training crawler (GPTBot) through.
>
> If you want your site to appear in AI search results, `moderate` is usually the right choice. If you really want to exclude AI training, also add the right rules to your `robots.txt`. Some parties, such as Google, also use their regular search crawler for AI and can only be excluded through `robots.txt`.

**Based on the Anubis default.** The rules follow the default policy shipped with Anubis v1.27.0. This way we benefit from the bot lists maintained by the Anubis community.

**Deliberately disabled:**

- *Honeypot.* A trap for bots is disabled by default, but can be enabled per domain.
- *DNS block lists.* Disabled, so no external lookup is needed for every request.

**Two ways of integration.** Depending on the server, BotStopper sits in front of the website in one of two ways:

- *Behind nginx (per website).* Each website has its own BotStopper process with its own key. All traffic goes through BotStopper, which passes it on to the website after the check.
- *Behind HAProxy (shared).* One BotStopper process serves all websites behind a load balancer. HAProxy checks the cookie itself and only sends a visitor to BotStopper when there is no valid cookie yet. Checked visitors therefore go straight to the website, without an extra hop.

> **Tip for webmasters:** in the HAProxy setup, BotStopper itself cannot "let anyone through": it can only present a puzzle or refuse. Exceptions such as search engines, webhooks, and trusted IP addresses therefore have to be handled in HAProxy in that setup. Visitors with 0 points also get the very light check without JavaScript there. If an exception does not work for you as described on this page, ask which setup your website uses.

## Frequently asked questions

**Will my website become harder to find in Google?**
No. Googlebot and other major search engines are recognized and let through without a check.

**Does BotStopper block all bots?**
No. It targets bots that pretend to be a browser and known AI and scraper bots. Well-behaved bots that honestly identify themselves usually get through.

**Is BotStopper a replacement for a firewall or DDoS protection?**
No. BotStopper makes mass scraping expensive and unattractive. It is not protection against targeted attacks or large denial-of-service attacks.

**My payment provider, webshop integration, or monitoring has stopped working. What now?**
Contact us and tell us which service it is and, if you know, which IP address the requests come from or which path they go to. We can add an exception for your domain.

**Can BotStopper be turned off for my website?**
Yes. BotStopper can be disabled per domain.
