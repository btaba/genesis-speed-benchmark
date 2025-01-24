
```sh
python test_mjx.py --mjxxml -B=1024

python test_genesis.py -B 1024 --mjxxml
```

Tested on RTX 4090 (steps per second):

I **think** "Genesis (no self collision)" is the right comparison to the MJX runs in terms of the solver/collisions, but of course...benchmarks are hard.

| Batch Size | 1024 | 8192 | 16384 |
|---|---|---|---|
| Genesis (no self collision) | 1.6M | 12.9M | 26.2M |
| MJX old script | 0.2M | 1.6M | 2.9M |
| MJX | 1.4M | 6.2M | 7.8M |
| MJX Newton | 1.7M | 6.5M | 7.6M |
| MJX Newton Loop Unroll 5 (LU5) | 1.7M | 7.1M | 8.7M |
| MJX Newton LU5 - No Joint Limit | 2.1M | 8.7M | 10.3M |
| MJX Newton LU5 - No Joint Limit / No Contact | 6M | - | 31.5M |


"Genesis (no collision)" should be compared to "MJX Newton LU5 - No Contact", only joint limits are being tested here.

| Batch Size | 1024 | 8192 | 16384 |
|---|---|---|---|
| Genesis (no collision) | 2.24M | 17.7M | 34.8M |
| MJX Newton LU5 - No Contact | 2.2M | 10.4M | 13.4M |
