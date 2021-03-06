Tutorial step 10: Run a real game
=================================

Holy Moly! It's actually time to run your first real game with MPF.
When we say a "real" game, we're talking about with multiple players
and balls machine flow from attract to game mode and back to attract
once the game is over.

1. Make one quick addition to your display configuration
--------------------------------------------------------

We know that at this point, you just want to run your game. The
problem is if we run it now, the display will continue to show "PRESS
START" throughout the entire game since we haven't configured it for
anything else. So let's make a quick addition to the ``slide_player:``
section of your config so it will show the player and ball number when
a game is in progress. (Later in this tutorial we'll revisit this and
explain what's actually going on. For now just make this change.) In
your config file, add a ``ball_started:`` entry with the following
information. Your complete ``slide_player:`` section should now look
like this:

::

    slide_player:
     mc_ready: welcome_slide
     mode_attract_started: attract_started
     ball_started:
         widgets:
            type: text
            text: PLAYER (number) BALL (ball)

2. Change your flipper config so they don't automatically enable on machine boot
--------------------------------------------------------------------------------

Almost there! The other quick change we need to make is to remove the
``enable_events:`` from the flipper configuration that we added back in
the *Get Flipping!* step.

This is because by default, MPF will
automatically enable your flippers when a ball starts and disable them
when a ball ends. But since we added a configuration setting to your
flippers that set them to automatically enable themselves immediately
when MPF loaded, that setting overwrote the default setting which
enables your flippers when a ball starts. So as your config file is
now, the flippers enable when MPF boots, then they disable when the
first ball ends, and that's it. They won't enable again for Ball 2.

To make this change, simply remove the ``enable_events: machine_reset_phase_3`` line
from each of your two flipper sections of your config file. So now
your ```flippers:`` section should look like this: (It might not be 100%
identical since you might have single-wound flipper coils.)

::

    flippers:
        left_flipper:
            main_coil: c_flipper_left_main
            hold_coil: c_flipper_left_hold
            activation_switch: s_left_flipper
        right_flipper:
            main_coil: c_flipper_right_main
            hold_coil: c_flipper_right_hold
            activation_switch: s_right_flipper

3. Running your game with physical hardware
-------------------------------------------

If you have a physical machine attached, go ahead and run your game
without the ``-x`` or ``-X`` command line options. (If you don't have a physical
machine and you want to simulate a game using the keyboard keys,
skip to Step 4 below.)

::

  C:\pinball\your_machine>mpf both -v

Make sure you have at least one ball in the trough and then run your
game. The display should display "ATTRACT MODE." Hit the start button.
A ball should be kicked out of the trough and into the plunger lane,
and the DMD should change to "PLAYER 1 BALL 1." If you have a coil-
fired plunger, you should be able to hit the launch button and the
coil should fire. If you have a manual plunger, you should be able to
plunge and flip. If you hit the start button a second time during Ball
1, a second player should be added. (The DMD won't show this since we
haven't configured it to show a message, but you can see this in the
logs and when the ball drains then it should go to Player 2 Ball 1
instead of Player 1 Ball 2.)

A few caveats to this early bare-bones game:

+ Since you haven't configured any scoring yet, this game will be
  boring and nothing will score. But hey, you're playing!
+ If your flippers, trough eject, or plunger coil is too weak or too
  strong, you can adjust them in the coil's ``pulse_ms:`` setting in the
  config file.
+ If you start MPF with a ball in the plunger lane and you
  have a coil-fired plunger, MPF will immediately fire the plunger to
  kick out the ball. This is by design since you don't have a "home" tag
  in your plunger ball device's configuration, which means that MPF will
  automatically eject the ball to get all the balls into ball devices
  tagged with "home."
+ If you shoot a ball into a playfield lock or any other ball device,
  it will get stuck there since you haven't configured that device. (In
  this case you need to add configuration entries for those ball devices
  so MPF can know about them. Then it will automatically kick out any
  balls that enter. We'll get to that later.)
+ By default MPF is configured to allow a maximum of 4 players per
  game, with 3 balls per game. You can change this in the :doc:`/config/game`
  section of the machine config.

4. "Playing" a game without a physical machine attached
-------------------------------------------------------

If you've been adding keyboard switch map entries to your config file
as you've been going through this tutorial, you can actually "play" a
complete game on your computer keyboard. Before you do this Doing so
it somewhat complex, but it is technically possible. Here's how you'd
do it:

#. Run ``mpf both -v`` and wait until you see the "ATTRACT MODE" message
   on the display, push the "S" key to start a game. At this point MPF
   will eject a ball from the trough to the plunger
#. If you have a coil-fired plunger, push the "L" key (or whatever key
   you mapped to your launch button) to launch the ball. You should see
   the plunger lane switch deactivate based on the coil firing thanks to
   the smart virtual platform.
#. If you do not have a coil-fired plunger, push the "P" key (or
   whatever key you mapped to your plunger lane switch) to un-toggle that
   switch which simulates the ball leaving the plunger lane.
#. Now you can "flip" with the "Z" and "/" keys.
#. After you get bored of this, push the "1" key to activate a trough
   ball switch. At this point MPF will think a ball drained and you
   should see the display switch to Ball 2 and the trough switch should
   open and the plunger lane switch should close as the "smart virtual"
   platform ejects a ball from the trough to the plunger.
#. Repeat until you're bored.
#. After Ball 3 is over you can push "S" again to start another game.
#. Congrats! You just played your first virtual pinball game. Yeah,
   it's boring right now and kind of hard to understand, but it worked!
   (Later we'll show you how to write automated scripts to "play" the
   game for you. :)

5. Look at your log files
-------------------------

Assuming everything went correctly, now let's look at the log files to
see what actually happened. Remember that you'll actually have two log
files—-one from the MPF core and one from the media controller. These
will be the two newest files in the ``/logs`` folder which will be
automatically added to your machine folder. Just take a look through them
to start to get a feel for
everything that MPF is doing behind the scenes.

6. What if your game won't start?
---------------------------------

If your game doesn't start or doesn't work, hopefully we've given you
enough information in this tutorial to work out what the problem is.
That said, here's a list of things that could go wrong:

+ No ball in the trough.
+ Ball in the trough, but not activating the switch.
+ Trough switches are optos but you didn't add ``type: NC`` to your
  switch configurations. (Mechanical trough switches do not need a
  ``type:`` setting.)
+ Trough is trying to eject, but the trough coil's ``pulse_ms:`` setting
  is too weak and the ball can't get out.
+ Incorrect switch or coil numbers which don't match up to your actual
  hardware inputs and outputs.
+ Some other setting isn't configured properly, which could lead to
  who-knows-what error? (Maybe compare your config file to the complete
  config from mpf-examples?)

If you're still having problems, feel free to post to the mpf-users
Google group.

Check out the complete config.yaml file so far
----------------------------------------------

If you want to see a complete ``config.yaml`` file up to this point, it's in the ``mpf-examples/tutorial``
folder with the name ``step10.yaml``.

You can run this file directly by switching to that folder and then running the following command:

::

   C:\mpf-examples\tutorial>mpf both -c step10

Remember though that unless you're following this tutorial with an actual *Demolition
Man*, you'll have some differences in your config file, including:

+ If you're using FAST or OPP hardware, you'll have a ``fast:`` or
  ``opp:`` section in your config.
+ Your ``number:`` settings for all your switches and coils will be your
  actual hardware numbers and not the numbers for *Demolition Man*from
  this file.
+ Your flippers might be configured for single-wound coils instead of
  dual-wound (main + hold) like in this file.
+ Your trough might have fewer switches.
+ Your plunger lane might not have a coil-fired eject, which also
  means you might not have a launch button or
  ``player_controlled_eject_tag``.
+ Your plunger lane might not have a switch which is activated when a
  ball is in it, meaning it won't be configured as a ball device.
+ Your trough might be a Williams System 11 or early Williams WPC
  style which would be configured as two separate ball devices.
