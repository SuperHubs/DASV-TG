# Difficulty Score Computation

Given a question and timestamped subtitle segments, we compute the textual
evidence dispersion score. A pretrained text encoder is used to extract
semantic representations of the question and subtitle segment:s, and the
cosine similarity between them is used as the relevance score $s_i$. For
subtitle selection, segments with $s_i \geq 0.35$ are regarded aas relevant
and at most the top 20 segments ranked by $s_i$ are retained.For the selected
segments, let $\tau_i=(t_i^s+t_i^e)/2$ denote the
center timestamp. The normalized relevance weight is computed as $\lambda_i=s_i/\sum_j s_j$, and
the weighted temporal center is $\bar{\tau}=\sum_i\lambda_i\tau_i$. The dispersion score is calculated as:

$$
D_s=\min\left(\frac{4}{T^2}\sum_i\lambda_i(\tau_i-\bar{\tau})^2,1\right)
$$

where $T$ denotes the video duration.
If no subtitle segment satisfies the relevance threshold, $D_s$ isset to 1

