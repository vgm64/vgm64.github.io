---
layout: post
title: Quickly fitting data points in python
---


Before I came to data science, I came from the odd intersection of particle
physics, nuclear physics, and astrophysics. From the particle physics point of
view, the C++ library ROOT reigns supreme. 

> The ROOT system provides a set of OO frameworks with all the functionality
> needed to handle and analyze large amounts of data in a very efficient way. ...
> Included are histograming methods in an arbitrary number of dimensions, curve
> fitting, function evaluation, minimization, graphics and visualization classes
> to allow the easy setup of an analysis system that can query and process the
> data...

ROOT is also famous for <a href=
"http://www.cernlove.org/blog/2009/10/the-root-of-all-evil/" >horrible looking
plots</a>. It's not really ROOT's fault; ROOT was around when in the computing
world, those were the hip new colors on the block. Sure, ROOT can be teased into
creating publication worthy plots that are quite nice, but if I'm going to spend
that much time tweaking and tuning my plots, then I'd rather use matplotlib and
make them absolutely stunning.

But what ROOT lacks in its graphical niceties, it made up for in its ability to
quickly fit data. Every histogram and graph class had its own `Fit` method that
would gladly take predefined functions (like Gaussian curves), as well string
representations of methods (like `[0]*x + sin(2*[1]*x)`) and user defined C++
functions. This made measuring decay time constants and energy deposition means
extremely easy. Often the result of the fit was applied directly to the plot
being fit to. Out the door it went to the eager advisor's inbox!

When I moved to data analysis in Python, making best fits to data was clunky and
depended on manual application of scipy's optimize.leastsq method. To get back a
bit of the agility I was used to from ROOT, I wrote the `python-fit` module,
<a href="https://pypi.python.org/pypi/python-fit">available on PyPi</a>. Like a
Unix utility, it's designed to do one thing and do it well. Quickly and
painlessly fit data.

Here it is in action:

    $ cat strut_stuff.py
    from matplotlib import pyplot as plt
    import fit
    from numpy import random, exp, arange
    
    # Create some data to fit
    x = arange(-10, 10, .2)
    # A gaussian of height 10, width 2, centered at zero. With noise.
    y = 10*exp(-x**2/8) + (random.rand(100) - 0.5)
    
    
    # No need to provide first guess at parameters for fit.gaus
    (xf, yf), params, err, chi = fit.fit(fit.gaus, x,y)
    
    print "N:     %.2f +/- %.3f" % (params[0], err[0])
    print "mu:    %.2f +/- %.3f" % (params[1], err[1])
    print "sigma: %.2f +/- %.3f" % (params[2], err[2])
    
    plt.plot(x,y, 'bo', label='Data')
    plt.plot(xf, yf, 'r-', label='Fit')
    plt.legend()
    plt.show()
    $ python strut_stuff.py
    N:     9.92 +/- 0.082
    mu:    0.01 +/- 0.019
    sigma: 2.01 +/- 0.019

The key to this module's utility is its return values. Most fitting routines
focus on returning the parameters that minimize the chi-squared residual. I
wanted to focus on what the resulting fit looks like. A set of evenly spaced x
points and the corresponding y values are provided so that the data can be
immediately plotted. Afterwards, we see the typical output parameters, error on
the fit parameters, and the chi-square residual. All of this is powered by
scipy's <a href="http://docs.scipy.org/doc/scipy/reference/odr.html">ODR
package</a>. Happy fitting!






