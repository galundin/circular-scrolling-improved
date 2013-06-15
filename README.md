xorg-synaptics-circscroll-improved
==================================

*UPDATE: It has been suggested to use a system-wide gesture framework, instead of patching the driver.  I'm still looking into that.*

The proprietary Synaptics touchpad driver provides a scrolling gesture called ChiralMotion (TM). I have found it to be the most efficient and effortless scrolling input I have used on any device, and I really miss it in the open source driver. Unfortunately, ChiralMotion is patented, and the proprietary Linux driver is only available to OEMs.

The open source driver does provide a gesture of similar intent, called Circular Scrolling, which measures the difference in angular position relative to the center of the touchpad. However, this requires the user to divert attention and use a fairly large radius to avoid getting off-center, otherwise it will produce erratic jumps and oscillations. This also restricts both the precision and maximum speed of scrolling, so the usability of this gesture is comparatively limited.

So I designed some improvements to Circular Scrolling, to achieve usability on par with ChiralMotion, without infringing the patent (US7446754). Its output reliably detects clockwise versus counter-clockwise motion, anywhere on the touchpad, but the algorithm makes no such distinction of "chiral" state. The basic mechanism is still a difference in angular position, but relative to the previous point. In effect, it measures the change in the angles of the last two segments. This method is more similar to US patent 20110025618, but that patent specifically states that it does not apply to the use of arctangent, but rather to a simplified look-up table suitable for low-cost embedded systems. There are other differences from both of these patents, which I'll gladly defend if necessary. In my implementation, the angular difference is further processed with an adaptive low-pass filter to reduce noise while preserving responsiveness. Its computational complexity is negligible and shouldn't have any significant impact on performance.

Users already accustomed to the current Circular Scrolling gesture shouldn't be adversely affected by the change. The main differences they will notice are:

* enhanced dynamic range, providing finer control
 on the low end, and greater speed on the high end

* greatly improved reliability for tight rotations, 
 allowing more efficient and effortless scrolling

As stated in the man page:

 "Use tight circles near the center of the pad for fast scrolling 
 and large circles for better control."

This recommendation is still perfectly appropriate, except that the circling doesn't need to be centered to the pad, it can be anywhere. I tried to find an optimal balance between latency and noise in response to radius and speed, consistent with that description. This is the main difference from ChiralMotion, in which the magnitude of scrolling is a function of distance moved. My implementation might actually be more intuitive and reliable than ChiralMotion. I'm sure the filter parameters could use a bit of tuning, but it works fairly well already.

I prototyped this in Javascript, contained in a stand-alone HTML file, which you can view here:

http://galundin.github.io/xorg-synaptics-circscroll-improved/circscroll-prototype.html

Please take a look at the well-commented source of that page for details on the algorithm.
