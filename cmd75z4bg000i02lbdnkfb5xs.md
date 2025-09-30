---
title: "The 5 Best Downloaders for Videos, Music & Images (No Browser Needed)"
seoDescription: "Download videos, music, and images without a browser using 5 powerful command-line tools. A nostalgic yet practical guide for developers and tinkerers."
datePublished: Thu Jul 17 2025 09:04:40 GMT+0000 (Coordinated Universal Time)
cuid: cmd75z4bg000i02lbdnkfb5xs
slug: 5-best-downloaders-for-music-videos-images-no-browser
canonical: https://geekist.co/5-best-downloaders-for-music-videos-images-no-browser/
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1752742231970/26fb83f7-81c9-4d6d-8780-4a350de4cebe.avif
tags: productivity, python, terminal, opensource, automation, rust, developer-tools, cli-tools, open-source-software, open-source-community, productivity-tools, python-cli, youtube-downloaders, geek-culture, media-downloading

---

Before YouTube, Spotify, and Netflix dominated the internet, the only way we could get our hands on videos and music was to download them. Also, internet speeds weren’t ever measured in **Mbps** or **Gbps**.

We downloaded on **_Kbps_**. Let that sink in for a moment.

![A vintage Windows download dialog box showing a file with 0.49% completed and an estimated time left of 39 years.](https://geekist.co/wp-content/uploads/dialup-1-480x320.avif)

That meant a movie would take an entire day if you were lucky. If not, you'd wait a couple of days at least (and pray the landline didn't start ringing!).

When I was a teen, you could grab a pirated copy of just about anything via torrents or apps like Kazaa, LimeWire, or Napster, but those weren’t ever meant to last anyway.

In this article, we'll explore new ways to download videos, images, and music with the resurgence of some clever CLI tools and a little tinkering.

You might have heard about `youtube-dl`, once the undisputed YouTube downloader, only to be dethroned by a newer tool called `yt-dlp`. But there are options available now. Some don't even need you to download anything and instead allow you to directly stream media onto your favorite media app.

Let’s take a look at five standout tools and the unique tricks each one brings to the table.

## Download Everything With [yt-dlp](https://geekist.co/github/yt-dlp-an-audio-video-downloader/)

Let’s start with the tool that took the crown from `youtube-dl`.

`yt-dlp` is what most folks (not just the cool kids who wear sunglasses indoors) reach for these days when they want to download videos from YouTube or pretty much any popular media site.

It’s fast, reliable, supports thousands of platforms, and has so many flags you’ll spend more time reading flags than watching the actual video.

Here’s are some quick install instructions (if you’re on macOS or Linux):

```bash
brew install yt-dlp
# or for the hardcore
pip install -U yt-dlp
```

Now say you want to download an entire YouTube channel. That’s easy:

```bash
yt-dlp https://www.youtube.com/@beetlejuicearchives3490
```

Want just the audio in MP3?

```bash
yt-dlp -x --audio-format mp3 https://www.youtube.com/@beetlejuicearchives3490
```

Here’s where things get wild: `yt-dlp` also integrates with `SponsorBlock`. That means it can automatically skip over sponsored segments in videos. You don’t have to do anything fancy, just pass a flag:

```bash
yt-dlp --sponsorblock-remove sponsor https://www.youtube.com/watch?v=hH2I8rR07aM
```

You can also sort and filter download formats with fine-grained control. Want only the highest-quality MP4 with AAC audio?

```bash
yt-dlp -f "bv[ext=mp4][height<=1080]+ba[ext=m4a]" https://www.youtube.com/watch?v=xyz
```

All that and we're still only just scratching the surface. It can handle playlists, metadata, cookies, rate-limits, and even merge video and audio streams with FFmpeg under the hood.

That said, if you're not into downloading anything and you just want to watch a video straight from the source, right inside your media player app, there's a tool for that too!

## Stream It Like You Stole It With [you-get](https://geekist.co/github/you-get-dumb-downloader-that-scrapes-the-web/)

