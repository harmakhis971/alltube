# Frequently asked questions

<!-- markdownlint-disable MD026 -->

## My browser plays the video. How do I download it?

Most recent browsers automatically play a video
if it is a format they know how to play.
You can ususally download the video by doing *File > Save to* or *ctrl + S*.

## How do I change config parameters?

You need to create a YAML file called `config.yml` in the `config/` folder.

See [`config.example.yml`](../config/config.example.yml)
for a list of parameters you can set and their default value.

## How do I enable audio conversion?

In order to enable audio conversion, you need to add this to your `config.yml` file:

```yaml
convert: true
avconv: path/to/avconv
```

You will also need to install `avconv` on your server:

```bash
sudo apt-get install libav-tools
```

## How do I deploy AllTube on Heroku?

Create a dyno with the following buildpacks:

* `heroku/php`
* `heroku/nodejs`
* `heroku/python`

You might also need to add the following config variables:

```env
CONVERT=1
PYTHON=/app/.heroku/python/bin/python
```

Then push the code to Heroku and it should work out of the box.

## Why can't I download videos from some websites (e.g. Dailymotion)

Some websites generate an unique video URL for each IP address.
When using AllTube, the URL is generated for our server's IP address
and your computer is not allowed to use it.

There are two known workarounds:

* You can run AllTube locally on your computer.
* You can enable streaming videos through the server (see below).
  Please note that this can use a lot of resources on the server
  (which is why we won't enable it on alltubedownload.net).

## CSS and JavaScript files are missing

You probably don't have the minified files (in the `dist` folder).
You need to either:

* Use a [release package](https://github.com/Rudloff/alltube/releases)
* Run `yarn install` (see detailed instructions in the [README](../README.md#from-git))

## I get a 404 error on every page except the index

This is probably because your server does not have mod_rewrite
or AllowOverride is disabled.
You can work around this by adding this to your `config.yml` file:

```yaml
uglyUrls: true
```

## How do I enable streaming videos through the server?

You need to add this to your `config.yml` file:

```yaml
stream: true
```

Note that this can use a lot of ressources on your server.

## I want to download M3U videos

You need to enable streaming (see above).

## The downloaded videos have a strange name like `videoplayback.mp4`

AllTube can rename videos automatically if you enable streaming (see above).

## I want to download a video that isn't available in my country

If the video is available in the server's country,
you can download it if you enable streaming (see above).

## How do I run the Docker image?

```bash
docker run -p 8080:80 rudloff/alltube
```

## How do I run Heroku locally?

You should be able to use `heroku local` like this:

```bash
sudo APACHE_LOCK_DIR=. APACHE_PID_FILE=./pid APACHE_RUN_USER=www-data \
    APACHE_RUN_GROUP=www-data APACHE_LOG_DIR=. \
    heroku local
```

You might need to create some symlinks before that:

```bash
ln -s /usr/sbin/apache2 /usr/sbin/httpd
ln -s /usr/sbin/php-fpm7.0 /usr/sbin/php-fpm
```

And you probably need to run this in another terminal
after `heroku local` has finished launching `php-fpm`:

```bash
chmod 0667 /tmp/heroku.fcgi.5000.sock
```

## How can I download 1080p videos from Youtube?

Youtube distributes HD content in two separate video and audio files.
So AllTube will offer you video-only and audio-only formats in the format list.

You then need to merge them together with a tool like ffmpeg.

You can also enable the experimental remux mode
that will merge the best video and the best audio format on the fly.

## I want to convert videos to something other than MP3

By default the `convert` option only allows converting to MP3,
in order to keep things simple and ressources usage low.
However, you can use the `convertAdvanced` option like this:

```yaml
convertAdvanced: true
convertAdvancedFormats: [mp3, avi, flv, wav]
```

This will add new inputs on the download page
that allow users to converted videos to other formats.
