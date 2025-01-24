
```sh
python test_mjx.py --mjxxml -B=1024

python test_genesis.py -B 1024 --mjxxml
```

Tested on RTX 4090, reporting steps per second.

Below is a best effort to replicate the comparison 2.2.1 from the blog post. Only joint limits are being tested here. "Genesis (no collision)" should be compared to "MJX Newton LU5 - No Contact" (Newton solver + loop unroll 5 + disable contact). "MJX old script" roughly matches what was reported in the blog post. I can't exactly replicate the Genesis numbers from the blog post (they look somewhat ballpark except for 1024).

| Batch Size | 1024 | 8192 | 16384 |
|---|---|---|---|
| Genesis (no collision) | 2.24M | 17.7M | 34.8M |
| MJX old script | 0.2M | 1.6M | 2.9M |
| MJX Newton LU5 - No Contact | 2.2M | 10.4M | 13.4M |

Key changes made to the MJX script:

* Disable collisions with `mj_model.opt.disableflags |= mujoco.mjtDisableBit.mjDSBL_CONTACT`.
* Scan instead of Python for loop.
* Pick solver params that we actually use in MJX for training. Set similar params for Genesis.

_______________

For the runs below (which I ran earlier on), I __think__ "Genesis (no self collision)" is the best comparison to the MJX setting in terms of the solver/collisions. Only contacts with the plane and gripper are turned on. With that being said, I'm not a Genesis expert and AFAICT contype/conaffinity are not used, so take the comparison with a grain of salt.

| Batch Size | 1024 | 8192 | 16384 |
|---|---|---|---|
| Genesis (no self collision) | 1.6M | 12.9M | 26.2M |
| MJX | 1.4M | 6.2M | 7.8M |
| MJX Newton | 1.7M | 6.5M | 7.6M |
| MJX Newton Loop Unroll 5 (LU5) | 1.7M | 7.1M | 8.7M |
| MJX Newton LU5 - No Joint Limit | 2.1M | 8.7M | 10.3M |
| MJX Newton LU5 - No Joint Limit / No Contact | 6M | - | 31.5M |
