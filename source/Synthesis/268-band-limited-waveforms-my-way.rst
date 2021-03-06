Band Limited waveforms my way
=============================

- **Author or source:** Anton Savov (gb.liam@ottna)
- **Type:** classic Sawtooth example
- **Created:** 2009-06-22 18:09:08


.. code-block:: text
    :caption: notes

    This is my <ugly> C++ code for generating a single cycle of a Sawtooth in a table
    normaly i create my "fundamental" table big enough to hold on around 20-40Hz in the
    current Sampling rate
    also, i create the table twice as big, i do "mip-maps" then
    so the size should be a power of two, say 1024 for 44100Hz = 44100/1024 = ~43.066Hz
    then the mip-maps are with decreasing sizes (twice) 512, 256, 128, 64, 32, 16, 8, 4, and 2
    
    if the "gibbs" effect is what i think it is - then i have a simple solution
    here is my crappy code:


.. code-block:: c++
    :linenos:
    :caption: code

    int sz = 1024; // the size of the table
    int i = 0;
    float *table; // pointer to the table
    double scale = 1.0;
    double pd; // phase
    double omega = 1.0 / (double)(sz);
    
    while (i < sz)
    {
    	double amp = scale;
    	double x = 0.0; // the sample
    	double h = 1; // harmonic number (starts from 1)
    	double dd; // fix high frequency "ring"
    	pd = (double)(i) / (double)(sz); // calc phase
    	double hpd = pd; // phase of the harmonic
    	while (true) // start looping for this sample
    	{
    		if ((omega * h) < 0.5) // harmonic frequency is in range?
    		{
    			dd = cos(omega * h * 2 * pi);
    			x = x + (amp * dd * sin(hpd * 2 * pi));
    			h = h + 1;
    			hpd = pd * h;
    			amp = 1.0 / h;
    		}
    		else { break; }
    	}
    	table[i] = x;
    	++i;
    }
    
    the peaks are around +/- 0.8
    a square can be generated by just changing h = h+2; the peaks would be +/- 0.4
    
    any bugs/improvements?

Comments
--------

- **Date**: 2009-06-22 18:37:34
- **By**: gb.liam@ottna

.. code-block:: text

    excuse me, there is a typo
    
    amp = scale / h;

- **Date**: 2009-09-20 10:34:18
- **By**: antto mail bg

.. code-block:: text

    for even smoother edges:
    dd = cos(sin(omega * h * pi) * 0.5 * pi);
    no visual ringing, smooth waveform

- **Date**: 2009-09-25 04:15:31
- **By**: antto mail bg

.. code-block:: text

    to get +/- 1.0 amplitude: scale = 1.25

