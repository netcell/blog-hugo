+++
comments = true
date = "2015-03-14"
draft = false
image = "img/bg.jpg"
menu = ""
share = true
slug = "put-a-tele-lens-on-that-phaser"
tags = ["game", "development", "es6", "javaScript", "phaser", "guide", "tutorial"]
title = "Put a Tele Lens on that Phaser"
excerpt = 'I realized the Camera class of the HTML5 game engine Phaser does not have zoom functionality, so I decided to build another camera that does, using a Group to simulate the scale and motion.'

+++

**This article was written based on Phaser 2.3, some of the information regarding the framework might not be applicable on later version of Phaser.**

I’ve been working with the HTML5 game engine [Phaser](http://phaser.io). Its API is pretty decent and simple, very easy to learn and performs well on mobile devices. It was *smooth* and happy for me.

However, recently while making an top-down action JRPG game, I wished to utilize camera zooming and movement in order to deliver better effect and experience. That’s when I realized `Phaser.Camera` *does not have zoom functionality!*
{{< figure src="/img/1-ugmDwJ--aLIGUkJfvghjng.jpeg" title="NO ZOOM? It’s a freaking camera!" >}}

So I [googled](https://www.google.com/search?q=phaser+camera+zoom) for some work around but the implementations are too messy and complicated for me. So I decided to make a DIY project.

---

My method is simple:

1. Make a `Phaser.Group`, call it `Camera`.
{{< highlight js "linenos=inline" >}}
    class Camera extends Phaser.Group {
    }
{{< /highlight >}}
2. Throw what I want to see on in it.
3. Scale it when I want to zoom.
{{< highlight js "linenos=inline" >}}
    zoomTo(scale) {
        this.scale.setTo(scale)
    }
{{< /highlight >}}

---

> Aha, ain't that easy?

> Yes it is.

> Does it work?

> Not so fast.

One problem surfaced ist that when zoomed out and panned, the camera panned *outside* of the group, like in the picture bellow.
{{< figure src="/img/1-bqUaZHg_1FCVjBqJI1ws9Q.png" >}}

The problem is: by default, the bounds of `game.camera` is set to the size of `game.world`. While our group is originally identical to `game.world`, when the group is scaled, `game.camera.bounds` should also be scaled and repositioned accordingly.

{{< highlight js "linenos=inline" >}}
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
{{< /highlight >}}

Finally, I added some `tween` to make the zoom smooth.
{{< highlight js "linenos=inline" >}}
    zoomTo(scale, duration) {
        ...
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
{{< /highlight >}}

And then I had…
{{< youtube DzFVyb30kFs >}}

---

# SO WHAT'S NEXT?

See how smoothly the camera is chasing the main character in the video? Check it out in [A Stalker on Phaser]({{< relref "a-stalker-on-phaser.md" >}}) and [stay tune]({{< subscribe >}}).