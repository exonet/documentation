# Anubis

This role installs and manages [BotStopper](https://anubis.techaro.lol/docs/admin/botstopper/), the commercial edition of [Anubis](https://anubis.techaro.lol). BotStopper sits in front of a website and checks whether a visitor uses a real browser before it lets the visitor through. It stops AI crawlers and other automated scrapers, while normal visitors and search engines barely notice it.

More documentation:

- [How does BotStopper work?](HOW_BOTSTOPPER_WORKS.md): what BotStopper does and what the default policy blocks and allows.
- [Hoe werkt BotStopper?](HOE_WERKT_BOTSTOPPER.md): the same explanation in Dutch.
- [Custom templates](CUSTOM_TEMPLATES.md): how the challenge, error and imprint pages look, with previews.

## Variables

BotStopper is configured per domain in the `users` variable, usually in `vars/users.yml`. All other settings of the role are managed by Exonet. Only the keys below can be set by customers.

| Name | Level | Type | Default | Description |
| ---- | ----- | ---- | ------- | ----------- |
| `managed_challenge` | user, domain | `bool` | `false` | Protect the domains with BotStopper. On a user it applies to all domains of the user; on a domain it overrides the user setting. |
| `policy` | domain | `dict` | | Changes to the default bot policy for this domain. See [Bot policy](#bot-policy). |
| `challenge_title` | domain | `str` | `Verifying your browser before continuing` | Title of the challenge page. |
| `error_title` | domain | `str` | `Something went wrong — please try again` | Title of the error page. |
| `footer_text` | domain | `str` | `Protected by BotStopper.` | Footer text of the challenge and error pages. |
| `webmaster_email` | domain | `str` | | Contact address shown in the footer of the challenge and error pages. |
| `forced_language` | domain | `str` | | Show the challenge page in one language, for example `nl`. By default the language of the browser is used. |

## Enable BotStopper

Set `managed_challenge: true` on a user to protect all of its domains, or on a single domain to protect only that domain. Set `managed_challenge: false` on a domain to exclude it when the user has it enabled. Domains with a `redirect` are never protected, because there is no website behind them.

```yaml
users:
  - name: example_prd
    managed_challenge: true   # protect all domains of this user
    uid: 1500
    domains:
      - name: example.nl
        aliases:
          - www.example.nl
      - name: opt-out.nl
        managed_challenge: false   # do not protect this domain

  - name: other_prd
    uid: 1501
    domains:
      - name: selective.nl
        managed_challenge: true   # protect only this domain
```

A domain and its aliases share one cookie, so a visitor who passed the check on `example.nl` is not checked again on `www.example.nl`.

## Bot policy

Every protected domain gets its own policy, based on the default policy of Anubis. The default suits most websites. Change it per domain with `policy`; only the keys you set change, the rest of the default is kept.

The following keys are available below `policy.bots`:

| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| `ai_block` | `bool` / `str` | `aggressive` | How strictly AI bots are blocked: `aggressive`, `moderate` or `permissive`. Set to `false` to not block AI bots. See [the AI block levels](HOW_BOTSTOPPER_WORKS.md#key-design-decisions). |
| `whitelist_addresses` | `list` | `[]` | IP addresses or networks (CIDR) that are let through without a check, for example an office or a monitoring service. |
| `rules` | `list` | `[]` | Custom rules, checked before the default rules. See [Custom rules](#custom-rules). |
| `whitelist` | `bool` | `true` | Let through the addresses in `whitelist_addresses` and the trusted services (Exonet, GitHub, GitLab, Bitbucket, Klarna, Sentry and Stripe). |
| `good_crawlers` | `bool` | `true` | Let through known search engines, such as Googlebot and Bingbot. |
| `json_api` | `bool` | `true` | Let through requests to `/api/` that ask for JSON. |
| `keep_internet_working` | `bool` | `true` | Let through `robots.txt`, `sitemap.xml`, favicons and `/.well-known/`. |
| `private` | `bool` | `true` | Let through requests from private addresses. |
| `pathological` | `bool` | `true` | Block known problematic bots, such as headless browsers. |
| `aggressive_brazilian_scrapers` | `bool` | `true` | Block a group of known aggressive scrapers. |
| `xai` | `bool` | `true` | Apply the rules for the crawlers of xAI. |
| `firefox_ai` | `bool` | `true` | Challenge Firefox AI link previews. |
| `generic_browser` | `bool` | `true` | Give browser-like visitors a light check. Disabling this lets most visitors through without a check. |

The honeypot, a trap for bots, is disabled by default. Enable it for a domain with `policy.honeypot.enabled: true`.

This example keeps the default policy, but uses the moderate AI block, disables the Firefox AI rule and lets an office address through:

```yaml
users:
  - name: example_prd
    managed_challenge: true
    domains:
      - name: example.nl
        policy:
          bots:
            ai_block: moderate
            firefox_ai: false
            whitelist_addresses:
              - 192.0.2.10/32
```

### Custom rules

A custom rule has a `name`, an `action` and one or more conditions. The first rule that matches decides what happens. Custom rules are checked before the default rules, so they take precedence.

| Key | Description |
| --- | ----------- |
| `name` | Unique name of the rule. |
| `action` | `ALLOW` (let through), `DENY` (block) or `CHALLENGE` (always check). |
| `path_regex` | Regular expression that matches the path of the request. |
| `user_agent_regex` | Regular expression that matches the `User-Agent` header. |
| `remote_addresses` | List of IP addresses or networks (CIDR). |

This example lets a health check and a webhook endpoint through without a check, and blocks a scraper by its user agent:

```yaml
domains:
  - name: example.nl
    policy:
      bots:
        rules:
          - name: allow-health
            action: ALLOW
            path_regex: ^/health$
          - name: allow-webhooks
            action: ALLOW
            path_regex: ^/webhooks/
          - name: deny-example-scraper
            action: DENY
            user_agent_regex: ExampleScraper
```

Use `ALLOW` rules sparingly: a path that is let through is also reachable for scrapers.

### Websites behind HAProxy

On a setup with a HAProxy load balancer, one BotStopper serves all websites behind the load balancer and cannot let visitors through itself. In that setup `managed_challenge` still decides which domains are protected, but the `policy` and the appearance settings of a domain have no effect. Exceptions, such as an address or path that must be let through, are made in HAProxy by Exonet. Describe the exception you need in the pull request. Ask Exonet when you are not sure which setup your website uses.

## Appearance

By default the challenge page has a neutral grey style with English text, because the same page is used for many websites. The titles, footer text and contact address can be changed per domain:

```yaml
domains:
  - name: example.nl
    challenge_title: "Een moment geduld alstublieft"
    error_title: "Er ging iets mis"
    footer_text: "Beveiligd door Example B.V."
    webmaster_email: webmaster@example.nl
    forced_language: nl
```

See [Custom templates](CUSTOM_TEMPLATES.md) for how to change the colors, images and templates of the pages. Ask Exonet when something is unclear or you need more.
