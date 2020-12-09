Notes for Graham.

Let's see how our power draw is. We optimised our clock, now let's try to optimise our weather.

As a start, run on the 'tweaked' hardware (power led nuked, spi flash pulldown for deep sleep mode).
Run at 80MHz, and turn off the logging...

Idle 460uA - about as good as we can currently get on these boards!
Power during boot/run seemed pretty high. I suspect we can improve this by:

 - ensure we only have the wifi on for as short a time as possible
 - turn the voltage detect adc off as soon as we can
 - turn the serial logs into esp32 log_nnn's, and disable the uart when
   we are not in debug mode.

Ultimately, we only wake up for 6 seconds once every 3 hours 6/(3600*3) == 0.00055 of the time, so
the power draw during the wake is probably nothing compared with the power draw during sleep.

460uA/0.00055 == 836363 - I think we'd have to be pulling >= 800mA for the whole 6s.

Still, we should try to reduce our power as much as we can..

OK, the wifi is only on for 1s and then turned off before the big screen draw, so that is OK already.
I've clocked the unit down to 80MHz, and then drop that to 20MHz after we are done with the wifi.
I now explicitly turn the adc off after we have gotten the voltage.

I'm going to remove the clock from the top left corner, as it is the time we updated, not the current
time, which is confusing. Later if we wanted we could add it back and do a per-minute update with
a partial screen redraw - quite possible, but will eat more power and possibly get a bit fuzzy
if we do a partial redraw for a whole hour (59 redraws).
