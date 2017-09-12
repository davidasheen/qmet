The qMET Project class
**********************

A qMET analysis is logically divided into interlaboratory comparison projects. A project is represented by a :py:class:`.Project` object which contains the following items:

   * Sample labels that represent the physical objects that have been distributed for measurement
   * Dataset labels that identify the origin of each set of measurement results
   * Experimental spectral data containing the measurement results on the objects that have been analyzed
   * Interspectral distance functions that will be used to calculate the spread of the data and identify outliers
   * A distribution function that will be used to estimate outliers. By default, this is a lognormal distribution, but can be any distribution function supported by :py:class:`scipy.stats.rv_continuous`.

The intent is that the user will interact primarily with the :py:class:`.Project` object, loading it with the necessary information to conduct its analysis and then using the built in methods.

Creating Projects
=================

A blank :py:class:`.Project` can be instantiated with no arguments:
   
.. code-block:: python

   my_project = qmet.Project()

This creates a :py:class:`.Project` with no data or metadata of any kind. This information can be loaded later, or it can be provided when the code is initialized:

.. code-block:: python

   my_project = qmet.Project(x_data_list=xdata,
                             Sample_names=Sample_names,
                             Data_set_names=Data_set_names,
                             distance_metric_dict=distance_metric_dict,
                             range_to_use=range_to_use
                            )

Loading data into projects
--------------------------

There are two ways to add data to a qMET Project. The first is by using the built-in adder function

.. code-block:: python
   
   my_project.data = xdata

In this case, xdata must be a string containing a filename with the data in it. By default, the file is read by :py:func:`fanndataread`, although that can be changed manually. Alternatively, the data can be directly added to the 

.. code-block:: python
   
   my_project._data = xdata

Defining distance metrics
-------------------------

The distance metrics are defined by a list of dictionaries. Each dictionary must have the name of the metric as a text string and the function used to call the metric. The function must either be a callable that accepts two inputs or a string that is recognized by scipy.spatial.pdist(). The following are two examples:

.. code-block:: python
   
   jeffries = r'Symmetric Kullback-Liebler'
   mahalanobis = r'Mahalanobis'
   nmr_distance_metrics = [dict(metric=mahalanobis,function='mahalanobis'), #'mahalanobis' is recognized by pdist()
                           dict(metric=jeffries,function=qmet.jeffries), #qmet.jeffries is a distance included in this package
                          ]

Project workflow
================

Once the project has been created with the basic data and metadata needed for the analysis, the basic workflow is as follows:

.. code-block:: python
   
   my_project.process_mahalanobis() #This calculated the mean and covariance of the samples and is only needed if the Mahalanobis distance is included in the project
   my_project.set_distances()
   my_project.set_zscores()
   my_project.set_zscore_principal_components()
   my_project.find_all_outliers()

This will, in order:

   * Calculate the interspectral distances
   * Fit the project's distribution function to the distance data and calculate the corresponding scores.
   * Conduct a principal components analysis on the scores and compute the projected statistical distance
   * Use the projected statistical distance to determine the data set outliers.


Documentation
=============

Method summary
--------------

.. currentmodule:: qmet

Analysis functions
++++++++++++++++++

.. autosummary::
   Project
   Project.set_distances
   Project.set_zscores
   Project.set_zscore_principal_components
   Project.find_all_outliers

Plotting functions
++++++++++++++++++
   
.. autosummary::
   Project.plot_distance_fig
   Project.plot_zscore_distances
   Project.plot_distance_histograms
   Project.plot_zscore_outliers
   Project.plot_projected_zscores
   Project.plot_zscore_loadings

   
Full documentation
------------------
   
.. autoclass:: Project
   
   .. automethod:: Project.set_distances()
   .. automethod:: Project.set_zscores()
   .. automethod:: Project.set_zscore_principal_components()
   .. automethod:: Project.find_all_outliers()
   .. automethod:: Project.plot_distance_fig()
   .. automethod:: Project.plot_zscore_distances()
   .. automethod:: Project.plot_distance_histograms()
   .. automethod:: Project.plot_zscore_outliers()
   .. automethod:: Project.plot_projected_zscores()
   .. automethod:: Project.plot_zscore_loadings()
