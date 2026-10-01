# Hoe werkt BotStopper?

Deze pagina legt uit wat BotStopper doet, voor wie het is bedoeld en wat je er als bezoeker of websitebeheerder van merkt. Hij is geschreven voor iedereen. Waar het nuttig is, staat er een korte technische tip voor webmasters (herkenbaar aan **Tip voor webmasters**).

This page is also available in English: [HOW_BOTSTOPPER_WORKS.md](HOW_BOTSTOPPER_WORKS.md).

## In het kort

BotStopper is een poortwachter die vóór je website staat. Voordat een bezoeker de website te zien krijgt, controleert BotStopper of er een echte browser aan de andere kant zit. Meestal gaat dat automatisch in een fractie van een seconde. De bezoeker ziet hooguit kort een pagina met "Verifying your browser before continuing" en wordt daarna doorgestuurd.

Het doel is om **geautomatiseerde scrapers**, vooral crawlers die websites leegtrekken voor het trainen van AI-modellen, tegen te houden. Die kunnen een website met duizenden verzoeken per minuut overspoelen. Dat maakt de site traag of onbereikbaar voor echte bezoekers, en het kost rekenkracht en bandbreedte.

BotStopper is de commerciële versie van het open-sourceproject [Anubis](https://anubis.techaro.lol) van Techaro.

## Hoe de controle werkt

### De rekenpuzzel (proof-of-work)

Een bezoeker die gecontroleerd moet worden, krijgt een kleine rekenpuzzel. De browser lost die met JavaScript zelf op. Voor één bezoeker is dat verwaarloosbaar werk, meestal minder dan een seconde. Voor een scraper die miljoenen pagina's wil ophalen, telt het op tot enorm veel rekenkracht. Daardoor wordt massaal scrapen duur, terwijl gewone bezoekers er nauwelijks iets van merken.

Is de puzzel opgelost, dan krijgt de browser een cookie. Met dat cookie hoeft de bezoeker een week lang niet opnieuw gecontroleerd te worden.

> **Tip voor webmasters:** de puzzel is een SHA-256-hash die met een bepaald aantal nullen moet beginnen. Het aantal nullen is de *difficulty*. Elke extra nul maakt de puzzel gemiddeld 16 keer zwaarder. Het cookie is een ondertekend JWT. Een bezoeker kan het dus niet zelf vervalsen.

### Niet iedereen krijgt dezelfde behandeling

BotStopper legt niet iedereen dezelfde puzzel voor. Elk verzoek wordt beoordeeld aan de hand van een **policy**, een lijst regels. Elke regel geeft een van vier uitkomsten:

| Uitkomst  | Betekenis                                                                           |
| --------- | ----------------------------------------------------------------------------------- |
| ALLOW     | Direct doorlaten, zonder controle.                                                  |
| DENY      | Direct weigeren. De bezoeker ziet een foutpagina.                                   |
| CHALLENGE | Een puzzel voorleggen.                                                              |
| WEIGH     | Geen besluit nemen, maar "verdachtheidspunten" toevoegen of aftrekken en doorgaan. |

De regels worden van boven naar beneden gecontroleerd. De eerste regel die ALLOW, DENY of CHALLENGE oplevert, beslist. WEIGH-regels tellen alleen punten op. Wordt er onderweg geen besluit genomen, dan bepaalt het totaal aantal punten wat er gebeurt:

| Punten      | Wat er gebeurt                                                                 |
| ----------- | ------------------------------------------------------------------------------ |
| 0 of minder | Doorlaten.                                                                     |
| 1 t/m 9     | Een heel lichte controle zonder JavaScript (een automatische doorverwijzing). |
| 10 t/m 19   | Een lichte rekenpuzzel (difficulty 2).                                         |
| 20 t/m 29   | Een zwaardere puzzel (difficulty 4).                                           |
| 30 of meer  | De zwaarste puzzel (difficulty 6).                                             |

Een gewone browser krijgt 10 punten, puur omdat hij zich als browser presenteert. Een normale bezoeker krijgt dus een lichte puzzel die vrijwel meteen is opgelost.

> **Tip voor webmasters:** de punten worden toegekend op basis van de `User-Agent`. Alles wat `Mozilla` of `Opera` in de User-Agent heeft (dat zijn alle gangbare browsers) krijgt +10. Juist scrapers doen zich vaak voor als een browser. Een verzoek dat zich eerlijk als tool bekendmaakt, zoals `curl` of een RSS-lezer, krijgt geen punten en komt dus gewoon door, tenzij een andere regel het tegenhoudt.

## Wat wordt er tegengehouden?

Met de standaardinstellingen worden de volgende verzoeken **geweigerd**:

- **AI-crawlers en AI-assistenten.** Bekende bots die websites verzamelen voor AI-training, AI-zoekmachines en AI-assistenten die namens een gebruiker een pagina ophalen. Denk aan GPTBot, ClaudeBot, ChatGPT-User, PerplexityBot, Bytespider, Amazonbot, Meta-ExternalAgent en tientallen andere. Standaard staat dit op de strengste stand (`aggressive`).
- **Headless browsers.** Browsers zonder scherm die door een programma worden bestuurd, zoals HeadlessChrome en Lightpanda. Die worden vrijwel alleen voor automatisering gebruikt.
- **Bekende problematische scrapers.** Onder andere een specifieke Amerikaanse AI-scraper, de "code review"-crawler van xAI en crawlers uit de clouds van Alibaba en Huawei.

De volgende verzoeken krijgen **extra punten** en dus een zwaardere puzzel:

- **Verdachte browserversies.** Een User-Agent die beweert Internet Explorer, Windows 95/98, Windows CE, een iPod of "Windows NT 11.0" te zijn (+20 punten). Echte bezoekers gebruiken die nauwelijks nog, scrapers geven ze nog vaak op.
- **Verzoeken via Cloudflare Workers** (+15 punten). Dat is een populaire manier om scrapers te verbergen.
- **Link-previews van Firefox AI** (+5 punten).

> **Tip voor webmasters:** "weigeren" levert standaard een foutpagina op met HTTP-status **200**, geen 403. Dat is een bewuste keuze van de Anubis-standaardpolicy: een scraper krijgt zo geen duidelijk signaal dat hij geblokkeerd is. Houd daar rekening mee als je in logs of monitoring naar statuscodes kijkt.

## Wat wordt er níét tegengehouden?

Een website moet vindbaar en bruikbaar blijven. Daarom laat BotStopper het volgende **zonder controle** door:

- **Zoekmachines.** Googlebot, Bingbot, Applebot, DuckDuckBot, Qwant, Yandex, Kagi, Mojeek, Marginalia, Common Crawl, het Internet Archive (Wayback Machine), Arquivo.pt en Wikimedia (voor bronvermeldingen). Een bot wordt alleen doorgelaten als **zowel** de naam **als** het IP-adres klopt met wat de zoekmachine officieel publiceert. Een scraper die zich als Googlebot voordoet, komt dus niet zomaar langs.
- **Standaardbestanden** die andere systemen nodig hebben: `robots.txt`, `sitemap.xml`, favicons en alles onder `/.well-known/` (bijvoorbeeld voor SSL-certificaten en `security.txt`).
- **Vertrouwde diensten.** Verzoeken vanaf de IP-adressen van Exonet zelf en van diensten die vaak webhooks sturen: GitHub, GitLab, Bitbucket, Klarna, Sentry en Stripe. Zo blijven betalingsbevestigingen en deploy-meldingen gewoon werken.
- **Interne netwerken.** Verzoeken vanaf privé-adressen (bijvoorbeeld `10.x.x.x` of `192.168.x.x`).
- **JSON-API's.** Verzoeken naar een pad dat met `/api/` begint en die expliciet om JSON vragen (`Accept: application/json`).
- **Tools die zich eerlijk bekendmaken.** Zoals hierboven beschreven: `curl`, monitoringtools, RSS-lezers en de meeste link-preview-bots van chat-apps komen door, zolang ze zich niet als browser voordoen en niet op een blokkadelijst staan.

> **Tip voor webmasters:** krijgt een koppeling (webhook, monitoring, app) toch een puzzel? Die kan de puzzel niet oplossen en krijgt dan een HTML-pagina in plaats van het verwachte antwoord. Kijk welke User-Agent en welk IP-adres de dienst gebruikt. Vraag ons om het IP-adres, het pad of de User-Agent aan de uitzonderingen toe te voegen. Dat kan per domein.

## Wat merkt een bezoeker ervan?

- **Bij het eerste bezoek** verschijnt heel kort een controlepagina, meestal minder dan een seconde. Daarna wordt de bezoeker automatisch doorgestuurd naar de pagina die de bezoeker wilde zien.
- **Daarna een week niets.** Het cookie blijft 7 dagen geldig.
- **Een ander IP-adres betekent opnieuw controleren.** Het cookie is gekoppeld aan het IP-adres van de bezoeker. Wie van wifi naar 4G/5G wisselt, krijgt dus opnieuw een (korte) controle. Zo kan een geldig cookie niet worden doorgegeven aan een netwerk van scrapers.
- **JavaScript is nodig.** Zonder JavaScript kan de browser de puzzel niet oplossen. Bezoekers met JavaScript uitgeschakeld of met sommige zeer strenge privacy-extensies kunnen daardoor niet verder.
- **Cookies moeten aan staan.** Het cookie is puur functioneel: het bevat alleen het bewijs dat de puzzel is opgelost. Het wordt niet gebruikt om bezoekers te volgen, en er komt geen externe partij aan te pas.
- **Neutraal uiterlijk.** De controlepagina heeft standaard een neutrale grijze stijl met Engelse tekst, omdat dezelfde pagina voor veel verschillende websites wordt gebruikt. Per website kunnen kleuren, afbeeldingen, titels en voettekst worden aangepast. Zie [CUSTOM_TEMPLATES.md](CUSTOM_TEMPLATES.md).

## Belangrijke ontwerpkeuzes

**Aan- en uitzetten per website.** BotStopper wordt per domein ingeschakeld met de instelling `managed_challenge`. Dat kan voor alle domeinen van een klant tegelijk, of voor een losse website. Een enkel domein kan ook weer worden uitgezonderd. Domeinen die alleen doorverwijzen naar een ander adres worden nooit beschermd, omdat daar niets te beschermen valt.

**Eén domein, één cookie.** Het cookie geldt voor het hele domein, inclusief subdomeinen. Wie `voorbeeld.nl` heeft bezocht, hoeft voor `www.voorbeeld.nl` niet opnieuw een puzzel op te lossen.

**Per website een eigen policy.** Elke website krijgt een eigen set regels. De standaard is voor iedereen gelijk, maar per domein kan bijvoorbeeld de AI-blokkade minder streng worden (`moderate` of `permissive`), een extra IP-adres worden toegestaan of een specifiek pad worden vrijgegeven.

> **Tip voor webmasters:** de drie standen van de AI-blokkade verschillen in wie er doorkomt:
>
> - `aggressive` (standaard) blokkeert alles wat met AI te maken heeft, ook AI-zoekmachines en AI-assistenten die namens een gebruiker een pagina openen.
> - `moderate` blokkeert trainingscrawlers, maar laat AI-zoekbots en AI-assistenten van OpenAI, Perplexity en Mistral door, mits ze van hun officiële IP-adressen komen.
> - `permissive` laat daarnaast ook de trainingscrawler van OpenAI (GPTBot) door.
>
> Wil je dat je site in AI-zoekresultaten verschijnt, dan is `moderate` meestal de passende keuze. Wil je AI-training écht uitsluiten, zet dan ook de juiste regels in je `robots.txt`. Sommige partijen, zoals Google, gebruiken hun gewone zoekcrawler ook voor AI en zijn alleen via `robots.txt` uit te sluiten.

**Gebaseerd op de standaard van Anubis.** De regels volgen de standaardpolicy die met Anubis v1.27.0 wordt meegeleverd. Daardoor profiteren we mee van de lijsten met bots die de Anubis-gemeenschap bijhoudt.

**Bewust uitgeschakeld:**

- *Honeypot.* Een val voor bots staat standaard uit, maar kan per domein worden aangezet.
- *DNS-blokkadelijsten.* Staan uit, zodat er bij elk verzoek geen externe opzoeking nodig is.

**Twee manieren van inbouwen.** Afhankelijk van de server staat BotStopper op een van twee manieren voor de website:

- *Achter nginx (per website).* Elke website heeft een eigen BotStopper-proces met een eigen sleutel. Al het verkeer gaat door BotStopper, dat het na controle doorgeeft aan de website.
- *Achter HAProxy (gedeeld).* Eén BotStopper-proces bedient alle websites achter een loadbalancer. HAProxy controleert het cookie zelf en stuurt een bezoeker alleen naar BotStopper als er nog geen geldig cookie is. Gecontroleerde bezoekers gaan daardoor rechtstreeks naar de website, zonder extra tussenstap.

> **Tip voor webmasters:** in de HAProxy-opstelling kan BotStopper zelf niemand "doorlaten": het kan alleen een puzzel geven of weigeren. Uitzonderingen zoals zoekmachines, webhooks en vertrouwde IP-adressen moeten in die opstelling daarom in HAProxy worden geregeld. Ook bezoekers met 0 punten krijgen daar de heel lichte controle zonder JavaScript. Werkt een uitzondering bij jou niet zoals op deze pagina beschreven, vraag dan na welke opstelling je website gebruikt.

## Veelgestelde vragen

**Wordt mijn website slechter vindbaar in Google?**
Nee. Googlebot en andere grote zoekmachines worden herkend en zonder controle doorgelaten.

**Blokkeert BotStopper alle bots?**
Nee. Het richt zich op bots die zich als browser voordoen en op bekende AI- en scraperbots. Nette bots die zich eerlijk bekendmaken, komen meestal gewoon door.

**Is BotStopper een vervanging voor een firewall of DDoS-bescherming?**
Nee. BotStopper maakt massaal scrapen duur en onaantrekkelijk. Het is geen bescherming tegen gerichte aanvallen of grote overbelastingsaanvallen.

**Mijn betaalprovider, webshopkoppeling of monitoring werkt niet meer. Wat nu?**
Neem contact met ons op en geef aan welke dienst het is en, als je dat weet, vanaf welk IP-adres of naar welk pad de verzoeken gaan. We kunnen een uitzondering toevoegen voor jouw domein.

**Kan BotStopper uit voor mijn website?**
Ja. BotStopper kan per domein worden uitgeschakeld.
