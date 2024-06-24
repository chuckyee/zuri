========
Concepts
========

When building machine learning products in industry, our product and annotation
teams often asked, "How much data do we need to collect and label?" For the
training set size, we could compute learning curves: vary the amount of
training data and plot the model's performance on the train and test splits.
From these curves, we could infer the amount of training data required to reach
a chosen performance target. For example, "We need 2500 training samples for
the test :math:`F_1` score to reach 90%".

But what about the amount of *test* data needed? The larger the amount of test
data, the smaller the uncertainty in the computed test metric, but the tradeoff
is more time and resources must be devoted to test data annotation. Similarly,
if the total amount of data is fixed, the tradeoff is that less data is
available for model training. This problem of determining the amount of data
required to reach a desired level of significance broadly falls under the
domain of *power analysis* in the statistical practice of experiment design.

The algorithms implemented in this package allow the user to calculate how much
test data is required to reach a given level of confidence in the reported
model performance. The formulas and computations depend on the mathematical
form of the test metric: currently binary accuracy, binary precision, binary
recall and binary :math:`F_1` are supported, with extensions planned for the
future.

The underlying theory for the algorithms is built on Bayesian statistics. The
reason the Bayesian framework was selected is due to the intuitive nature of
the computed uncertainty bounds, as well as the ease with which they could be
communicated to non-technical stakeholders--the product and annotation teams.
The tradeoff is that the mathematical development can be heavy and that closed
forms can be derived only for relatively simple metrics.

By encapsulating the derivations and algorithms in a user-friendly interface,
the hope is that `zuri` will allow machine learning practitioners to easily
compute uncertainties and perform power analysis.

In the following, we discuss the pros and cons of the Bayesian approach,
followed by the derivations of the key formulas and numerical algorithms
implemented in this package.


Why Bayesian?
-------------------------------------------

In machine learning and data science projects, it's crucial to communicate
model performance and uncertainties effectively to product teams and
stakeholders. The Bayesian approach to quantifying model performance offers a
more intuitive and easier-to-communicate framework compared to frequentist
confidence intervals.

Unlike frequentist confidence intervals, which can be challenging to interpret,
the Bayesian approach treats the model parameters themselves as random
variables. This aligns better with how we naturally reason about uncertainty –
we're interested in the probability distribution of the parameter itself, given
the observed data. The Bayesian approach directly provides this posterior
distribution, making it easier to communicate the range of plausible values for
the parameter and their associated probabilities.

By working with the parameter's probability distribution, the Bayesian approach
allows for more intuitive statements like "there is a 95% probability that the
parameter lies within this range." This is in contrast to the frequentist
interpretation, which can be harder to grasp: "if we repeated the experiment
many times, 95% of the constructed confidence intervals would contain the true
parameter value." The Bayesian framing is more natural and resonates better
with non-technical stakeholders, facilitating clearer communication and
decision-making.


:math:`F_1` distribution
-------------------------------------------

Goutte and Gaussier (2005) show that the :math:`F_1` score can be expressed as
a ratio of Gamma variates:

.. math::

   F_1 = \frac{U}{U+V}, \quad \text{with} \quad
   \begin{cases}
   U \sim \Gamma(TP + \lambda, 2 \theta) \\
   V \sim \Gamma(FP + FN + 2 \lambda, \theta)
   \end{cases}

We use the convention of parameterizing the Gamma distribution with a scale
parameter :math:`\theta`, which means the PDF has a factor of
:math:`e^{-x/\theta}`.

The ratio of two Gamma distributions with differing scaling factors can be
computed as follows:

.. math::

   f_Y(Y) = \frac{\phi}{(1-(1-\phi)Y)^2}
   \cdot \beta\left( \frac{\phi Y}{1-(1-\phi)Y}; \alpha_1,\alpha_2 \right)

where :math:`U\sim\Gamma(\alpha_1,\theta_1)` and
:math:`V\sim\Gamma(\alpha_2,\theta_2)` and :math:`\phi = \theta_2/\theta_1`.
