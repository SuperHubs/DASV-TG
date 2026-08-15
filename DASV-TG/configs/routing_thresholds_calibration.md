# Routing Thresholds Calibration

Using the simple/complex difficulty labels in the training split, we calibrate
the thresholds from the corresponding difficulty-score distributions.

Specifically,

$$
\theta_1=P_{95}(r_{\mathrm{simple}})=0.47
$$

and

$$
\theta_2=P_{5}(r_{\mathrm{complex}})=0.59
$$

These thresholds are fixed during validation and test inference, and no model
parameters are updated during the calibration process.
