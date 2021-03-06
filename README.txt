======================================================
SkewT -- Atmospheric Profile Plotting and Diagnostics
======================================================

SkewT provides a few useful tools to help with the plotting and analysis of 
upper atmosphere data. In particular it provides some useful classes to 
handle the awkward skew-x projection (provided by Ryan May, see notes in 
source code and LICENSE.txt).

It's most basic implementation is to read a text file of the format provided 
by the University of Wyoming's website: 

http://weather.uwyo.edu/upperair/sounding.html

Typical usage often looks like this::

    #!/usr/bin/env python

    from skewt import SkewT
    sounding = SkewT.Sounding(filename="soundingdata.txt")
    sounding.plot_skewt(color='r',lw=2)

Alternatively you may input the required data fields in a dictionary. The 
dictionary must have as a minimum the fields PRES and TEMP corresponding to 
pressure (hPa) and temperature (deg C). Soundings will typically have a dew 
point temperature trace and wind barbs as well, so it's best to include the 
dewpoint temp DWPT (deg C), wind speed SKNT (knots) and wind direction in 
degrees WDIR. Other fields may be included as per the docstring::

    #!/usr/bin/env python

    from skewt import SkewT
    sounding = SkewT.Sounding(data=data_dict)
    sounding.plot_skewt(color='r',lw=2)

News
====
Simon Caine is now on board as a developer! We look forward to a bunch of 
exciting improvements from this Guru of Python/Atmos.

Thanks for your interest in this package and I'd love to hear your feedback: 
thomas.chubb AT monash.edu

Here's a summary of what's new in this release:

* Added an automatic parcel initialisation routine to facilitate parcel 
  ascent routine.
* Bug-fix: There was an error thrown if the input data wasn't masked arrays. 
  This must have been really annoying... Sorry, and thanks Simon for 
  pointing this out. You wouldn't have noticed if you were reading text 
  files because the Sounding.readfile() method returns masked arrays.

Sounding Files
==============
The format for the sounding files is very specific (sorry). You are best off 
using the example in "examples" as a template. Here's a sample of the first 
few lines::

    94975 YMHB Hobart Airport Observations at 00Z 02 Jul 2013

    -----------------------------------------------------------------------------
       PRES   HGHT   TEMP   DWPT   RELH   MIXR   DRCT   SKNT   THTA   THTE   THTV
	hPa     m      C      C      %    g/kg    deg   knot     K      K      K 
    -----------------------------------------------------------------------------
     1004.0     27   12.0   10.2     89   7.84    330     14  284.8  306.7  286.2
     1000.0     56   12.4   10.3     87   7.92    325     16  285.6  307.8  286.9
      993.0    115   12.8    9.7     81   7.66    311     22  286.5  308.1  287.9

The script defines columns by character number so you really do have to get 
the format *exactly* right. One day I will get around to writing a routine 
to output the text files properly.

Parcel Ascent
=============
Simple routine to calculate the characteristics of a parcel initialised with 
pressure, temperature and dew point temperature. You could do it like this::

    from skewt import SkewT

    sounding=SkewT.Sounding("examples/94975.2013070200.txt")
    sounding.make_skewt_axes()
    sounding.add_profile(color='r',lw=2)
    sounding.lift_parcel(1004.,17.4,8.6)
    draw()

Automatic Parcel Definition (New in version 0.1.3!)
---------------------------------------------------
You can still manually input a parcel as in the example above, but there is 
a new routine to automagically define a parcel from the sounding itself. You 
define a layer depth that you would like to characterise (say 100mb). The 
routine surface_parcel then returns

1. The surface pressure (just the pressure of the lowest level)
2. The characteristic dew-point temperature (from the average Qv in the layer)
3. The characteristic temperature (from the maximum Theta in the layer)

You could do it like this::

    from skewt import SkewT

    sounding=SkewT.Sounding("examples/94975.2013070200.txt")
    sounding.make_skewt_axes()
    sounding.add_profile(color='r',lw=2)
    parcel=sounding.surface_parcel(mixheight=100.)
    sounding.lift_parcel(*parcel)
    draw()

The above steps are also now included in the Sounding.plot_skewt() wrapper 
for your convenience, so all of the above can be condensed with::

    from skewt import SkewT

    sounding=SkewT.Sounding("examples/94975.2013070200.txt")
    sounding.plot_skewt(color='r',lw=2)


To-Do List
==========
* The Sounding.readfile() routine is a bit of a mess.

* I want to do some basic diagnostics given the lifted parcel. This 
  shouldn't be hard it's just that I have more pressing things to do. If 
  anybody out there wants to adapt/contribute routines that they have I'd be 
  most grateful. Send your fan/hate mail to thomas.chubb AT monash.edu

* Hodographs?

Contributors
==============

* Simon Caine is the latest recruit (yay)

* Hamish Ramsay has promised to at least think about adding some extra 
  diagnostics.

* The initial SkewX classes were provided by a fellow called Ryan May who 
  was a student at OU. I have not made contact with Ryan other than to 
  download his scripts and modify them for my own purposes.

