:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. note::

   This document describes the process to build the pointing model for the Auxiliary Telescope.

.. Add content here.
.. Do not include the document title (it's automatically added from metadata.yaml).

The process of building pointing model for the Vera Rubin observatory is common to both the Auxiliary and Main Telescopes.
This tech-note documents the process used to build the pointing model for the Auxiliary Telescope at different stages in the project.
Each new pointing model run is described in a dedicated session.

The pointing model is part of the pointing component (`ATPtg`_ and `MTPtg`_).
Although they are separate components, the code base is the same for both telescopes.
That means that all the pointing kernel functionality is exact the same on both cases, the main differences are in the SAL layer of the components.
For more information see the `pointing component documentation`_.

.. _ATPtg: https://ts-xml.lsst.io/sal_interfaces/ATPtg.html
.. _MTPtg: https://ts-xml.lsst.io/sal_interfaces/MTPtg.html
.. _pointing component documentation: https://ts-pointing-common.lsst.io

.. _General-Principles:

General Principles
==================

The pointing model contains some realistic physical representation of the most common mechanical properties observed in telescopes plus generic polynomial and harmonics terms.

The model input data consists of a series of observations taken on sky where the azimuth and elevation position of the telescope is registered after centering the start in the field of view.
The actual data contains the position with and without the offset required to center the target, plus the rotator angle (in case of AltAz mounts).

Registering the data in a format that can be later used to compute pointing models is supported by the pointing components by using the `pointAddData`_ command.

.. _pointAddData: https://ts-xml.lsst.io/sal_interfaces/ATPtg.html#pointadddata

The following in an example dataset taken with the Auxiliary Telescope:

.. code-block:: text

  LSST Auxiliary Telescope, 2020 Jan 26 UTC 1 47 15
  : ALTAZ
  : ROTNL
  -30 14 40.2
  333.0949589 78.6684282 333.0167132 78.7625213 293.6668950
  240.4719461 44.8995879 240.3726938 44.9816645 306.1848271
  226.7459782 40.0365061 226.6556824 40.1055927 315.6175556
  196.2657687 36.7445377 196.1729864 36.8002718 317.5486844
  172.6853974 53.4019806 172.5781383 53.4555115 302.4060731
  154.9764958 73.0126568 154.8450967 73.0685376 280.5258586
  120.8814629 55.6118420 120.8052043 55.6589219 303.2002837
  111.9944970 48.5062414 111.9290073 48.5488632 310.8539746
  117.3470395 30.9244814 117.2775891 30.9685963 332.6099343
  117.0992858 31.6455324 117.0286376 31.6953479 106.7711023
  115.5267384 36.6238119 115.4605335 36.6677284 108.7757071
  090.7710199 51.0219367 090.7150525 51.0742177 112.0793148
  336.8322735 66.9273300 336.7782862 67.0239479 199.2589405
  302.6581446 35.2470238 302.5773588 35.3348704 225.1006034
  184.1741942 70.6586995 184.0197970 70.7229965 354.5840128
  035.2813650 41.9310769 035.2287450 42.0025788 151.8577754
  043.0139225 27.5764544 042.9570499 27.6509279 143.9913851
  END

One important aspect to keep in mind when obtaining data for a pointing model is the interplay between telescope position and optical collimation.
If the telescope optics is not collimated when taking the data, or if collimation changed over time (due to mechanical reasons or else), one may need to recompute the pointing model either with new data or reformatting the data to be compatible.

.. _Building-an-initial-pointing-model:

Building an initial pointing model
==================================

When we first started on-sky observations with the Auxiliary Telescope, we had a small CMOS camera and an ocular, that we could fit into the camera socket on Nasmyth 1.
Because the camera field of view is small (~30 arcsec), we initially relied a lot more on the ocular to find stars and center them in the field than on the camera.

We started by observing only a handful of targets.
Although it is not possible to constrain a good pointing model with these limited datasets, we can at least remove the first order terms of the pointing error (e.g. azimuth and elevation zero points, see :ref:`fitting pointing model <Fitting-Pointing-Model>` furthermore), which then allows us to use the CMOS camera to obtain data more efficiently.

It is also important to emphasize that during this earlier stages we did not have active control of the optics collimation, so the data taken to build this initial pointing model had to be superseded later.

.. _Refining-Pointing-model:

Refining Pointing model
=======================

The main point to consider when refining the pointing model is that a good pointing model requires a good lookup table for the optics.
Nevertheless, one requires a good pointing model in order to build a good optics lookup table.

To solve this circular dependency between pointing model and optics lookup table we perform an iterative approach, improving each one of these at each iteration.

Once an initial pointing model was computed using the CMOS camera, effort was dedicated into obtaining a more active control of the optical alignment.

With the initial pointing model we where able to point and track targets more reliably over a good portion of the sky, even with the small field of view of the available camera, which allowed us to build better lookup tables for the optics.

This work was initiated with the CMOS camera and later redone with LATISS.

With the new camera installed and an initial version of pointing model and optics lookup table, we where able to sample more targets in the sky more efficiently.

.. _Pointing-model-run-202003:

Pointing model run 2020/03
--------------------------

This pointing model data was obtained with the following set of optics lookup tables;

  - 2020/03/12: `M1 pressure lookup table`_.
  - 2020/03/13: `Optics Hexapod lookup table`_.

.. _M1 pressure lookup table: https://tstn-012.lsst.io
.. _Optics Hexapod lookup table: https://tstn-013.lsst.io

The dataset, taken on the night of 2020/03/14, is reproduced bellow:

.. code-block:: text

  LSST Auxiliary Telescope, 2020 Mar 15 UTC 2 27 13
  : ALTAZ
  : ROTNR
  -30 14 40.2
  154.5139233 46.1577658 154.4096955 46.2199866 314.4605341
  357.5678314 17.9506751 357.4900009 18.0435750 288.4585002
  357.1805839 31.6160368 357.1093148 31.6954934 301.9815193
  358.2118688 56.8783782 358.1454349 56.9733136 327.0756924
  347.5999780 70.6144443 347.5398578 70.7115838 341.0912845
  323.8041930 79.9832723 323.7093333 80.0857805 351.8436522
  018.8692942 84.9767233 018.8684492 85.0679421 356.7106661
  047.6556515 70.3108171 047.6139243 70.3874748 339.8624050
  047.3214290 58.0218443 047.2620570 58.0996033 327.5545904
  043.0308213 45.3968715 042.9617598 45.4753609 314.9437026
  039.2580973 22.1207582 039.1930307 22.1978042 291.6277522
  087.7493259 18.6262800 087.6795663 18.6886360 287.7218599
  088.7768166 33.2190339 088.7122462 33.2682459 302.3067621
  094.1086071 45.7303005 094.0370805 45.7894927 314.7504970
  092.2544485 61.5521446 092.1819055 61.6152214 330.3066134
  107.6320101 76.7063788 107.5432790 76.7658342 345.5776401
  153.9140911 81.8137095 153.6679213 81.8787540 350.3691146
  144.5183097 72.1021036 144.3863160 72.1623975 341.3843738
  142.2532274 55.5229833 142.1480415 55.5841901 324.6526726
  137.5457038 30.5829845 137.4602677 30.6296798 299.5670612
  133.9886635 17.1847063 133.9075305 17.2436887 286.3343927
  132.7695614 19.6774738 132.6885209 19.7330891 288.9255259
  211.3011076 81.3446205 210.9970243 81.4277598 351.9363841
  195.4981131 70.9455152 195.3341187 71.0194850 341.0148891
  193.2916824 56.8761051 193.1663346 56.9502421 326.9930977
  185.8139290 44.5810159 185.7012314 44.6476518 314.5179672
  176.8598491 17.2785396 176.7724100 17.3439524 286.7440828
  227.9678119 30.2922359 227.8747886 30.3617034 300.8442111
  229.6222199 41.0536491 229.5200929 41.1333113 311.6391435
  233.2062101 53.4807098 233.0840475 53.5710452 324.1901714
  232.5879344 68.1675325 232.4348991 68.2571052 338.8469271
  237.0552385 81.5377692 236.7540306 81.6315329 352.5994603
  224.8167489 17.9507370 224.7245002 18.0339147 288.4724257
  267.8139227 27.3648641 267.7251956 27.4481086 299.1215216
  269.7681627 38.4861510 269.6742923 38.5720102 310.7434790
  271.5085630 53.0142770 271.4000372 53.1168265 323.8554050
  274.2468497 64.2155849 274.1284872 64.3185550 335.3203697
  266.0360257 76.5660337 265.8638645 76.6671042 348.9751496
  329.6583907 82.3539537 329.5493134 82.4577645 356.0761032
  319.2607020 67.4522271 319.1731715 67.5542916 338.2145520
  316.6631312 56.6064178 316.5753298 56.7093257 327.2345760
  313.9002173 42.8905503 313.8108478 42.9843875 313.7268993
  313.1359793 31.2112474 313.0527900 31.2975443 301.9927343
  319.2054673 17.4259367 319.1254117 17.5238048 287.9991321
  END

.. _Pointing-model-run-202002:

Pointing model run 2021/02
--------------------------

On the night of 2021/02/19 (`SUMMIT-4829`_) we took some additional data for the pointing model.

During the same run we also took new data for the optical lookup table (both for M1 pressure and hexapod).
After reducing the data and producing new lookup tables we realized that the newer data was not compatible  with previous data.
Most importantly, we noticed that the collimation coefficients (especially hexapod y-axis correction) for the new table where considerably different at lower elevation.

The new lookup table was loaded into the ATAOS component and used during the last night of the run.

The idea was to combine the new pointing data with the :ref:`previous data <Pointing-model-run-202003>` to obtain an improved model.
Unfortunately, with the updated lookup tables the data is no longer compatible, specially at lower elevation.

Furthermore, the data taken this time is slightly different than the regular procedure.
Instead of centering the start in the field of view, we decided to mark the position wherever the field landed after we performed wavefront sensing.
We also added an acquisition image just before the positions was marked.

Therefore, we have to add a post-processing step to the data, where we measure the offset required to center the target and update the pointing file by applying the offset.
We developed a notebook to perform this task, which can be found in `ts_analysis_notebook`_.

.. _SUMMIT-4829: https://jira.lsstcorp.org/browse/SUMMIT-4829
.. _ts_analysis_notebook: https://github.com/lsst-ts/ts_analysis_notebooks

.. _Fitting-Pointing-Model:

Fitting Pointing Model
======================

The pointing model fit is done using `tpoint`_.

.. _tpoint: http://www.tpointsw.uk

To facilitate use of the software we provide it as a docker image, that can be downloaded with:

.. prompt:: bash

  docker pull ts-dockerhub.lsst.org/tpoint:latest

Because `tpoint`_ generates some plots during steps of the fits, the container must be run with some special options to allow the displaying of those graphs.

On a Mac, make sure you have `XQuartz`_ installed, and that the ``Security`` option ``Allow connections from network clients``, in ``Preferences`` is enabled.
If the option is originally disabled, you will have to enabled it and restart `XQuartz`_ for it to take effect.

.. _XQuartz: https://www.xquartz.org

.. figure:: /_static/xquartz_preference.png
   :name: fig-xquartz-preference
   :target: ../_images/xquartz_preference.png
   :alt: XQuartz preference window.

   XQuartz preference window.
   In the "Security" tab, make sure "Allow connections from network clients" is enabled.

Once `XQuartz`_ is configured to allow network clients, you also have to add local host to the permission table.
To do that run the following command:

.. prompt:: bash

  xhost + 127.0.0.1

To run the container and analyze some data make sure you have the data in a directory and export it to the container.
When starting (e.g. running) the container, it is important to also pass the options to allow it to run the graphs, as state above.
The command will look something like the following:

.. prompt:: bash

  docker run -it --rm -e DISPLAY=host.docker.internal:0 -v /tmp/.X11-unix:/tmp/.X11-unix -v ~/data:/home/saluser/data ts-dockerhub.lsst.org/tpoint:latest

In the command above, it is assumed that the data is stored in a local directory ``data`` in the user home folder.
Change it appropriately to match the actual path.
The additional options (``-e DISPLAY=...`` and ``-v /tmp/...``) are the ones required to enable container UI interaction.

Once you run the command above your terminal will be inside the container and ready to run tpoint.
To do that, simple do:

.. code-block:: text

  $ tpoint

  + - - - - - - - - - - - - - - - - - - - - +
  |                 TPOINT                  |
  |   Telescope Pointing Analysis System    |
  |              Version 21.8               |
  + _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ +

  Copyright 2016 P.T.Wallace.  All rights reserved.

  Licensed to AURA Inc. for use with the 8m Large Synoptic Survey
  Telescope and 1.2m auxiliary telescope.  Supplied December 3, 2016.

  There are 73 standard pointing terms.
  Reading procedures from file procs.dat ...
  The library now contains 537 lines.
  Reading star catalog entries from file stars.dat ...
  The catalog contains 210 stars.

  TPOINT ready for use:  type HELP for assistance, END to quit.

  *

Load the data:

.. code-block:: text

  * indat data/20200314/AT_point_file_20200315T022713.dat
  LSST Auxiliary Telescope, 2020 Mar 15 UTC 2 27 13
  : ALTAZ
  : ROTNR
  -30 14 40.2
  154.5139233 46.1577658 154.4096955 46.2199866 314.4605341
  357.5678314 17.9506751 357.4900009 18.0435750 288.4585002
  357.1805839 31.6160368 357.1093148 31.6954934 301.9815193
  358.2118688 56.8783782 358.1454349 56.9733136 327.0756924
  ....
  316.6631312 56.6064178 316.5753298 56.7093257 327.2345760
  313.9002173 42.8905503 313.8108478 42.9843875 313.7268993
  313.1359793 31.2112474 313.0527900 31.2975443 301.9927343
  319.2054673 17.4259367 319.1254117 17.5238048 287.9991321
  END
  *

The first thing you may want to try is the ``fauto`` option, which will perform a full model fit automatically.

.. code-block:: text

  * fauto

  ...

After running this command you will see outputs indicating that the fit is ongoing and :ref:`plots with different diagnostics <fig-tpoint-fauto>`.
Sit back and wait until the fit is concluded (which may take some time, depending on the dataset).

.. figure:: /_static/tpoint-fauto.png
   :name: fig-tpoint-fauto
   :target: ../_images/tpoint-fauto.png
   :alt: diagnostic plots generated by tpoint

   Diagnostic plots generated by tpoint when running ``fauto`` with the :ref:`202003 <Pointing-model-run-202003>` dataset.

Once the fit is done tpoint print the final model and some quick diagnostics:

.. code-block:: text

  ...

  coeff       change     value    sigma

  1     IA         +0.014   -269.50    2.568
  2     IE                    +0.00
  3   & HACA2      +0.020    -11.89    1.982
  4   & HASA7      -0.019    -12.28    1.970
  5   & HESACE     -0.044    -11.55    2.696
  6   & HESA2      +0.004     +2.85    1.291
  7   & HESA3      -0.002     -2.53    1.116
  8   & HESE       -0.050   -223.21   17.816
  9   & HESE5      +0.238   -239.98   85.831
  10   & HESE7      +0.159   -191.78   55.371
  11   & HECE7      +0.242   -228.10  108.508
  12   & HESE8      -0.136   +136.23   62.538
  13   & HECE8      +0.010    -46.32   19.075
  14     NPAE       -0.031    -50.97    3.614
  15     CA                    +0.00
  16   & HSCA5CE8   -0.019     -6.81    2.001
  17   & HSSA6CE6   +0.009     -4.86    1.929
  18   & HSCE3      -0.039     -7.39    2.839
  19   & HSSE8      -0.012     -5.49    1.406
  20   & HSCE8      +0.004     -5.82    1.368
  21     AN         +0.002    +44.26    1.130
  22     AW         +0.033    +69.89    1.428
  23     TF         -0.431   +491.27  131.179
  24     TX10       +0.170   -126.69   50.740

  Sky RMS =   5.46
  Popn SD =   7.72

  TX10 and TF cannot reliably be distinguished.
  Observation #7 is a very weak outlier candidate.

  *

Note that ``tpoint`` provides the Root mean squares (RMS) and Power Spectra density (PSD) of the fit, to help diagnose the quality of the fit.

It is also highly recommended to run a "manual fit" of the data.
Instructions on how to perform this task can be found in `tpoint documentation`_.

.. _tpoint documentation: https://github.com/lsst-ts/tpoint_exec_161103/raw/master/tpoint.pdf


.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
