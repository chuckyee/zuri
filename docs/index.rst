Zuri
======================================

Posterior distributions for common ML metrics.

Zuri helps you:

* compute uncertainty bounds of your model's performance
* determine whether one model is better than another
* choose the amount of test data you need (i.e. power analysis)

The metrics supported are (binary) accuracy, precision, recall and :math:`F_1`.


Quickstart
----------

Upper and lower bounds on model performance:

.. code-block:: python

   from zuri import ConfusionMatrix, posteriors

   # I've evaluated my model's performance and computed the F1 score
   tp, fp, fn = 31, 5, 2
   f1 = 2*tp / (2*tp + fp + fn)

   # What are the 95% uncertainty bounds on my model performance?
   cm = ConfusionMatrix(tp=tp, fp=fp, fn=fn)
   f1 = posteriors.f1(cm)
   ci_lower, ci_upper = f1.interval(confidence=0.95)


Probability that the new model is better than the old:

.. code-block:: python

   from zuri import ConfusionMatrix, compare

   cm1 = ConfusionMatrix(tp=50, fp=10, fn=5)   # old model
   cm2 = ConfusionMatrix(tp=40, fp=15, fn=10)  # new model

   print(f"F1 score of cm1: {cm1.f1:.2f}")
   print(f"F1 score of cm2: {cm2.f1:.2f}")

   # probability that F1(new) > F1(old)
   probability = compare.f1(cm1, cm2)
   print(f"\nProbability that F1 score of cm1 is larger than cm2: {probability:.2f}")


How much test data needs to be collected so that the uncertainty in the
reported test metric is sufficiently small?

.. code-block:: python

   from zuri import power

   power.f1(
       precision=0.8,   # guess for model precision
       recall=0.8,      # guess for model recall
       threshold=0.75,  # value F1 must exceed
       alpha=0.95,      # confidence level
       beta=0.8         # proportion of experiments deemed significant
   )


Contents
========

.. toctree::
   :maxdepth: 2

   installation
   usage
   API Reference <modules>
   contributing
   authors
   history

Indices and tables
==================
* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
