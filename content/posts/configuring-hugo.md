---
title: "Configuring Hugo"
date: 2019-04-08T23:10:37+01:00
draft: false
tags: ['hugo', 'css']
---


# Changing the logo

Firstly to change your logo, put your svg in ```static/images/logo.svg```. I used an png and put it through an online svg converter.

# Custom CSS

To make a custom css file, we need to overwrite the css of the template. We don't want delete the template of course.

Run `grep -r 'css' ./themes/cupper-hugo-theme/layouts` from your hugo root dir.

With this theme I got:
```
./themes/cupper-hugo-theme/layouts/partials/katex.html:<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.10.1/dist/katex.min.css" integrity="sha384-dbVIfZGuN1Yq7/1Ocstc1lUEm+AT+/rCkibIcC/OmWo5f0EA48Vf8CytHzGrSwbQ" crossorigin="anonymous">
./themes/cupper-hugo-theme/layouts/partials/head.html:  <link rel="stylesheet" href="{{ "css/prism.css" | absURL }}" media="none" onload="this.media='all';">
./themes/cupper-hugo-theme/layouts/partials/head.html:  {{ $templateStyles := resources.Get "css/template-styles.css" }}
./themes/cupper-hugo-theme/layouts/partials/head.html:  {{ $styles := $templateStyles | resources.ExecuteAsTemplate "css/styles.css" . }}
./themes/cupper-hugo-theme/layouts/partials/head.html:  <link rel="stylesheet" type="text/css" href="{{ $styles.Permalink }}">
./themes/cupper-hugo-theme/layouts/partials/head.html:  <link rel="stylesheet" type="text/css" href="{{ $custom.Permalink }}">
```

It looks like the stylesheet is being referenced in the ```head.html``` file. The css referenced is ``css/styles.css``. This looks like out main template style sheet. So we must reference our css after this.


If you look above at this line ```{{ $styles := $templateStyles | resources.ExecuteAsTemplate "css/styles.css" . }}```. This is some logic to assign a something to a variable ```styles```.

Initially I changed the ```css/styles.css``` to ```css/custom.css``` and made a put my own ```custom.css``` in ```static/css/```. To be honest, I totally guessed at this location. The whole page broke - something is changing.


Instead of replacing, add this line

```
{{ $custom := $templateStyles | resources.ExecuteAsTemplate "css/custom.css" . }}
```

To this file ```./themes/cupper-hugo-theme/layouts/partials/head.html```. Place it just below the other line.

Hey! It works!

**Update 30/04/19**
---

Ok now this doesn't work! You can simply add this line to make it work. I think custom.css might be a reserved word, so I changed it to custom1.css. Add this to the head.html file.

```
  <link rel="stylesheet" type="text/css" href="{{"css/custom1.css" | absURL }}" media="none" onload="this.media='all';">
```

Delete the previous lines that this posts suggests

# Fixing the link on the logo

Change line 4 in ```header.html```, which is:

```
<a class="logo" href="/" aria-label="{{ .Site.Title }} home page">
```

to this:

```
<a class="logo" href="{{ .Site.BaseURL }}" aria-label="{{ .Site.Title }} home page">
```

This fixes the broken link on the logo (going to the home page)

# Add the date on the posts on blog page

Add the following two lines to ```list.html```

```
{{ $dateFormat := $.Site.Params.dateFormat | default "Jan 2, 2006" }}
<h4 id='blog-date' class='date'>{{ .PublishDate.Format $dateFormat   }}</h4>
```

Add the first line before the loop, add the second under the title

Then add this to your custom css:
```
@import url('https://fonts.googleapis.com/css?family=IBM+Plex+Mono');
#blog-date {

    font-size: 20px;font-family: 'IBM Plex Mono', monospace;
    font-weight: bold;
    font-style: italic;
}
```