That’s exactly what `you-get` does. It pulls media from sites like YouTube, Vimeo, Twitter, and others, and streams it directly into apps like `MPV` or `VLC` without leaving the terminal.

Here’s how to install it:

```bash
brew install you-get
# or if you're managing Python packages manually
pip install -U you-get
```

Now, say you’ve found a video and just want it to play instantly:

```bash
you-get --player vlc https://www.youtube.com/watch?v=jNQXAC9IVRw
```

This hands the video off directly to `VLC`, and the stream begins, no file clutter and no fussing with browser extensions. Most of all, no more mystery files named `video_final_FINAL_v2(1).mp4` cluttering your desktop. You treat it like a streaming service shortcut.

Of course, for the control-freaks among us, yes, you can still save the file.

```bash
you-get https://twitter.com/username/status/1234567890
```

Or inspect the available formats before deciding:

```bash
you-get -i https://vimeo.com/12345678
```

We’ll also cover a music-first workflow that keeps your Spotify playlists mirrored on disk automatically.

Let’s take a look at `spotDL`.

## **Sync Your Music Library With** [spotDL](https://geekist.co/github/spotdl-download-spotify-playlists/)

If you want a music-first workflow that stays in sync with your playlists, spotDL is the right tool. Point it at a Spotify playlist (yours or public), and it fetches matching audio from YouTube/YouTube Music, writes proper metadata/cover art, and - crucially - keeps things updated with an incremental sync file.

To install (pick one):

```bash
# Recommended
pipx install spotdl
# or:
pip install spotdl
```

Unlike the other tools here, spotDL does more than grab a single file: it mirrors whole playlists and keeps them in sync. That means the first command looks a little busier, but it’s because you’re creating a sync file that defines the playlist on disk. After that, updates are as simple as re-running one command.

Initialize a playlist sync (creates a .spotdl file) and download:

```bash
# Create a real sync file + download into ./dubstep-favourites/
spotdl sync "https://open.spotify.com/playlist/27uRJq4FVTL1zAZ8DdoZJr" \
  --save-file dubstep-favourites.spotdl \
  --output "dubstep-favourites/{artist} - {title}.{ext}" \
  --m3u "dubstep-favourites.m3u8"
```

• `--save-file` creates a .spotdl sync file (must end with .spotdl).  
• `--output` controls how filenames are written (here: “Artist – Title”).  
• `--m3u` auto-generates a playlist file for media players.

Think of this like “snapshotting” your Spotify playlist into a folder. That's why you'd find the next command useful, it incrementally syncs this playlist to disk:

```bash
spotdl sync dubstep-favourites.spotdl \
  --output "dubstep-favourites/{artist} - {title}.{ext}" \
  --m3u "dubstep-favourites.m3u8"
```

Now it only pulls new/changed tracks. No need to re-enter the playlist URL.

`SpotDL` takes care of your music collection and keeps it neat, but what if you’re after something more immediate - like catching a Twitch stream as it happens, or saving a broadcast while you watch?

That’s where `streamlink` comes in.

## Stream Live or Save It With [streamlink](https://geekist.co/github/streamlink-pipe-video-streams-to-your-player/)

Not everything you want to grab comes as a neat file with a download link. A lot of media online today (especially live events) is served as HLS or DASH streams. That's where `streamlink` comes in.

It supports a long list of services via maintained plugins (e.g. YouTube, Twitch, BBC iPlayer, Vimeo, TikTok). Use it when you’d rather play or capture the stream, rather than fully “download” via an extractor.

You can install it with `pip` or `brew`:

```bash
brew install streamlink
# or:
pipx install streamlink
```

Play a stream instantly:

```bash
streamlink "https://www.youtube.com/watch?v=jNQXAC9IVRw" best
```

The `best` keyword automatically picks the highest quality available, though you can choose 720p, 480p, or even audio if you want to save bandwidth.

