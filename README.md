# netbsd-gpio-doc
NetBSD GPIO DOC

This is the story of a good night getting GPIO working on
a raspberry pi running NetBSD.

Most linux tutorials seem to focus on using python but i was intriegued with the shell utility
gpioctl as it seemed like a more lightweight way of managing the pins. At first i was having errors like
this:


```
bash-4.3# gpioctl gpio0 25 set out pp
gpioctl: GPIOSET: Operation not permitted
bash-4.3# gpioctl gpio0 24 set out pp
gpioctl: GPIOSET: Operation not permitted
bash-4.3# gpioctl gpio 2 set out pp
gpioctl: GPIOSET: Operation not permitted
bash-4.3# gpioctl gpio0 2 set out pp
gpioctl: GPIOSET: Operation not permitted

```
Here is the email i sent to the ports-arm@netbsd.org list.

(http://mail-index.netbsd.org/port-arm/2016/02/13/msg003670.html) [http://mail-index.netbsd.org/port-arm/2016/02/13/msg003670.html]

...and the helpful response.

(http://mail-index.netbsd.org/port-arm/2016/02/13/msg003669.html) [http://mail-index.netbsd.org/port-arm/2016/02/13/msg003669.html]


What i did not realize is that there was a lot of boot time setup needed to use the GPIO pins.

First you have to activate the gpio configuration in /etc/rc.conf with the following line:

```
gpio=YES                                                # configure GPIO devices

```

As was patiently explained to me on the ports-arm list the pins need to be first configured when 
securelevel = 0 at bootlevel before the securelevel is raised. This is done in /etc/gpio.conf by 
/etc/rc.d/gpio . This script only runs if gpio=YES is in /etc/rc.conf.


my gpio.conf looks like this to configure pin 21 of my raspberry pi for LED output

```

# $NetBSD: gpio.conf,v 1.2 2011/11/19 17:30:09 mbalmer Exp $
#
# GPIO device and pin configuration
# The syntax of this file is like the command line arguments to the gpioctl(8)
# command.
#
# E.g. to define pin 6 of /dev/gpio0 as output and name it error_led, write
# gpio0 6 set out error_led

# Empty lines and lines starting with # are ignored.

gpio0 21 set out twentyone


```

After reboot this allows me to turn the pin on and off with gpioctl.

```
bash-4.3# gpioctl /dev/gpio0 21 toggle
pin 21: state 0 -> 1
bash-4.3# gpioctl /dev/gpio0 21 toggle
pin 21: state 1 -> 0
bash-4.3# gpioctl /dev/gpio0 21 toggle
pin 21: state 0 -> 1
bash-4.3# gpioctl /dev/gpio0 21 0     
pin 21: state 1 -> 0
bash-4.3# gpioctl /dev/gpio0 21 0
pin 21: state 0 -> 0

```

Success !! ... And thanks to all who helped !!!
