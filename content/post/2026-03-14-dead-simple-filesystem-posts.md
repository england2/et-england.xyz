+++
title = "Saving links to webpages the Unix way"
date = 2026-03-14
slug = "dead-simple-filesystem-links"
+++

When I take notes on a technical article I make a new directory with example code to experiment with.
In these situations, it's useful to keep the link to the article in this same directory.

Here's a script that makes a special 'weblink' file, which can be opened later.

```bash
# file: ~/bin/mkwl ("make web link")
#!/usr/bin/env fish
source /home/t/.config/fish/global.fish

set url $argv[1] 
set filename \
    (string sub -s 3 -e 66 \
    (string replace 'http:' '' \
    (string replace 'https:' '' \
    (string replace --all '/' '_' $url)))).weblink

echo "$filename"
echo $url > $filename
```

So to create a weblink we just do

```txt
$ mkwl https://medium.com/@siacavazzi/how-i-made-a-robust-web-scraper-d17060470bd8
```

And we get


```txt
$ ls
'medium.com_@siacavazzi_how-i-made-a-robust-web-scraper-d17060470.weblink'
```

And now we need a script to open it

```bash
# file: ~/bin/owl ("open web link")
#!/usr/bin/env fish
xdg-open (cat $argv[1] | head -n1) >/dev/null 2>&1 &
disown
```

### Make it official

Here's how we can web links recognized by the OS on Linux!

We need a desktop entry

```txt
# file: ~/.local/share/applications/weblink.desktop
[Desktop Entry]
Type=Application
Name=Open Web Link
Exec=/home/t/bin/owl %f
MimeType=application/x-weblink;
NoDisplay=true
Terminal=false
```

And a mime type

```txt
# file: ~/.local/share/mime/packages/weblink.xml
<?xml version="1.0" encoding="UTF-8"?>
<mime-info xmlns="http://www.freedesktop.org/standards/shared-mime-info">
  <mime-type type="application/x-weblink">
    <comment>Web link</comment>
    <glob pattern="*.weblink"/>
  </mime-type>
</mime-info>
```


<br>
    
### Demo

<br>

Here's a demo showing off this next-level, earth-shattering computer science discovery.

(I just think gifs are cool, okay?)

![Animated demo of opening a `.weblink` file](/images/transparent.gif)
