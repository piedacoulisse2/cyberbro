# Cyberbro

A simple application that extracts your IoCs from garbage input and checks their reputation using multiple services. \
Inspired by [Cybergordon](https://cybergordon.com/) and [IntelOwl](https://github.com/intelowlproject/IntelOwl).

This project aims to provide a simple and efficient way to check the reputation of your observables using multiple services, 
without having to deploy a **complex** solution.

**==> Checkout the public demo: [https://demo.cyberbro.net/](https://demo.cyberbro.net/) <==**

![GitHub stars](https://img.shields.io/github/stars/stanfrbd/cyberbro?style=social)
[![Follow on X/Twitter](https://img.shields.io/twitter/follow/cyberbro_cti?style=social)](https://x.com/cyberbro_cti)
![GitHub issues](https://img.shields.io/github/issues/stanfrbd/cyberbro)
![License](https://img.shields.io/github/license/stanfrbd/cyberbro)
![example branch parameter](https://github.com/stanfrbd/cyberbro/actions/workflows/jobs.yml/badge.svg)

![cyberbro_gh](https://github.com/user-attachments/assets/d82a021c-a199-4f07-ab26-7af8e1b650a0)

# Features

* **Effortless Input Handling**: Paste raw logs, IoCs, or fanged IoCs, and let our regex parser do the rest.
* **Multi-Service Reputation Checks**: Verify observables (IP, hash, domain, URL) across multiple services like VirusTotal, AbuseIPDB, IPInfo, Spur.us, MDE, Google Safe Browsing, Shodan, Abusix, Phishtank, ThreatFox, URLscan, Github, Google...
* **Detailed Reports**: Generate comprehensive reports with advanced search and filter options.
* **High Performance**: Leverage multithreading for faster processing.
* **Automated Observable Pivoting**: Automatically pivot on domains, URL and IP addresses using reverse DNS and RDAP.
* **Accurate Domain Info**: Retrieve precise domain information from ICANN RDAP (next generation whois).
* **Abuse Contact Lookup**: Accurately find abuse contacts for IPs, URLs, and domains.
* **Export Options**: Export results to CSV and **autofiltered well formatted** Excel files.
* **MDE Integration**: Check if observables are flagged on your Microsoft Defender for Endpoint (MDE) tenant.
* **OpenCTI Integration**: Get stats (number of incidents, indicators) from OpenCTI and the latest Indicator if available.
* **Proxy Support**: Use a proxy if required.
* **Data Storage**: Store results in a SQLite database.
* **Grep.App**: Search for observables with Grep.App API (fast GitHub searches).
* **Analysis History**: Maintain a history of analyses with easy retrieval and search functionality.

# What Cyberbro does that others don't

* **Accessible to everyone** from beginners to experts. No gatekeeping here.
* **Lightweight & Easy Deployment**: Simple to set up and use.
* **Advanced TLD Verification**: Uses `tldextract` to accurately extract root domains, helping RDAP lookups.
* **Pragmatic Information Gathering**: Utilizes GitHub and Google indexed results to catch what other engines might miss.
* **CTI Report Integration**: Leverages IoC.One for IoC-related **CTI reports** in HTML or PDF.
* **EDR Integration**: Integrates with solutions like Microsoft Defender for Endpoint to check if observables were seen in YOUR environment.


# Getting Started - TL;DR

> [!TIP]
> If you are lazy, you need Docker. \
> Do a `git clone` ; copy `secrets-sample.json` to `secrets.json` ; `docker compose up` then go to `localhost:5000`. Yep, that's it!

# Getting Started

* To get started, clone the repository

```bash
git clone https://github.com/stanfrbd/cyberbro
cd cyberbro
```

## Edit the config file (mandatory)

```
cp secrets-sample.json secrets.json
```

> [!NOTE]
> Don't have API keys? No problem, just copy the `secrets-sample.json` to `secrets.json` and leave all like this. Be careful if a proxy is used. \
> You will be able to use **all free engines!**

* Fill values (including proxy if needed) in the `secrets.json` file.

```json
{
    "virustotal": "token_here",
    "abuseipdb": "token_here",
    "ipinfo": "token_here",
    "google_safe_browsing": "token_here",
    "proxy_url": "",
    "mde_tenant_id": "tenant_here",
    "mde_client_id": "client_id_here",
    "mde_client_secret": "client_secret_here",
    "shodan": "token_here",
    "opencti_api_key": "token_here",
    "opencti_url": "https://demo.opencti.io"
}
```

* Obtain API keys from the official documentation of each service.
* Microsoft Defender for Endpoint (MDE) is a paid service and can be skipped if you don't have an account (unchecked by default).

> [!IMPORTANT]
> You can modify the configuration via the GUI at [http://127.0.0.1:5000/config](http://127.0.0.1:5000/config). \
> This endpoint is disabled by default for security reasons, as it is not protected. \
> To enable it, set `app.config['CONFIG_PAGE_ENABLED'] = True` at the beginning of `app.py`. \
> **This is not recommended for public or team use, as it exposes your API keys.**

# Launch the app

## Lazy and easy - use docker

```bash
docker compose up # use -d to run in background and use --build to rebuild the image
```

* Go to http://127.0.0.1:5000 and Enjoy.

> Don't forget to edit the `secrets.json` before building the image.

## The old way

* Clone the repository and install the requirements.

You might want to create a [`venv`](https://docs.python.org/3/library/venv.html) before installing the dependencies.

```bash
pip install -r requirements.txt
```

* Run the app with `gunicorn` (clean mode).

```bash
gunicorn -w 4 -t 4 -b 0.0.0.0:5000 app:app
```

* Run the app with in development mode.

```bash
python3 app.py
```

# Screenshots

<details>
<summary>See all screenshots</summary>

![image-analysis](https://github.com/user-attachments/assets/1331e340-e95d-4b0a-b487-f13b27f2e24d)

![image-history](https://github.com/user-attachments/assets/859c5f43-6da9-4a6a-8b64-23e5035df8a5)

![image-stats](https://github.com/user-attachments/assets/c4676eb5-b6de-4611-bade-e21d9e10fcf3)

</details>

![image](https://github.com/user-attachments/assets/5092341e-30af-42e8-baff-772836873e24)
![image](https://github.com/user-attachments/assets/e36a900e-873c-4eec-854f-1d01a97ebc1c)

> [!CAUTION]
> This is still a development server, not intended for production. \
Some misconfigurations may lead to **security issues**.

# Cyberbro API

* The API is available at `/api/` and can be accessed via the GUI or command-line.

**There are currently 3 endpoints:**

* `/api/analyze` - Analyze a text and return analysis ID (JSON).
* `/api/is_analysis_complete/<analysis_id>` - Check if the analysis is complete (JSON).
* `/api/results/<analysis_id>` - Retrieve the results of a previous analysis (JSON).

```bash
curl -X POST "http://localhost:5000/api/analyze" -H "Content-Type: application/json" -d '{"text": "20minutes.fr", "engines": ["reverse_dns", "rdap"]}'
```

```json
{
  "analysis_id": "e88de647-b153-4904-91e5-8f5c79174854",
  "link": "/results/e88de647-b153-4904-91e5-8f5c79174854"
}
```

```bash
curl "http://localhost:5000/api/is_analysis_complete/e88de647-b153-4904-91e5-8f5c79174854"
```

```json
{
  "complete": true
}
```

```bash
curl "http://localhost:5000/api/results/e88de647-b153-4904-91e5-8f5c79174854"
```

```json
[
  {
    "observable": "20minutes.fr",
    "rdap": {
      "abuse_contact": "",
      "creation_date": "2001-07-11",
      "expiration_date": "2028-01-08",
      "link": "https://rdap.nic.fr/domain/20minutes.fr",
      "name_servers": [
        "ns-1271.awsdns-30.org",
        "ns-748.awsdns-29.net",
        "ns-16.awsdns-02.com",
        "ns-1958.awsdns-52.co.uk"
      ],
      "organization": "",
      "registrant": "20 MINUTES FRANCE SAS",
      "registrant_email": "0d6621ed24c26f0d32e2c4f76b507da9-679847@contact.gandi.net",
      "registrar": "GANDI",
      "update_date": "2024-11-18"
    },
    "reverse_dns": {
      "reverse_dns": [
        "13.249.9.82",
        "13.249.9.92",
        "13.249.9.83",
        "13.249.9.129"
      ]
    },
    "reversed_success": true,
    "type": "FQDN"
  }
]
```

> [!NOTE]
> The [dedicated wiki page](https://github.com/stanfrbd/cyberbro/wiki/API-usage-and-engine-names) gives all the names of usable engines.

# API and third-party services

* [VirusTotal](https://developers.virustotal.com/v3.0/reference)
* [AbuseIPDB](https://docs.abuseipdb.com/)
* [IPquery](https://ipquery.gitbook.io/ipquery-docs)
* [IPinfo](https://ipinfo.io/developers)
* [Google Safe Browsing](https://developers.google.com/safe-browsing)
* [Microsoft Defender for Endpoint](https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-for-endpoint-api)
* [Shodan](https://developer.shodan.io/)
* [Spur.us](https://spur.us/)
* [Abusix](https://abusix.com/)
* [Phishtank](https://www.phishtank.com/)
* [OpenRDAP](https://openrdap.org/api)
* [ICANN](https://lookup.icann.org/)
* [Google](https://google.com/)
* [Github](https://github.com/)
* [ThreatFox](https://threatfox.abuse.ch/api/)
* [URLscan](https://urlscan.io/)
* [Ioc.One](https://ioc.one/)
* [OpenCTI](https://www.opencti.io/)
* [Grep.App](https://grep.app/)

> [!NOTE]
> Any questions? Check the [wiki](https://github.com/stanfrbd/cyberbro/wiki) or raise an [issue](https://github.com/stanfrbd/cyberbro/issues/new) \
> For the advanced config (tuning of `supervisord.conf` before deployment, selection of visible engines, change `/api/` prefix...), check the [dedicated wiki page](https://github.com/stanfrbd/cyberbro/wiki/Advanced-options-in-secrets.json).

# Special thanks

A huge thank you to all the amazing contributors who made pull requests and helped improve this project:

* [Florian PILLOT](https://github.com/Harukunnn) who reworked engines (refactoring and optimizations).
* [Axel](https://github.com/botlabsDev) who develops [Ioc.One](https://ioc.one/) and added a specific User-Agent allowing scraping of Ioc[.]One.

Your contributions are greatly appreciated!

# License

```
MIT License

Copyright (c) 2025 stanfrbd

Permission is hereby granted, free of charge, to any person obtaining a copy 
of this software and associated documentation files (the "Software"), to deal 
in the Software without restriction, including without limitation the rights 
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell 
copies of the Software, and to permit persons to whom the Software is 
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included 
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS 
OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, 
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL 
THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER 
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING 
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER 
DEALINGS IN THE SOFTWARE.
```