But why stop there? Why not record it while watching? Remember those old cable boxes that came with a built-in Harddisk that recorded your favorite TV show so you can watch it a hundred times over later (and bore the life out of your cat)?

```bash
streamlink --record "~/Videos/{author}/{title}-{time:%Y%m%d%H%M%S}.ts" \
  -p mpv "https://twitch.tv/day9tv" best
```

Yes. Yes, of course. You don't need to _watch_ it to have a copy:

```bash
streamlink --output "~/Videos/{author}/{title}-{time:%Y%m%d%H%M%S}.ts" \
  "https://twitch.tv/day9tv" best
```

But that would mean you might miss the stream going live at 11pm tonight, wouldn’t it? For that, there’s `at`:

```bash
brew install at
brew services start atd
```

👉 _macOS doesn’t ship with at/atd anymore; Apple moved to launchd. Homebrew’s `atd` gets you the classic one-shot scheduler back._

And that means both you (and the cat) get your shut-eye at 11pm and still have a copy tucked away for later:

```bash
echo 'streamlink --record ~/Videos/{title}.ts https://twitch.tv/day9tv best' | at 23:00
```

> 💡 Want it to stop after 2 hours?

```bash
brew install coreutils   # provides `gtimeout` on macOS
echo 'gtimeout 7200 streamlink --record ~/Videos/{title}.ts https://twitch.tv/day9tv best' | at 23:00
```

And yep, it works for `yt-dlp` jobs too. So we’ve covered media downloads (YouTube with `yt-dlp`, Spotify with `spotDL`) and now live streams on demand with streamlink.

What about images? Enter `gallery-dl`.

## Download Images Like a Wizard With [gallery](https://geekist.co/github/gallery-dl-download-image-galleries-collections/)[-dl](https://github.com/mikf/gallery-dl)

If `yt-dlp` is the multitool for video, then `gallery-dl` is more like a precision instrument for images. It’s not just for generic image URLs either; it actually understands how full galleries work on sites like Twitter, Reddit, DeviantArt, Pixiv, Danbooru, and even Tumblr.

Here’s the quick install:

```bash
brew install gallery-dl
# or for the hardcore
pip install -U gallery-dl
```

Want to download every image from a specific Reddit post?

```bash
gallery-dl https://www.reddit.com/r/EarthPorn/comments/abc123
```

Or maybe you’ve found an artist’s full portfolio on Twitter?

```bash
gallery-dl https://twitter.com/username
```

You can even go full hacker mode and just extract the direct image URLs without downloading them:

```bash
gallery-dl --get-urls https://twitter.com/username/status/1234567890
```

This prints out the raw image links to your terminal so you can inspect and pass them into another script or tool. It’s great if you’re building an automation pipeline or if you just want to see what’s actually behind a page without committing to a full download.

There’s full config support too. Want everything downloaded into folders named by artist and source? Done. Want to skip posts that don’t match a certain tag, language, or resolution? You can do that. It’s almost scary how much you can tweak.

Here’s a taste of what a config file might look like (totally optional, but quite powerful):

```json
{
  "base-directory": "~/Pictures/gallery",
  "twitter": {
    "cookies": "twitter-cookies.txt",
    "username": "your_username"
  },
  "output": {
    "mode": "tagged"
  }
}
```

You might be collecting memes or just downloading reference material, whatever your image downloading needs are, `gallery-dl` handles it all surprisingly easy.

---

Back in the day, apps like Kazaa and LimeWire made things feel easy. You searched, you clicked, and the download started (eventually).

There was a kind of thrill to it, even though it was very likely the file turned out to be mislabeled or corrupted.

These CLI tools might not have the glossy buttons or cartoonish logos, but for developers, tinkerers, and anyone who lives in the terminal, they’ve become indispensable. They’re faster and definitely more flexible.

In many ways, they're more reliable than anything we had back then.

Hopefully you found something new in this lineup that you didn’t know you needed. If this kind of nerdy deep dive is your thing, consider subscribing. There are more posts like this already in the pipeline.