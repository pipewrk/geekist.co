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

We downloaded on ***Kbps***. Let that sink in for a moment.

![A vintage Windows download dialog box showing a file with 0.49% completed and an estimated time left of 39 years.](https://cdn.hashnode.com/res/hashnode/image/upload/v1752742190790/db8c4865-b9c9-4f0f-894b-fe93ad0b1347.avif align="center")

That meant a movie would take an entire day if you were lucky. If not, you’d wait a couple of days at least (and pray the landline didn’t start ringing!).

When I was a teen, you could grab a pirated copy of just about anything via torrents or apps like Kazaa, LimeWire, or Napster, but those weren’t ever meant to last anyway.

In this article, we’ll explore new ways to download videos, images, and music with the resurgence of some clever CLI tools and a little tinkering.

You might have heard about `youtube-dl`, once the undisputed YouTube downloader, only to be dethroned by a newer tool called `yt-dlp`. But there are options available now. Some don’t even need you to download anything and instead allow you to directly stream media onto your favorite media app.

Let’s take a look at five standout tools and the unique tricks each one brings to the table.

## Download Everything With [`yt-dlp`](https://github.com/yt-dlp/yt-dlp)

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

All that and we’re still only just scratching the surface. It can handle playlists, metadata, cookies, rate-limits, and even merge video and audio streams with FFmpeg under the hood.

That said, if you’re not into downloading anything and you just want to watch a video straight from the source, right inside your media player app, there’s a tool for that too!

## Stream It Like You Stole It With [`you-get`](https://github.com/soimort/you%E2%80%91get)

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

If you like the simplicity of `you-get` but want something faster, with more control over formats and playlists, there’s a tool that fits that profile perfectly.

Let’s take a look at `annie`.

## Speed & Simplicity With [`annie`](https://github.com/qinwang/annie)

Built in Go and made for speed, `annie` is a no-nonsense media downloader that covers most of the popular platforms, just like the others. It doesn’t try to be fancy. It just works, and it’s fast!

To install:

```bash
brew install annie
```

The default usage is exactly what you’d expect:

```bash
annie https://youtu.be/dQw4w9WgXcQ
```

But `annie` really shines when you want to preview available formats before downloading:

```bash
annie -i https://youtu.be/dQw4w9WgXcQ
```

This gives you a clean, numbered list of all available video and audio streams, including resolution and size. Once you pick the one you want, just specify it:

```bash
annie -f 18 https://youtu.be/dQw4w9WgXcQ
```

It also handles full playlists with a simple flag:

```bash
annie -p https://www.bilibili.com/bangumi/play/ep198061
```

`annie` doesn’t panic when the Wi-Fi hiccups. It just picks up where it left off like nothing happened, and it splits files into chunks for faster downloading. It’s quick and gets the job done. If you want something faster than `you-get` but leaner than `yt-dlp`, `annie` strikes a solid balance.

Now, if you’re curious about something newer, with clean subcommands and stream decryption built in, then it’s worth checking out `rustube-cli`.

## Clean & Modern With [`rustube-cli`](https://github.com/DzenanJupic/rustube)

If you’re the kind of person who appreciates tools that feel like they were built this decade (instead of something that still thinks Flash is relevant), `rustube-cli` might be right up your alley. Written in Rust and designed to be simple but robust, it gives you just the essentials: clean command structure and reliable stream decryption for YouTube.

You can install it via Cargo:

```bash
cargo install rustube-cli
```

Once installed, downloading a video is as straightforward as with all the other tools:

```bash
rustube fetch https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

Want to see what stream qualities are available before downloading anything? Use `check`:

This will show you all available streams, including resolution and codec info, so you can make an informed choice without wasting time or bandwidth.

```bash
rustube check https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

You don’t need to memorize obscure flags here. `rustube-cli` keeps things clean by using subcommands instead of arguments, which makes it much easier to read and reason about.

It’s a younger project, and still growing, but it’s fast and thoughtful. Even though it currently only supports YouTube, it doesn’t come with the overhead that bigger tools carry.

But what if you’re after images instead? Twitter threads, art sites, galleries, basically stuff you can’t just “right-click and save”? There’s a tool for that too.

## Download Images Like a Wizard With [`gallery-dl`](https://github.com/mikf/gallery-dl)

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

In many ways, they’re more reliable than anything we had back then.

Hopefully you found something new in this lineup that you didn’t know you needed. If this kind of nerdy deep dive is your thing, consider subscribing. There are more posts like this already in the pipeline.