
```sh
python test_mjx.py --mjxxml -B=1024

python test_genesis.py -B 1024 --mjxxml
```

Tested on 1x RTX 4090, reporting steps per second.

Below is a best effort to replicate the comparison 2.2.1 from the blog post. Only joint limits are being tested here. "MJX old script" roughly matches what was reported in the blog post. "Genesis (no collision)" should be compared to any of the  "MJX ... No Contact" rows; the solver (CG, Newton) and loop unroll parameters are varied. I can't exactly replicate the Genesis numbers from the blog post in 2.2.1, but I'm using the script above which is loading the same XML.

| Batch Size | 1024 | 8192 | 16384 |
|---|---|---|---|
| Genesis (no collision) | 2.24M | 17.7M | 34.8M |
| MJX old script | 0.2M | 1.6M | 2.9M |
| MJX CG - No Contact | 2.1M | - | - |
| MJX CG - Loop Unroll 5 - No Contact | 2.2M | 11.4M | 15.5M |
| MJX Newton - Loop Unroll 5 - No Contact | 2.2M | 10.4M | 13.4M |


Key changes made to the MJX script:

* Disable collisions with `mj_model.opt.disableflags |= mujoco.mjtDisableBit.mjDSBL_CONTACT`.
* Scan instead of Python for loop.
* Pick solver params that we actually use in MJX for training (`iterations=5`, `ls_iterations=8`). Set the same params for the Genesis script.

For a full code diff, see [here](https://github.com/btaba/genesis-speed-benchmark/compare/main...btaba:genesis-speed-benchmark:test?expand=1).

NOTE: missing values are due to JAX slow compilation alarm (need to open a bug report)
