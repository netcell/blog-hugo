+++
comments = true
date = "2015-04-03"
draft = false
image = ""
menu = ""
share = true
slug = "make-some-joy-on-phaser-stick"
tags = ["game", "development", "es6", "javaScript", "phaser", "guide", "tutorial"]
title = "A Virtual Joystick for Phaser"
excerpt = 'So I wrote my own Virtual Joystick Plugin for the HTML5 game engine Phaser. That may sound stupid now, but at that time @photostorm didn’t release his official plugin yet. Still, it’s a $20 value there.'

+++

So I needed a *Virtual Joystick* plugin (kinda) a few weeks ago. I found [phaser-touch-control-plugin](https://github.com/eugenioclrc/phaser-touch-control-plugin), but I want the joystick to stay in one place on the screen. So I wrote my own Virtual Joystick Plugin. That may sound stupid now, but at that time *@photostorm* didn’t release his [official plugin](http://phaser.io/shop/plugins/virtualjoystick) yet. Still, it’s a $20 value there.

**For a quick demo, check out the Youtube video at the bottom of this post.**

Here is my design:
{{< figure src="/img/1-MOCWHqW8lCMOF3YOuZY9_w.png" >}}

1. A **background** for the joystick (the grey box) to show the players where to put their finger.
{{< highlight js "linenos=inline" >}}
class Joystick extends Phaser.Sprite {
    constructor(x, y) {
        super(game, 0, 0, 'background');
        this.anchor.setTo(0.5, 0.5);
    }
}
{{< /highlight >}}

2. A *draggable sprite* for the **pin** (the red box) which players can, duh, drag. Here, `pin.input.enableDrag` is called with first param `true` so that the pin is always *centered* at your finger tip.
{{< highlight js "linenos=inline" >}}
constructor(x, y) {
    ...
    let pin = game.add.sprite(0, 0, 'pin');
        pin.anchor.setTo(0.5, 0.5);
        pin.inputEnabled = true;
        pin.input.enableDrag(true);
    this.addChild(pin);
    this.pin = pin;
}
{{< /highlight >}}

3. Event `onDragStop`: The pin returns to the center of the background.
{{< highlight js "linenos=inline" >}}
constructor(x, y) {
    ...
    pin.events.onDragStop.add(this.onDragStop, this);
    ...
}
onDragStop(){
    this.pin.position.setTo(0, 0);
}
{{< /highlight >}}

4. A restriction `maxDistance` to prevent the pin from going outside of the background (like in the picture below). If the background is a circle, this is normally the radius, meaning half of the background image width. The check happens at event `onDragUpdate`.
{{< figure src="/img/1-GGn274e5_Gy_Fe1Q4y_wZQ.png" >}}
{{< highlight js "linenos=inline" >}}
constructor(x, y) {
    ...
    this.maxDistance = this.width/2;
    pin.events.onDragUpdate.add(this.onDragUpdate, this);
    ...
}
onDragUpdate(){
    let {pin, maxDistance} = this;
    var distance = pin.getMagnitude();
    if (distance > maxDistance) {
        pin.setMagnitude(maxDistance);
    }
}
{{< /highlight >}}

---

There are two problems:

1. Event `onDragUpdate`: if `distance` is larger than `maxDistance`, the pin position is changed and not at player finger anymore.
2. In other game, players can start dragging from anywhere on the background of the joystick and the pin jumps at it immediately. Here, players have to actually start dragging at the pin position.

To solve this, I added a transparent layer to act as the draggable, and use the pin only for indication of position.
{{< figure src="/img/1-7uYh6KEiMyim_lhPJSxswg.png" >}}

1. Add a transparent `dragger` and move `dragging` events to `dragger`
{{< highlight js "linenos=inline" >}}
constructor(x, y) {
    ...
    let dragger = this.dragger = game.add.sprite(0, 0, null);
    dragger.anchor.setTo(0.5, 0.5);
    dragger.width = dragger.height = this.width;
    dragger.inputEnabled = true;
    dragger.input.enableDrag(true);
    this.addChild(dragger);
    dragger.events.onDragStop.add(this.onDragStop, this);
    dragger.events.onDragUpdate.add(this.onDragUpdate, this);
}
onDragStop(){
    this.dragger.position.setTo(0, 0);
    this.pin.position.setTo(0, 0);
}
{{< /highlight >}}

2. The pin's position then would be set based on the dragger's position and `maxDistance`.
{{< highlight js "linenos=inline" >}}
onDragUpdate(){
    let {pin, dragger, maxDistance} = this;
    var distance = dragger.getMagnitude();
    pin.position.copyFrom(dragger);
    if (distance > maxDistance) {
        pin.setMagnitude(maxDistance);
    }
}
{{< /highlight >}}

Check out the full source code on this [gist](https://gist.github.com/netcell/9083b5cef97125128420), and see this Virtual Joystick in action in my development footage for Sword of Vendetta: Dojo’s Last Blood.

{{< youtube r-rhHbcNb0g >}}