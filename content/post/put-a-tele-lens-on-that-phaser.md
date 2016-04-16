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

**This article was written based on Phaser 2.3, some of the information regarding the framework might not be applicable on later version of Phaser.**

I’ve been working with [Phaser](http://phaser.io) for a while now and I feel comfortable with the framework. Its API is pretty decent and simple, very easy to learn and performs well on mobile devices. It was smooth and happy for me.
{{< figure src="https://cdn-images-2.medium.com/max/800/1*9BZY9WvFkXcqcL7VaWUpnQ.jpeg" >}}

---

However, recently I was making an action/JRPG game. I wish to utilize camera movement/zooming in order to create better effect and experience. That’s when I realized *Phaser Camera does not have zoom functionality!!!*
{{< figure src="https://cdn-images-2.medium.com/max/800/1*ugmDwJ--aLIGUkJfvghjng.jpeg" title="NO ZOOM? It’s a freaking camera!" >}}

So I [googled](https://www.google.com/search?q=phaser+camera+zoom) for some work around but none of the implementations looks cool to me. Their codes look just…messy and complicated. I just thought, why does it have to be complicated? So I decided to make my hands do some dirty work.
{{< figure src="https://cdn-images-2.medium.com/max/800/1*bm8JsSkJElRo8FQUw_ulJQ.png" >}}

---

My way is simple: *make a group and throw what ever I want to see on the camera in it and scale it when I wanna zoom the camera.*

So this is what I came up with:

{{< highlight js "linenos=inline" >}}
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
{{< /highlight >}}

> Aha, ain't that easy?

> Yes it is.

> Does it work?

> Not so fast.

{{< figure src="https://cdn-images-2.medium.com/max/800/1*VJMB7ZisBUqsrMWEOkfU1g.png" >}}

---

When zooms out and pans, the camera pans outside of the group.
{{< figure src="https://cdn-images-2.medium.com/max/800/1*bqUaZHg_1FCVjBqJI1ws9Q.png" >}}

That is because as default, the camera bounds is set to the size of the world, when the group scales, the bounds should also be scaled and repositioned.

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

Finally, I added the ability to zoom slowly, because people like it smooth.
{{< highlight js "linenos=inline" >}}
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
{{< /highlight >}}

And then I had…
{{< youtube DzFVyb30kFs >}}

---

# SO WHAT'S NEXT?

See how smoothly the camera is chasing the main character in the video? Check it out in [A Stalker on Phaser](http://anhnt.ninja/2015/08/19/a-stalker-on-phaser/) and [stay tune](http://).
