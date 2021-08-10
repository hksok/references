```python
import tensorflow as tf
import tensorflow_probability as tfp
tfd = tfp.distributions

normal = tfd.Normal(loc=0., scale=1.)
print(normal)

# tfp.distributions.Normal("Normal", batch_shape=[], event_shape=[],)

normal.sample()

# tf.Tensor(-0.4975249, shape=(), dtype=float32)

normal.sample(3)

# tf.Tensor([0.9273644, 0.60512847, -0.09919496], shape=(3,), dtype=float32)

normal.prob(0.5)

# tf.Tensor(0.35206532, shape=(), dtype=float32)

normal.log_prob(0.5)

# tf.Tensor(-1.0439385, shape=(), dtype=float32)

bernoulli = tfd.Bernoulli(probs=0.7)
print(bernoulli)

# tfp.distributions.Bernoulli("Bernoulli", batch_shape=[], event_shape=[], dtype=int32)

bernoulli = tfd.Bernoulli(logits=0.847)
bernoulli.sample(3)

# tf.Tensor([0 1 0], shape=(3,), dtype=int32)

bernoulli.prob(1)

# tf.Tensor(0.6999374, shape=(), dtype=float32)

bernoulli.log_prob(1)

# tf.Tensor(-0.35676435, shape=(), dtype=float32)

batched_bernoulli = tfd.Bernoulli(probs=[0.4, 0.5])
print(batched_bernoulli)

# tfp.distributions.Bernoulli("Bernoulli", batch_shape=[2], event_shape=[], dtype=int32)

batched_bernoulli.batch_shape

# (2,)

batched_bernoulli.sample(3)

# tf.Tensor(
#   [[0 0]
#    [0 1]
#    [1 0]], shape=(3, 2), dtype=int32)

batched_bernoulli.prob([1, 1])

# tf.Tensor([0.4 0.5], shape=(2,), dtype=float32)

batched_bernoulli.log_prob([1, 1])

# tf.Tensor([-0.9162907 -0.6931472], shape=(2,), dtype=float32)
```
