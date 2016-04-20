+++
comments = true
date = "2015-03-21"
draft = false
image = ""
menu = ""
share = true
slug = "a-stalker-on-phaser"
tags = ["game", "development", "es6", "javascript", "phaser", "guide", "tutorial"]
title = "A Stalker on Phaser"
excerpt = 'A stalker doesn’t stick on his target, he keeps distance and quietly follows. Unsatisfied with the follow functionality of the Camera class of the HTML5 game engine Phaser, I made a stalker out of my own.'

+++

**This article was written based on Phaser 2.3, some of the information regarding the framework might not be applicable on later version of Phaser.**

On my [previous post]({{< relref "put-a-tele-lens-on-that-phaser.md" >}}), I built an enhanced camera for [Phaser](http://phaser.io) that can smoothly zoom to any scale I want. At the end of that post, I shown you a demonstration video where the camera does not only zoom, but also follows the character smoothly. Now that's not something Phaser is capable of.

Don’t get me wrong, `Phaser.Camera` does have follow functionality, but it just sticks the camera on to the entity. That is, in no way, *following*.

When I hear *follow*, I think **stalker**.
{{< figure src="/img/1-icCM_IOWF0jN05JYCIpMHg.jpeg" title="" >}}

A stalker doesn’t stick on his target, he keeps distance and quietly follows. That means, when the target moves, he doesn’t move immediately but slowly chase after.

---

I would be using the `Camera` class [(from previous post)]({{< relref "put-a-tele-lens-on-that-phaser.md" >}}). As always, my method is very simple:

1. Make a sprite to act as a `stalker` and stick the `game.camera` on him.
{{< highlight js "linenos=inline">}}
class Camera extends Phaser.Group {
   constructor() {
       ...
       this.stalker = game.add.sprite(0, 0, null);
       game.camera.follow(this.stalker);
   }
}
{{< /highlight >}}
2. Use *arcade physics* method `moveToObject` to let `stalker` chase `target`. Of course, if you don't want to use arcade physics, it is possible to write a simple logic for this matter.
{{< highlight js "linenos=inline">}}
class Camera extends Phaser.Group {
   constructor() {
       ...
       game.physics.arcade.enable(this.stalker);
   }
   update() {
       ...
       var arcade = game.physics.arcade;
       arcade.moveToObject(this.stalker, this.target);
   }
}
{{< /highlight >}}

>Aha, ain’t that easy?

>Yes it is.

>Does it work?

>Not so fast.

Firstly, if `target` is in a group, it’s position is relative to that group. The `stalker` however, doesn’t belong the that *exclusive-membersive-only* group, so he could not possibly know where his target is. So I let the `stalker` follow `target.world` (the absolute position) instead of `target.position` (the relative position).
{{< highlight js "linenos=inline" >}}
follow(world) {
    this.target = target.world;
}
{{< /highlight >}}

Secondly, this only works when `stalker` is far away from `target`. The problem is he doesn’t slow down when he is close to `target` and hence runs pass it. Of course, he realizes that, turns around and runs back, but then again, he moves too fast and runs pass it again.

To solve the problem, we need to define a `safeDistance` as a threshold for `stalker` to recognize that he is too close and stop:
{{< highlight js "linenos=inline" >}}
update() {
    ...
    var position = this.stalker.position;
    var distance = position.distance(this.target);
    if (distance < this.safeDistance) return;
    ...
}
{{< /highlight >}}

---

For complete source code, check out this [gist](https://gist.github.com/netcell/60097d0661ad2f74a258). Here is the demonstration video from my [previous post]({{< relref "put-a-tele-lens-on-that-phaser.md" >}}).
{{< youtube DzFVyb30kFs >}}

