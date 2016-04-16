+++
comments = true
date = "2015-03-20"
draft = false
image = ""
menu = ""
share = true
slug = "a-stalker-on-phaser"
tags = ["game", "development", "es6", "javaScript", "phaser", "guide", "tutorial"]
title = "A Stalker on Phaser"
excerpt = 'A stalker doesn’t stick on his target, he keeps distance and quietly follows. That means, when the target moves, he doesn’t move immediately but slowly chase after. Let me remind you: a stalker keeps his distance.'

+++

On my [previous post]({{< relref "put-a-tele-lens-on-that-phaser.md" >}}), I’ve written an easy to use zoomable camera for [Phaser](http://phaser.io). I had it smoothly zoom the camera to any scale I want.

{{< figure src="https://cdn-images-2.medium.com/max/800/1*XTaGyskWI0j0Kc2D7X2hgA.jpeg" title="I feel you, Obama." >}}

>And then I realized, I wanted everything to be smooth.

How about a smooth follow functionality…

Don’t get me wrong, Phaser camera does have follow functionality, but it&#8217;s just sticking the camera on to the entity. Seriously, that is, in no way, *following*. When I hear *follow*, I think **stalker**.
{{< figure src="https://cdn-images-2.medium.com/max/800/1*icCM_IOWF0jN05JYCIpMHg.jpeg" title="" >}}

A stalker doesn’t stick on his target, he keeps distance and quietly follows. That means, when the target moves, he doesn’t move immediately but slowly chase after. So I let my hands dirty again.
{{< figure src="https://cdn-images-2.medium.com/max/800/1*bm8JsSkJElRo8FQUw_ulJQ.png" title="" >}}

As always, my method is very simple: make an actual stalker and let the camera follow him instead of the target. And this is what I came up with [(remember my code from previous post?)]({{< relref "put-a-tele-lens-on-that-phaser.md" >}})
{{< highlight js "linenos=inline">}}
class Camera extends Phaser.Group {
   constructor() {
       ...
       this.stalker = game.add.sprite(0, 0, null);
       game.physics.arcade.enable(this.stalker);
       game.camera.follow(this.stalker);
   }
   follow(world) {
       this.target = target;
   }
   update() {
       super.update();
       if (this.target) {
           var arcade = game.physics.arcade;
           var stalker = this.stalker;
           arcade.moveToObject(stalker, target);
       }
   }
}
{{< /highlight >}}

>Aha, ain’t that easy?

>Yes it is.

>Does it work?

>Not so fast.

{{< figure src="https://cdn-images-2.medium.com/max/800/1*VJMB7ZisBUqsrMWEOkfU1g.png" >}}

Firstly, if the target is in a group, it’s position is relative to that group. The stalker however, doesn’t belong the that group, so he couldn’t possibly know where his target is. So instead of let the stalker follow the target position, I let him follow the target’s GPS position (aka `target.world`).
{{< highlight js "linenos=inline" >}}
follow(world) {
    this.target = target.world;
}
{{< /highlight >}}

Secondly, when the stalker is far away from the target, everything works awesome, he chase after the target beautifully. But he doesn’t slow down when he is near the target and runs pass it. He realizes that, and turns around and runs back, but again, he moves too fast and runs pass it again.

>Let me remind you: a stalker keeps his distance.

When he is too close, he would stop:
{{< highlight js "linenos=inline" >}}
constructor() {
    ...
    this.safeDistance
    ...
}
update() {
    ...
    if (this.target) {
        ...
        var position = this.stalker.position;
        var distance = position.distance(this.target);
        if (distance >= this.safeDistance) {
            arcade.moveToObject(this.stalker, this.target);
        }
    }
}
{{< /highlight >}}

For complete source code, check out the gist [here](https://gist.github.com/netcell/60097d0661ad2f74a258).