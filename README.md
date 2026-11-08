# phim-dl

**English** · [Tiếng Việt](README.vi.md)

> [!WARNING]
> **This project is for educational purposes only.** It shows how HLS streaming, stream decryption
> and parallel downloads work. Do not use it to re-upload, share or sell movies.

Download movies and series from **phimkey.com** and its clone sites (**phimsea.com**, **phimfix.com**,
**xemphim.in**, ...) as **MP4** files, with their subtitles.

- Paste a link: a movie downloads as one file, a series downloads **every episode of every season**
- Original quality (usually 1080p, with 5.1 audio when the movie has it), no re-encoding
- **Subtitles** saved next to the video (`.vi.srt`, `.en.srt`, ... the best one of each language)
- See the **quality, size and bitrate** on each server before downloading (`--info`)
- Fast downloads with **aria2c** (a built-in downloader is used without it)
- Broken segments are downloaded again; if a server fails, the next one is tried
- Live progress bars: one per episode plus a total, with speed and ETA
- Stop anytime, run the same command again to continue

These sites only work with an account, so you need one to use phim-dl. Each site has its own accounts:
use an account of the site in the link.

## Install

1. Install [Python 3.10+](https://www.python.org/downloads/)
   (on Windows, tick **Add python.exe to PATH** on the first screen of the installer)
2. Download this project: **Code** → **Download ZIP**, then unzip it
3. Open a terminal in the unzipped folder (on Windows: click the address bar of the folder,
   type `cmd` and press Enter), then run:
   ```bash
   python -m pip install -r requirements.txt
   ```
4. Put `aria2c.exe` next to `phim-dl.py` (recommended, faster)
   - aria2c: https://github.com/aria2/aria2/releases (the `win-64bit` zip, take `aria2c.exe` out of it)
   - ffmpeg is installed by step 3. You can also put your own `ffmpeg.exe` next to the script.

## Log in

Log in once; the session is kept in `session.txt` next to the script:

```bash
python phim-dl.py --login
```

It asks for your email and password. The password is only sent to the site and is never saved.

That logs in to phimkey.com. For a clone site, add `--site`. You can be logged in to several sites
at once; each link uses the session of its own domain:

```bash
python phim-dl.py --login --site phimsea.com
python phim-dl.py --site phimsea.com          # shows which account is logged in there
```

The site keeps an account signed in in **one place at a time**, so logging in here may sign you out
in your browser (and the other way round). To share your browser's session instead, export the cookies
of the site to a `cookies.txt` file with a browser extension (for example
"Get cookies.txt LOCALLY") and run:

```bash
python phim-dl.py --cookies cookies.txt                     # phimkey.com
python phim-dl.py --cookies cookies.txt --site phimsea.com  # a clone site
```

Keep `session.txt` and `cookies.txt` private: anyone who has them can use your account.
`python phim-dl.py --logout` (with `--site` for a clone) forgets the saved session of that site.

## Use

```bash
python phim-dl.py "https://phimkey.com/title/the-odyssey~Vk8k"
```

| Command | What it does |
|---|---|
| `python phim-dl.py <link>` | Download the movie, or every episode of a series |
| `python phim-dl.py "https://phimsea.com/title/..."` | Links of the clone sites work the same way |
| `python phim-dl.py <link> --info` | Only show the quality, size and bitrate on each server |
| `python phim-dl.py <link> -S 2` | Only season 2 of a series (`1-2` = seasons 1 and 2) |
| `python phim-dl.py <link> -e 1-5,8` | Only episodes 1 to 5 and 8 of each season (`10-` = from 10 to the end) |
| `python phim-dl.py <link> --list` | Only list the seasons and episodes |
| `python phim-dl.py <link> --subs all` | Subtitles in every language (`vi,en` by default, `none` = no subtitles) |
| `python phim-dl.py <link> -s 2` | Use server 2 first (server 1 by default) |
| `python phim-dl.py <link> -o D:\Movies` | Save to another folder |
| `python phim-dl.py <link> -j 3` | Download 3 episodes at a time (default 2) |
| `python phim-dl.py <link> --lang vi` | Show messages in Vietnamese |

Links of a movie or series page (`/title/...`) and of a watch page (`/watch/...`) both work;
a watch page of an episode downloads only that episode.

Files are saved in `downloads/<title> (<year>)/`, named like `Movie.Name.2026.mp4` for a movie
and `Series.Name.S01E01.mp4` for an episode. Subtitles get the same name with the language:
`Movie.Name.2026.vi.srt`, so video players load them automatically.

Check before downloading:

```
  Server   Quality             Size            Bitrate     Length      Host
  ────────────────────────────────────────────────────────────────────────────────
  1        1080p (1920x1080)   ~4.8 GB         3.9 Mbps    2:52:35     t1.kingcdn.xyz  ← used by default
  2        1080p (1920x1080)   ~4.5 GB         3.7 Mbps    2:52:35     f3.kingcdn.xyz
  3        1080p (1920x1080)   ~4.5 GB         3.7 Mbps    2:52:35     f3.kingcdn.xyz
```

While downloading:

```
  ⠹ S01E01      ━━━━━━━━━━━━━━━━━╸━━━━━━━━━━━━    58.3%    392.9 MB / 673.3 MB     17.4 MB/s   ETA   00:16
  ⠼ S01E02      ━━━━━━╸━━━━━━━━━━━━━━━━━━━━━━━    21.0%    141.1 MB / 674.2 MB     17.1 MB/s   ETA   00:31
  ───────────────────────────────────────────────────────────────────────────────────────────────────────
  Σ Total 1/4   ━━━━━━━━━━━━━╸━━━━━━━━━━━━━━━━    44.8%      1.2 GB / 2.7 GB       34.4 MB/s   ETA   00:44
```

Messages are in **English** by default (Vietnamese if your system language is Vietnamese).
Use `--lang en` or `--lang vi` to choose. Use `--no-color` (or set `NO_COLOR=1`) for plain text.

Run `python phim-dl.py -h` to see all options.

## Failed downloads

If an episode can't be downloaded, it is shown in red with the reason, and listed in **`failed.txt`**
in the movie folder (episode, link and reason). Run the same command again to retry: finished
episodes are skipped and unfinished ones continue where they stopped. `failed.txt` is removed once
nothing fails.

## Problems?

- **`python` is not recognized:** reinstall Python and tick **Add python.exe to PATH**
- **"Missing library":** run `python -m pip install -r requirements.txt` again
- **"You are not logged in" / "The session has expired":** run `python phim-dl.py --login` again
  (or export fresh cookies). This also happens after you log in somewhere else.
- **"This account cannot watch movies right now":** check your account's plan on the site
- **Slow downloads:** put `aria2c.exe` next to `phim-dl.py`. The video servers are slow per connection,
  so many segments are downloaded at once (32 with aria2c). Try `-n 48`, another server (`-s 2`), or
  `-j 3` for a series. The speed of your own connection is the upper limit
- **Ctrl+C:** stops right away. Run the same command again to continue

## Contributing

Bug reports and pull requests are welcome. Changes that get past captchas, paywalls or other
protections will not be accepted.

- **Report a bug:** open an [issue](https://github.com/loooveee/phim-dl/issues) with the command
  you ran, the error text (or `failed.txt`), your OS and `python --version`. Never paste your
  `session.txt`, cookies or password.
- **Send a fix:** fork the repo, create a branch, commit, push it to your fork, then open a pull request.
  Say what you changed, why, and how you tested it.
- **Rules:**
  - Keep everything in `phim-dl.py`. The docstring at the top has a code map.
  - Write every message in both languages: `t("English", "Tiếng Việt")`.
  - Use the color helpers (`green()`, `red()`...) instead of raw ANSI codes.

## Disclaimer

**This is for educational purposes only.** You are responsible for how you use it.
Respect copyright and the website's terms of use.

This project is not affiliated with phimkey.com or any of its clone sites. The author is not responsible for any misuse.

## License

MIT © loooveee
