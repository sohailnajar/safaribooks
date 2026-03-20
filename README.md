# SafariBooks
Download and generate *EPUB* of your favorite books from [*O'Reilly Learning*](https://learning.oreilly.com) library.
I'm not responsible for the use of this program, this is only for *personal* and *educational* purpose.
Before any usage please read the *O'Reilly*'s [Terms of Service](https://learning.oreilly.com/terms/).

> **Fork note:** This is a fork of [lorenzodifuccia/safaribooks](https://github.com/lorenzodifuccia/safaribooks) with the O'Reilly API v2 migration applied. The original v1 API no longer works.

---

## Overview:
  * [Requirements & Setup](#requirements--setup)
  * [Authentication](#authentication)
  * [Usage](#usage)
  * [Calibre EPUB conversion](#calibre-epub-conversion)

## Requirements & Setup:
Requires `python3` and `pip3` to be installed.
```shell
$ git clone https://github.com/sohailnajar/safaribooks.git
$ cd safaribooks/
$ pip3 install -r requirements.txt
```

The program depends on two **Python 3** modules:
```
lxml>=4.1.1
requests>=2.20.0
```

## Authentication
Direct login via `--cred` no longer works — O'Reilly has added bot protection that blocks programmatic login. Instead, authenticate by extracting cookies from your browser.

### Step 1: Log in via browser
Log in at [https://learning.oreilly.com](https://learning.oreilly.com) using your email/password, SSO, company, or university login.

### Step 2: Extract cookies

**Option A — Auto-extract from Chrome** (recommended):
```shell
$ uv run --with browser_cookie3 python retrieve_cookies.py -b chrome
```
Also supports `firefox`, `edge`, and `chromium`.

**Option B — Paste from browser DevTools** (no extra dependencies):
```shell
$ python retrieve_cookies.py --paste
```
Then run this in your browser console on `learning.oreilly.com` and paste the output:
```javascript
JSON.stringify(document.cookie.split(';').reduce((o,c) => { let [k,v] = c.trim().split('='); o[k] = v; return o; }, {}))
```

Both options save a `cookies.json` file that the program uses for authentication.

> **Note:** Your session will eventually expire. When it does, just log in again and re-run the cookie extraction.

## Usage:
Choose a book from the library and run:
```shell
$ python3 safaribooks.py XXXXXXXXXXXXX
```

The ID is the digits in the URL of the book page:
`https://learning.oreilly.com/library/view/book-name/XXXXXXXXXXXXX/`
For example: `https://learning.oreilly.com/library/view/building-knowledge-graphs/9781098127091/`

#### Program options:
```shell
$ python3 safaribooks.py --help
usage: safaribooks.py [--kindle] [--preserve-log] [--help] <BOOK ID>

Download and generate an EPUB of your favorite books from O'Reilly Learning.

positional arguments:
  <BOOK ID>            Book digits ID that you want to download. You can find
                       it in the URL (X-es):
                       `https://learning.oreilly.com/library/view/book-
                       name/XXXXXXXXXXXXX/`

optional arguments:
  --kindle             Add some CSS rules that block overflow on `table` and
                       `pre` elements. Use this option if you're going to
                       export the EPUB to E-Readers like Amazon Kindle.
  --preserve-log       Leave the `info_XXXXXXXXXXXXX.log` file even if there
                       isn't any error.
  --help               Show this help message.
```

Pay attention if you use a shared PC, because anyone with access to your files can use your session via `cookies.json`.

You can configure proxies by setting the environment variable `HTTPS_PROXY` or using the `USE_PROXY` directive in the script.

#### Calibre EPUB conversion
**Important**: since the script only downloads HTML pages and creates a raw EPUB, many of the CSS and XML/HTML directives may not render perfectly on E-Readers. To ensure best quality, convert the EPUB with [Calibre](https://calibre-ebook.com/):
```bash
$ ebook-convert "Books/Book Title (ID)/ID.epub" "Books/Book Title (ID)/ID_CLEAR.epub"
```

The `--kindle` option adds CSS rules for better compatibility with Amazon Kindle. If exporting to Kindle, convert to `AZW3` with Calibre and select `Ignore margins` in the conversion options.

## Example:
```shell
$ python3 safaribooks.py 9781098127091

       ____     ___         _
      / __/__ _/ _/__ _____(_)
     _\ \/ _ `/ _/ _ `/ __/ /
    /___/\_,_/_/ \_,_/_/ /_/
      / _ )___  ___  / /__ ___
     / _  / _ \/ _ \/  '_/(_-<
    /____/\___/\___/_/\_\/___/

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[-] Successfully authenticated.
[*] Retrieving book info...
[-] Title: Building Knowledge Graphs
[-] Authors: Jesús Barrasa, Jim Webber
[-] Identifier: 9781098127091
[-] ISBN: 9781098127107
[-] Publishers: O'Reilly Media, Inc.
[*] Retrieving book chapters...
[-] Downloading book contents... (21 chapters)
    [#####################################################################] 100%
[-] Downloading book CSSs... (1 files)
    [#####################################################################] 100%
[-] Downloading book images... (113 files)
    [#####################################################################] 100%
[-] Creating EPUB file...
[*] Done: Books/Building Knowledge Graphs (9781098127091)/9781098127091.epub
[!] Bye!!
```

---

## Credits
Originally created by *Lorenzo Di Fuccia* — [lorenzodifuccia/safaribooks](https://github.com/lorenzodifuccia/safaribooks)
