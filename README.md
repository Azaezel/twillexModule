# Twillex 
Twillex - Scene Object Tweening

Twillex provides animation, or "tweening", between the current state of an object and a given state.
The "state" can be represented by *any* combination of fields, such as position, blendColor,
size, rotation, text, or your own custom fields like myField.  There are short-cuts for fields
of the common scene object.  specifically, x and y for position, sx and sy for size, and
r, g, b, and a for color.
There are many interpolation methods to provide "easing" to your Tweens.  Easing is the animation
technique of speeding up at the beginning and/or slowing down at the end, called easing in and out,
respectively.  Easing options include linear, quadratic, "bounce," "elastic,"
and other common ones.

## License and Copyright
Version 1.1
Copyright 2012: Charles Patterson
BSD License: use, modify, and distribute freely, but leave the credits

## Special Thanks
Thanks to Lukas Joergensen for adding
- Torque3D support! (z values and a schedule-based loop)
- Global variables
- running eval() on the completion of a Tween

## Original Download and Documentation
For the original hosted version and instructions/discussions/etc. go to http://sourceforge.net/p/twillex

There you will also find a "Twillex Test Game" which is realy a scene that lets you manipulate
Tween parameters and see what happens.

# Usage and Instructions

Setup a Twillex "engine" to run tweens.  See the section SETUP below.  In our examples, we'll assume
it is named Tweener.

Then create Tweens by calling your Twillex engine's to() function:
```
%t = Tweener.to(1000, %banner, "position:100 0, rotation:-45, r:0.2");
%t.play();
```

Here we built a Tween (controlled by Tweener).  It will run for 1 second (1000 milliseconds),
and it will affect the %banner object.  We see that it will vary the fields 'position' -- which
is a two-element vector, by the way -- 'rotation', and the red component of the blendColor.

The Tween sits dormant until you command it to play, which we did.  Suppose this is a button or
HUD component and the time has come to show it again:
```
%t.rewind();
%t.play();
```

Now send it back where it came from:
```
%t.reverse();
%t.play();
```

Then when the Tween is no longer needed like at the end of the scene:
```
%t.delete();
```

Here is an example of a one-off Tween:
```
%t = Tweener.toOnce(500, %banner, "x:100, y:0, myVar:0.01", "yoyo:true");
```

Twillex cannot be sure what your fields will be called so it accepts them all, such as "position"
or "text" or "myVar".  You may get an error at run-time if the field didn't exist or wasn't a
short-cut field as was described earlier.  We also see a second string which holds control params that affect
the way the Tween plays out.  Due to our "yoyo" param, this Tween plays through then plays
backwards to the beginning and stops (all within 500 milliseconds).  

Since we wanted a one-time Tween, we used toOnce().  This is a convenience function which calls play() for you and also
add a parameter "delete:true" so that the Tween deletes itself on completion.  You could have used
to() and added the parameter yourself and also called play().
see $Twillex::paramValues for a complete list of parameters to affect the Tween

## Setup
To create a Twillex engine requires set up, tear down, and frame updates.

Setup and tear-down work as usual in Torque.
First, include the script
```
exec("<your_path>/Twillex.tscript");
```

Then, create a Twillex engine to run your tweens.
```
Twillex::create(Tweener);
```

You could even make several Twillex engines if you wanted to keep categories of Tweens separate.
In the end, delete your Twillex objects as usual:
```
Tweener.delete();
```

It's not possible to get Torque to call an onUpdate() on Twillex objects each frame.
The scene graph will have to be kind and call Twillex::onUpdate().

For example:
```
function mySceneGraph::onUpdateSceneTick(%this) {
   // do all the other stuff also
   Tweener.onUpdate();
}
```

Or if you prefer, instead of calling t2dSceneGraph::onUpdateSceneTick,
call Twillex::startUpdates() once and it will continue to schedule Twillex::onUpdate().

Note: Torque3D does not have a scene graph onUpdateSceneTick() and must use this function.
```
Tweener.startUpdates();
```

Note that you don't have to call onUpdate on the individual Tweens, just the Twillex engines.
