+++
comments = true
date = "2015-03-14"
draft = false
image = ""
menu = ""
share = true
slug = "put-a-tele-lens-on-that-phaser"
tags = ["game", "development", "es6", "javaScript", "phaser", "guide", "tutorial"]
title = "Put a Tele Lens on that Phaser"
excerpt = 'I’ve been working with Phaser for a while now and I feel comfortable with the framework. However, recently I was making a game where need the camera to zoom in and out. That’s when I realized Phaser Camera does not have zoom functionality!!!'

+++

<p class="graf--p" style="text-align: justify;">
  <div class="alert red">
    This article was written based on Phaser 2.3, some of the information regarding the framework might not be applicable on later version of Phaser.
  </div>
</p>

<p class="graf--p" style="text-align: justify;">
  I’ve been working with <a class="markup--anchor markup--p-anchor" href="http://phaser.io" data-href="http://phaser.io">Phaser</a> for a while now and I feel comfortable with the framework. Its API is pretty decent and simple, very easy to learn and performs well on mobile devices.
</p>

<blockquote class="graf--pullquote pullquote">
  <p>
    <strong class="markup--strong markup--pullquote-strong">It was smooth and happy for me.</strong>
  </p>
</blockquote><figure class="graf--figure">

<div class="aspectRatioPlaceholder is-locked">
  <p>
    <img class="graf-image aligncenter" src="https://cdn-images-2.medium.com/max/800/1*9BZY9WvFkXcqcL7VaWUpnQ.jpeg" alt="" width="640" height="460" data-image-id="1*9BZY9WvFkXcqcL7VaWUpnQ.jpeg" data-width="640" data-height="460" />
  </p>
</div></figure>

<p class="graf--p" style="text-align: justify;">
  However, recently I was making an action/JRPG game. I wish to utilize camera movement/zooming in order to create better effect and experience. That’s when I realized…
</p>

<blockquote class="graf--pullquote pullquote">
  <p>
    <strong class="markup--strong markup--pullquote-strong">Phaser Camera does not have zoom functionality!!!</strong>
  </p>
</blockquote><figure class="graf--figure">

<div class="aspectRatioPlaceholder is-locked">
  <img class="graf-image" src="https://cdn-images-2.medium.com/max/800/1*ugmDwJ--aLIGUkJfvghjng.jpeg" alt="" data-image-id="1*ugmDwJ--aLIGUkJfvghjng.jpeg" data-width="500" data-height="418" />
</div></figure>

<p class="graf--p" style="text-align: center;" data-align="center">
  <div class="alert gray">
    NO ZOOM? It’s a freaking camera ☹
  </div>
</p>

<p class="graf--p">
  So I <a class="markup--anchor markup--p-anchor" href="https://www.google.com/search?q=phaser+camera+zoom" data-href="https://www.google.com/search?q=phaser+camera+zoom">googled</a> for some work around but none of the implementations looks cool to me. Their codes look just…messy and complicated. I just thought, why does it have to be complicated? So I decided to make my hands do some dirty work.
</p><figure class="graf--figure">

<div class="aspectRatioPlaceholder is-locked">
  <div class="aspect-ratio-fill">
  </div>

  <p>
    <img class="graf-image aligncenter" src="https://cdn-images-2.medium.com/max/800/1*bm8JsSkJElRo8FQUw_ulJQ.png" alt="" data-image-id="1*bm8JsSkJElRo8FQUw_ulJQ.png" data-width="1500" data-height="1175" data-action="zoom" data-action-value="1*bm8JsSkJElRo8FQUw_ulJQ.png" />
  </p>
</div></figure>

<p class="graf--p">
  My way is simple: make a group and throw what ever I want to see on the camera in it and scale it when I wanna zoom the camera.
</p>

<p class="graf--p">
  You may ask: why not scale the world? I just don’t feel comfortable with doing whatever to the world at all.
</p><figure class="graf--figure">

<div class="aspectRatioPlaceholder is-locked">
  <blockquote>
    <div class="aspect-ratio-fill">
      Don’t you see what humanity have done to this world?
    </div>
  </blockquote>

  <p>
    <img class="graf-image aligncenter" src="https://cdn-images-2.medium.com/max/800/1*KETM5xZ7HStW-Cio9zvpng.png" alt="" data-image-id="1*KETM5xZ7HStW-Cio9zvpng.png" data-width="215" data-height="235" />
  </p>
</div></figure>

<p class="graf--p">
  So this is what I came up with:
</p>

    class Camera extends Phaser.Group {
        constructor() {
            ...
            this.scale.setTo(1);
            ...
        }
        zoomTo(scale) {
            this.scale.setTo(scale)
        }
    }

> Aha, ain&#8217;t that easy?
>
> Yes it is.
>
> Does it work?
>
> Not so fast.<figure class="graf--figure">

<div class="aspectRatioPlaceholder is-locked">
  <div class="aspect-ratio-fill">
  </div>

  <p>
    <img class="graf-image" src="https://cdn-images-2.medium.com/max/800/1*VJMB7ZisBUqsrMWEOkfU1g.png" alt="" data-image-id="1*VJMB7ZisBUqsrMWEOkfU1g.png" data-width="284" data-height="177" />
  </p>
</div></figure>

<p class="graf--p">
  When zooms out and pans, the camera pans outside of the group.
</p><figure class="graf--figure">

<div class="aspectRatioPlaceholder is-locked">
  <div class="aspect-ratio-fill">
  </div>

  <p>
    <img class="graf-image" src="https://cdn-images-2.medium.com/max/800/1*bqUaZHg_1FCVjBqJI1ws9Q.png" alt="" data-image-id="1*bqUaZHg_1FCVjBqJI1ws9Q.png" data-width="1004" data-height="279" data-action="zoom" data-action-value="1*bqUaZHg_1FCVjBqJI1ws9Q.png" />
  </p>
</div></figure>

<p class="graf--p">
  That is because as default, the camera bounds is set to the size of the world, when the group scales, the bounds should also be scaled and repositioned.
</p>

    constructor() {
        ...
        this.bounds = Phaser.Rectangle.clone(game.world.bounds);
        ...
    }
    zoomTo(scale) {
        var bounds       = this.bounds;
        var cameraBounds = game.camera.bounds;
        cameraBounds.x      = bounds.width  * (1 - scale) / 2;
        cameraBounds.y      = bounds.height * (1 - scale) / 2;
        cameraBounds.width  = bounds.width  * scale;
        cameraBounds.height = bounds.height * scale;
        ...
    }

<p class="graf--p">
  Finally, I added the ability to zoom slowly, because people like it smooth.
</p>

    zoomTo(scale, duration) {
        ...
        if (!duration) {
            ...
        } else {
            game.add.tween(cameraBounds).to({
                x      : bounds.width  * (1 - scale) / 2,
                y      : bounds.height * (1 - scale) / 2,
                width  : bounds.width  * scale,
                height : bounds.height * scale
            }, duration).start();
            return game.add.tween(this.scale).to({
                x: scale, y: scale
            }, duration).start();
        }
    }

<p class="graf--p">
  And then I had…
</p><figure class="graf--figure graf--iframe">

<div class="iframeContainer">
</div></figure>

<div class="iframeContainer">
  <div class="alert blue">
    SO WHAT&#8217;S NEXT?
  </div>
</div>

<div class="iframeContainer">
  See how smoothly the camera is chasing the main character in the video? Check it out in <a href="http://anhnt.ninja/2015/08/19/a-stalker-on-phaser/">A Stalker on Phaser</a>. Stay tune <img src="http://i1.wp.com/anhnt.ninja/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" data-recalc-dims="1" />
</div>