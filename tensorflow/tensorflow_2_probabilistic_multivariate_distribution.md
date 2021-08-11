```python
import tensorflow as tf
import tensorflow_probability as tfp
tfd = tfp.distributions

mv_normal = tfd.MultivariateNormalDiag(loc=[-1, 0.5], scale_diag=[1., 1.5])
print(my_normal)

# tfp.distribution.MultivariateNormalDiag(
# "MultivariateNormalDiag", batch_shape=[], event_shape=[2], dtype=float32)

mv_normal.event_shape # (2,)

mv_normal.sample(3)

# tf.Tensor(
# [[-0.4926784, 1.6360369]
#  [-2.029356, 1.7328173]
#  [-1.7517667, 0.01889575]], shape=(3, 2), dtype=float32)

batched_normal = tfd.Normal(loc=[-1. 0.5], scale=[1., 1.5])
print(batched_normal)

# tfp.distributions.Normal("Normal", batch_shape=[2], event_shape=[], dtype=float32)

batched_normal.sample(3)

# tf.Tensor(
# [[-1.614592, 1.0899202]
#  [-2.894516, -2.324822]
#  [-1.8110247, 0.29011112]], shape=(3, 2), dtype=float32)

batched_mv_normal = tfd.MultivariateNormalDiag(
    loc=[[-1., 0.5], [2., 0.], [-0.5, 1.5]],
    scale_diag=[[1., 1.5], [2., 0.5], [1., 1.]])
print(batched_mv_normal)

# tfp.distributions.MultivariateNormalDiag(
# "MultivariateNormalDiag", batch_shape=[3], event_shape=[2], dtype=float32)

batched_mv_normal.sample(2)

# tf.Tensor(
#   [[[-0.9306277 -0.12525171]
#     [ 3.2052054 -0.5585985 ]
#     [-1.1087043  1.9413768 ]]
#    
#    [[-0.15895575 1.5787622]
#     [ 0.83570756 -0.369511]
#     [-1.7262809  0.47082818]]], shape=(2, 3, 2), dtype=float32)
```