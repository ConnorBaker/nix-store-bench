# nix-store-bench

Benchmark results collected as part of:

- https://github.com/ConnorBaker/nix/tree/vibe-coding/optimise-and-gc-throughput
- https://github.com/ConnorBaker/nix/tree/vibe-coding/optimise-and-gc-throughput-baseline-bench-rig-616df9797

System:

```console
$ uname -a
Linux cbaker2-3680-ubuntu 7.0.0-15-generic #15-Ubuntu SMP PREEMPT_DYNAMIC Wed Apr 22 16:06:43 UTC 2026 x86_64 GNU/Linux
$ lscpu
Architecture:                x86_64
  CPU op-mode(s):            32-bit, 64-bit
  Address sizes:             46 bits physical, 48 bits virtual
  Byte Order:                Little Endian
CPU(s):                      32
  On-line CPU(s) list:       0-31
Vendor ID:                   GenuineIntel
  Model name:                Intel(R) Core(TM) i9-14900K
```

Using EXT4 on a 1TB NVMe drive. KVM was enabled.

The python script used for all of these comparisons (../nix/tests/nixos/nix-store-bench/bench.py) is from the `vibe-coding/optimise-and-gc-throughput` branch.

```console
> python3 ../nix/tests/nixos/nix-store-bench/bench.py summary-matrix --results-dir ubuntu-14900k-nvme-ext4/before --format md
```

| bench | dispatch | fs | throttle | layout | replica | n | t | iters | mean | p99 | stddev | cv |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 282.886ms | 289.010ms | 5.883ms | 2% |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 6.768s | 11.258s | 4.008s | 59%* |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 27.724s | 27.839s | 158.872ms | 1% |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 279.971ms | 283.529ms | 3.409ms | 1% |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 2.991s | 3.740s | 668.187ms | 22%* |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 17.925s | 19.441s | 1.408s | 8% |
| gc_barabasi | syscall | btrfs | none | flat | multi | 2000 | 4 | 3 | 279.514ms | 283.976ms | 4.758ms | 2% |
| gc_barabasi | syscall | btrfs | none | flat | multi | 10000 | 4 | 3 | 2.102s | 2.297s | 174.038ms | 8% |
| gc_barabasi | syscall | btrfs | none | flat | multi | 50000 | 4 | 3 | 13.773s | 13.942s | 223.040ms | 2% |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 277.189ms | 281.547ms | 4.392ms | 2% |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 2.324s | 2.595s | 242.769ms | 10% |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 14.942s | 15.580s | 590.537ms | 4% |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 186.773ms | 196.586ms | 8.766ms | 5% |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 3.762s | 3.784s | 20.214ms | 1% |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 59.735s | 62.939s | 3.026s | 5% |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 186.397ms | 190.696ms | 3.850ms | 2% |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 1.945s | 1.969s | 30.165ms | 2% |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 18.363s | 18.917s | 503.711ms | 3% |
| gc_barabasi | syscall | ext4 | none | flat | multi | 2000 | 4 | 3 | 186.538ms | 189.553ms | 3.591ms | 2% |
| gc_barabasi | syscall | ext4 | none | flat | multi | 10000 | 4 | 3 | 1.402s | 1.404s | 1.832ms | 0% |
| gc_barabasi | syscall | ext4 | none | flat | multi | 50000 | 4 | 3 | 10.526s | 10.674s | 142.639ms | 1% |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 186.547ms | 190.437ms | 3.723ms | 2% |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 1.560s | 1.574s | 12.357ms | 1% |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 11.140s | 11.242s | 102.444ms | 1% |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 2000 | 4 | 3 | 125.182ms | 131.728ms | 6.046ms | 5% |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 10000 | 4 | 3 | 733.591ms | 744.766ms | 11.945ms | 2% |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 50000 | 4 | 3 | 4.554s | 4.575s | 26.276ms | 1% |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 213.923ms | 218.636ms | 5.619ms | 3% |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 4.425s | 4.446s | 25.256ms | 1% |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 33.155s | 33.555s | 402.463ms | 1% |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 2000 | 4 | 3 | 217.106ms | 220.406ms | 4.034ms | 2% |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 10000 | 4 | 3 | 2.607s | 2.669s | 55.523ms | 2% |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 50000 | 4 | 3 | 21.476s | 21.967s | 828.236ms | 4% |
| gc_barabasi | syscall | xfs | none | flat | multi | 2000 | 4 | 3 | 220.132ms | 223.368ms | 2.984ms | 1% |
| gc_barabasi | syscall | xfs | none | flat | multi | 10000 | 4 | 3 | 2.067s | 2.116s | 54.700ms | 3% |
| gc_barabasi | syscall | xfs | none | flat | multi | 50000 | 4 | 3 | 19.122s | 19.411s | 318.789ms | 2% |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 2000 | 4 | 3 | 216.068ms | 222.361ms | 5.925ms | 3% |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 10000 | 4 | 3 | 2.196s | 2.221s | 37.888ms | 2% |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 50000 | 4 | 3 | 20.479s | 20.928s | 648.701ms | 3% |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 334.689ms | 340.587ms | 7.877ms | 2% |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 2.476s | 2.498s | 19.453ms | 1% |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 16.098s | 17.043s | 954.188ms | 6% |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 2000 | 4 | 3 | 326.477ms | 333.752ms | 10.290ms | 3% |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 10000 | 4 | 3 | 2.477s | 2.494s | 28.223ms | 1% |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 50000 | 4 | 3 | 16.589s | 16.841s | 348.503ms | 2% |
| gc_barabasi | syscall | zfs | none | flat | multi | 2000 | 4 | 3 | 332.868ms | 339.557ms | 7.097ms | 2% |
| gc_barabasi | syscall | zfs | none | flat | multi | 10000 | 4 | 3 | 2.472s | 2.490s | 17.406ms | 1% |
| gc_barabasi | syscall | zfs | none | flat | multi | 50000 | 4 | 3 | 16.602s | 17.375s | 713.259ms | 4% |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 2000 | 4 | 3 | 340.945ms | 356.835ms | 14.397ms | 4% |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 10000 | 4 | 3 | 2.470s | 2.513s | 54.229ms | 2% |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 50000 | 4 | 3 | 16.400s | 16.749s | 327.349ms | 2% |
| gc_clusters | syscall | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 281.001ms | 286.198ms | 4.688ms | 2% |
| gc_clusters | syscall | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 5.835s | 8.411s | 2.299s | 39%* |
| gc_clusters | syscall | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 32.524s | 38.732s | 5.556s | 17% |
| gc_clusters | syscall | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 276.985ms | 283.457ms | 5.813ms | 2% |
| gc_clusters | syscall | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 2.856s | 3.358s | 448.439ms | 16% |
| gc_clusters | syscall | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 17.171s | 17.578s | 684.348ms | 4% |
| gc_clusters | syscall | btrfs | none | flat | multi | 2000 | 4 | 3 | 275.150ms | 278.855ms | 3.312ms | 1% |
| gc_clusters | syscall | btrfs | none | flat | multi | 10000 | 4 | 3 | 2.185s | 2.304s | 124.799ms | 6% |
| gc_clusters | syscall | btrfs | none | flat | multi | 50000 | 4 | 3 | 12.531s | 12.977s | 657.597ms | 5% |
| gc_clusters | syscall | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 276.230ms | 278.674ms | 3.070ms | 1% |
| gc_clusters | syscall | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 2.305s | 2.553s | 220.829ms | 10% |
| gc_clusters | syscall | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 13.548s | 14.100s | 496.470ms | 4% |
| gc_clusters | syscall | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 181.533ms | 187.421ms | 6.397ms | 4% |
| gc_clusters | syscall | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 3.764s | 3.775s | 10.827ms | 0% |
| gc_clusters | syscall | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 63.949s | 65.312s | 1.371s | 2% |
| gc_clusters | syscall | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 182.628ms | 187.329ms | 4.815ms | 3% |
| gc_clusters | syscall | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 1.952s | 1.961s | 10.932ms | 1% |
| gc_clusters | syscall | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 18.693s | 18.984s | 262.112ms | 1% |
| gc_clusters | syscall | ext4 | none | flat | multi | 2000 | 4 | 3 | 183.262ms | 195.249ms | 10.746ms | 6% |
| gc_clusters | syscall | ext4 | none | flat | multi | 10000 | 4 | 3 | 1.374s | 1.393s | 18.193ms | 1% |
| gc_clusters | syscall | ext4 | none | flat | multi | 50000 | 4 | 3 | 10.094s | 10.129s | 37.973ms | 0% |
| gc_clusters | syscall | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 178.955ms | 184.769ms | 5.712ms | 3% |
| gc_clusters | syscall | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 1.548s | 1.556s | 6.727ms | 0% |
| gc_clusters | syscall | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 10.628s | 10.790s | 144.751ms | 1% |
| gc_clusters | syscall | tmpfs | none | flat | multi | 2000 | 4 | 3 | 118.200ms | 126.829ms | 7.947ms | 7% |
| gc_clusters | syscall | tmpfs | none | flat | multi | 10000 | 4 | 3 | 673.827ms | 676.525ms | 4.481ms | 1% |
| gc_clusters | syscall | tmpfs | none | flat | multi | 50000 | 4 | 3 | 4.021s | 4.040s | 26.888ms | 1% |
| gc_clusters | syscall | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 220.428ms | 226.009ms | 5.330ms | 2% |
| gc_clusters | syscall | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 4.533s | 4.552s | 28.647ms | 1% |
| gc_clusters | syscall | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 32.379s | 32.969s | 907.343ms | 3% |
| gc_clusters | syscall | xfs | io2 | flat | multi | 2000 | 4 | 3 | 223.122ms | 230.022ms | 6.378ms | 3% |
| gc_clusters | syscall | xfs | io2 | flat | multi | 10000 | 4 | 3 | 2.689s | 2.762s | 120.678ms | 4% |
| gc_clusters | syscall | xfs | io2 | flat | multi | 50000 | 4 | 3 | 21.336s | 21.700s | 337.029ms | 2% |
| gc_clusters | syscall | xfs | none | flat | multi | 2000 | 4 | 3 | 223.645ms | 228.685ms | 4.503ms | 2% |
| gc_clusters | syscall | xfs | none | flat | multi | 10000 | 4 | 3 | 1.930s | 1.976s | 40.948ms | 2% |
| gc_clusters | syscall | xfs | none | flat | multi | 50000 | 4 | 3 | 18.617s | 19.692s | 960.945ms | 5% |
| gc_clusters | syscall | xfs | nvme | flat | multi | 2000 | 4 | 3 | 219.659ms | 221.791ms | 2.043ms | 1% |
| gc_clusters | syscall | xfs | nvme | flat | multi | 10000 | 4 | 3 | 2.261s | 2.312s | 63.733ms | 3% |
| gc_clusters | syscall | xfs | nvme | flat | multi | 50000 | 4 | 3 | 18.612s | 19.677s | 1.157s | 6% |
| gc_clusters | syscall | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 324.376ms | 325.922ms | 2.350ms | 1% |
| gc_clusters | syscall | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 2.474s | 2.523s | 56.861ms | 2% |
| gc_clusters | syscall | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 18.385s | 20.578s | 1.970s | 11% |
| gc_clusters | syscall | zfs | io2 | flat | multi | 2000 | 4 | 3 | 320.751ms | 323.935ms | 5.140ms | 2% |
| gc_clusters | syscall | zfs | io2 | flat | multi | 10000 | 4 | 3 | 2.558s | 2.567s | 15.022ms | 1% |
| gc_clusters | syscall | zfs | io2 | flat | multi | 50000 | 4 | 3 | 17.431s | 17.779s | 341.953ms | 2% |
| gc_clusters | syscall | zfs | none | flat | multi | 2000 | 4 | 3 | 328.787ms | 334.603ms | 7.816ms | 2% |
| gc_clusters | syscall | zfs | none | flat | multi | 10000 | 4 | 3 | 2.525s | 2.546s | 28.187ms | 1% |
| gc_clusters | syscall | zfs | none | flat | multi | 50000 | 4 | 3 | 17.168s | 17.509s | 355.973ms | 2% |
| gc_clusters | syscall | zfs | nvme | flat | multi | 2000 | 4 | 3 | 342.369ms | 374.972ms | 29.552ms | 9% |
| gc_clusters | syscall | zfs | nvme | flat | multi | 10000 | 4 | 3 | 2.486s | 2.494s | 7.645ms | 0% |
| gc_clusters | syscall | zfs | nvme | flat | multi | 50000 | 4 | 3 | 16.958s | 17.379s | 634.982ms | 4% |
| optimise | none | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 521.105ms | 532.434ms | 10.139ms | 2% |
| optimise | none | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 2.823s | 2.859s | 33.488ms | 1% |
| optimise | none | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 14.997s | 15.946s | 866.678ms | 6% |
| optimise | none | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 526.739ms | 537.980ms | 11.739ms | 2% |
| optimise | none | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 2.751s | 2.784s | 30.382ms | 1% |
| optimise | none | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 16.871s | 18.345s | 2.225s | 13% |
| optimise | none | btrfs | none | flat | multi | 2000 | 4 | 3 | 525.276ms | 545.039ms | 17.739ms | 3% |
| optimise | none | btrfs | none | flat | multi | 10000 | 4 | 3 | 2.751s | 2.775s | 28.670ms | 1% |
| optimise | none | btrfs | none | flat | multi | 50000 | 4 | 3 | 15.890s | 18.145s | 2.018s | 13% |
| optimise | none | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 532.872ms | 541.345ms | 10.090ms | 2% |
| optimise | none | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 2.796s | 2.813s | 15.862ms | 1% |
| optimise | none | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 15.689s | 18.092s | 2.145s | 14% |
| optimise | none | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 356.717ms | 357.572ms | 826.785µs | 0% |
| optimise | none | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 1.967s | 1.992s | 22.581ms | 1% |
| optimise | none | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 61.097s | 63.239s | 2.144s | 4% |
| optimise | none | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 359.739ms | 363.736ms | 3.637ms | 1% |
| optimise | none | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 1.999s | 2.017s | 18.621ms | 1% |
| optimise | none | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 19.901s | 20.052s | 134.694ms | 1% |
| optimise | none | ext4 | none | flat | multi | 2000 | 4 | 3 | 360.837ms | 362.986ms | 2.217ms | 1% |
| optimise | none | ext4 | none | flat | multi | 10000 | 4 | 3 | 2.043s | 2.053s | 9.944ms | 0% |
| optimise | none | ext4 | none | flat | multi | 50000 | 4 | 3 | 12.539s | 12.641s | 100.852ms | 1% |
| optimise | none | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 362.697ms | 365.887ms | 3.083ms | 1% |
| optimise | none | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 1.967s | 2.001s | 30.012ms | 2% |
| optimise | none | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 12.473s | 12.588s | 104.215ms | 1% |
| optimise | none | tmpfs | none | flat | multi | 2000 | 4 | 3 | 245.909ms | 251.746ms | 5.352ms | 2% |
| optimise | none | tmpfs | none | flat | multi | 10000 | 4 | 3 | 1.223s | 1.226s | 3.455ms | 0% |
| optimise | none | tmpfs | none | flat | multi | 50000 | 4 | 3 | 6.176s | 6.183s | 6.534ms | 0% |
| optimise | none | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 425.929ms | 430.261ms | 6.657ms | 2% |
| optimise | none | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 2.885s | 2.957s | 78.039ms | 3% |
| optimise | none | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 19.428s | 19.829s | 409.610ms | 2% |
| optimise | none | xfs | io2 | flat | multi | 2000 | 4 | 3 | 423.132ms | 430.390ms | 6.981ms | 2% |
| optimise | none | xfs | io2 | flat | multi | 10000 | 4 | 3 | 3.012s | 3.204s | 184.231ms | 6% |
| optimise | none | xfs | io2 | flat | multi | 50000 | 4 | 3 | 20.068s | 21.231s | 1.090s | 5% |
| optimise | none | xfs | none | flat | multi | 2000 | 4 | 3 | 455.630ms | 534.871ms | 70.741ms | 16% |
| optimise | none | xfs | none | flat | multi | 10000 | 4 | 3 | 2.908s | 2.947s | 36.149ms | 1% |
| optimise | none | xfs | none | flat | multi | 50000 | 4 | 3 | 19.446s | 19.877s | 442.577ms | 2% |
| optimise | none | xfs | nvme | flat | multi | 2000 | 4 | 3 | 520.010ms | 586.633ms | 92.217ms | 18% |
| optimise | none | xfs | nvme | flat | multi | 10000 | 4 | 3 | 2.837s | 2.858s | 19.588ms | 1% |
| optimise | none | xfs | nvme | flat | multi | 50000 | 4 | 3 | 19.573s | 19.987s | 378.991ms | 2% |
| optimise | none | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 553.178ms | 563.271ms | 9.208ms | 2% |
| optimise | none | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 3.049s | 3.062s | 12.042ms | 0% |
| optimise | none | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 15.705s | 15.911s | 184.751ms | 1% |
| optimise | none | zfs | io2 | flat | multi | 2000 | 4 | 3 | 557.282ms | 563.949ms | 9.868ms | 2% |
| optimise | none | zfs | io2 | flat | multi | 10000 | 4 | 3 | 3.025s | 3.069s | 46.887ms | 2% |
| optimise | none | zfs | io2 | flat | multi | 50000 | 4 | 3 | 15.508s | 15.617s | 114.408ms | 1% |
| optimise | none | zfs | none | flat | multi | 2000 | 4 | 3 | 567.753ms | 583.065ms | 21.313ms | 4% |
| optimise | none | zfs | none | flat | multi | 10000 | 4 | 3 | 2.980s | 3.009s | 41.441ms | 1% |
| optimise | none | zfs | none | flat | multi | 50000 | 4 | 3 | 15.443s | 15.557s | 132.259ms | 1% |
| optimise | none | zfs | nvme | flat | multi | 2000 | 4 | 3 | 545.442ms | 561.998ms | 14.972ms | 3% |
| optimise | none | zfs | nvme | flat | multi | 10000 | 4 | 3 | 3.053s | 3.101s | 55.841ms | 2% |
| optimise | none | zfs | nvme | flat | multi | 50000 | 4 | 3 | 15.442s | 15.456s | 22.673ms | 0% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | multi | 2000 | 4 | 3! | 118.146ms | 122.105ms | 3.534ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 2.673s | 2.722s | 54.115ms | 2% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 53.875s | 57.496s | 4.215s | 8% |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | multi | 2000 | 4 | 3! | 118.473ms | 122.567ms | 3.661ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 2.756s | 2.848s | 90.875ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 45.642s | 46.257s | 906.933ms | 2% |
| optimise_with_concurrent_gc | none | btrfs | none | flat | multi | 2000 | 4 | 3! | 119.666ms | 122.623ms | 3.002ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | none | flat | multi | 10000 | 4 | 3 | 2.711s | 2.762s | 71.731ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | none | flat | multi | 50000 | 4 | 3 | 46.578s | 47.019s | 662.480ms | 1% |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | multi | 2000 | 4 | 3! | 118.610ms | 123.620ms | 4.473ms | 4% |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | multi | 10000 | 4 | 3! | 1.862s | 2.710s | 1.459s | 78%* |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 46.234s | 47.880s | 1.470s | 3% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | multi | 2000 | 4 | 3! | 118.314ms | 120.762ms | 3.120ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 2.005s | 2.032s | 27.969ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 41.262s | 42.367s | 1.156s | 3% |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | multi | 2000 | 4 | 3! | 120.856ms | 126.001ms | 5.329ms | 4% |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 1.971s | 2.065s | 83.631ms | 4% |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 34.544s | 34.875s | 304.896ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | none | flat | multi | 2000 | 4 | 3! | 83.698ms | 121.139ms | 59.507ms | 71%* |
| optimise_with_concurrent_gc | none | ext4 | none | flat | multi | 10000 | 4 | 3 | 2.040s | 2.101s | 53.779ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | none | flat | multi | 50000 | 4 | 3 | 33.137s | 33.332s | 173.892ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | multi | 2000 | 4 | 3! | 137.493ms | 174.616ms | 33.296ms | 24%* |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 1.975s | 2.014s | 38.000ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 33.772s | 34.212s | 462.959ms | 1% |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | multi | 2000 | 4 | 3! | 118.218ms | 121.519ms | 2.947ms | 2% |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | multi | 10000 | 4 | 3! | 1.030s | 1.466s | 750.898ms | 73%* |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | multi | 50000 | 4 | 3 | 23.379s | 23.629s | 232.376ms | 1% |
| optimise_with_concurrent_gc | none | xfs | gp3 | flat | multi | 2000 | 4 | 3! | 117.618ms | 121.526ms | 3.489ms | 3% |
| optimise_with_concurrent_gc | none | xfs | gp3 | flat | multi | 10000 | 4 | 3! | 132.629ms | 169.860ms | 54.784ms | 41%* |
| optimise_with_concurrent_gc | none | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 44.093s | 44.494s | 428.922ms | 1% |
| optimise_with_concurrent_gc | none | xfs | io2 | flat | multi | 2000 | 4 | 3! | 118.459ms | 122.654ms | 3.860ms | 3% |
| optimise_with_concurrent_gc | none | xfs | io2 | flat | multi | 10000 | 4 | 3! | 126.324ms | 160.160ms | 58.273ms | 46%* |
| optimise_with_concurrent_gc | none | xfs | io2 | flat | multi | 50000 | 4 | 3 | 43.621s | 44.317s | 643.854ms | 1% |
| optimise_with_concurrent_gc | none | xfs | none | flat | multi | 2000 | 4 | 3! | 117.994ms | 122.626ms | 4.152ms | 4% |
| optimise_with_concurrent_gc | none | xfs | none | flat | multi | 10000 | 4 | 3! | 126.216ms | 162.376ms | 58.547ms | 46%* |
| optimise_with_concurrent_gc | none | xfs | none | flat | multi | 50000 | 4 | 3 | 44.542s | 45.302s | 690.391ms | 2% |
| optimise_with_concurrent_gc | none | xfs | nvme | flat | multi | 2000 | 4 | 3! | 116.426ms | 120.192ms | 3.368ms | 3% |
| optimise_with_concurrent_gc | none | xfs | nvme | flat | multi | 10000 | 4 | 3! | 161.988ms | 164.303ms | 3.368ms | 2% |
| optimise_with_concurrent_gc | none | xfs | nvme | flat | multi | 50000 | 4 | 3 | 43.608s | 43.755s | 199.874ms | 0% |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | multi | 2000 | 4 | 3! | 122.315ms | 127.285ms | 4.438ms | 4% |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 2.278s | 2.337s | 65.441ms | 3% |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 36.108s | 36.519s | 387.855ms | 1% |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | multi | 2000 | 4 | 3! | 123.325ms | 125.024ms | 1.742ms | 1% |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | multi | 10000 | 4 | 3 | 2.288s | 2.409s | 108.261ms | 5% |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | multi | 50000 | 4 | 3 | 35.799s | 36.049s | 223.088ms | 1% |
| optimise_with_concurrent_gc | none | zfs | none | flat | multi | 2000 | 4 | 3! | 122.425ms | 125.242ms | 2.526ms | 2% |
| optimise_with_concurrent_gc | none | zfs | none | flat | multi | 10000 | 4 | 3! | 1.599s | 2.405s | 1.220s | 76%* |
| optimise_with_concurrent_gc | none | zfs | none | flat | multi | 50000 | 4 | 3 | 36.447s | 37.099s | 588.815ms | 2% |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | multi | 2000 | 4 | 3! | 128.969ms | 137.002ms | 8.877ms | 7% |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | multi | 10000 | 4 | 3 | 2.264s | 2.290s | 33.383ms | 1% |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | multi | 50000 | 4 | 3 | 35.623s | 35.852s | 345.995ms | 1% |

```
# `!` next to iters = cell reported caught throws; row is suspect
```

```console
> python3 ../nix/tests/nixos/nix-store-bench/bench.py summary-matrix --results-dir ubuntu-14900k-nvme-ext4/after --format md
```

| bench | dispatch | fs | throttle | layout | replica | n | t | iters | mean | p99 | stddev | cv |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| gc_barabasi | iouring | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 314.895ms | 320.857ms | 5.359ms | 2% |
| gc_barabasi | iouring | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 4.639s | 7.583s | 2.628s | 57%* |
| gc_barabasi | iouring | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 30.535s | 32.318s | 2.025s | 7% |
| gc_barabasi | iouring | btrfs | gp3 | flat | single | 2000 | 4 | 3 | 332.088ms | 338.211ms | 9.454ms | 3% |
| gc_barabasi | iouring | btrfs | gp3 | flat | single | 10000 | 4 | 3 | 5.016s | 8.226s | 2.865s | 57%* |
| gc_barabasi | iouring | btrfs | gp3 | flat | single | 50000 | 4 | 3 | 43.041s | 49.245s | 6.290s | 15% |
| gc_barabasi | iouring | btrfs | gp3 | sharded | multi | 2000 | 4 | 3 | 332.709ms | 338.265ms | 4.973ms | 1% |
| gc_barabasi | iouring | btrfs | gp3 | sharded | multi | 10000 | 4 | 3 | 4.740s | 7.571s | 2.528s | 53%* |
| gc_barabasi | iouring | btrfs | gp3 | sharded | multi | 50000 | 4 | 3 | 31.081s | 32.240s | 1.903s | 6% |
| gc_barabasi | iouring | btrfs | gp3 | sharded | single | 2000 | 4 | 3 | 334.293ms | 339.673ms | 5.044ms | 2% |
| gc_barabasi | iouring | btrfs | gp3 | sharded | single | 10000 | 4 | 3 | 5.149s | 8.329s | 2.839s | 55%* |
| gc_barabasi | iouring | btrfs | gp3 | sharded | single | 50000 | 4 | 3 | 38.637s | 52.404s | 12.329s | 32%* |
| gc_barabasi | iouring | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 313.933ms | 319.327ms | 4.935ms | 2% |
| gc_barabasi | iouring | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 2.517s | 2.964s | 400.660ms | 16% |
| gc_barabasi | iouring | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 18.935s | 19.730s | 741.104ms | 4% |
| gc_barabasi | iouring | btrfs | io2 | flat | single | 2000 | 4 | 3 | 327.594ms | 332.420ms | 4.389ms | 1% |
| gc_barabasi | iouring | btrfs | io2 | flat | single | 10000 | 4 | 3 | 2.778s | 3.315s | 483.532ms | 17% |
| gc_barabasi | iouring | btrfs | io2 | flat | single | 50000 | 4 | 3 | 22.675s | 25.024s | 2.101s | 9% |
| gc_barabasi | iouring | btrfs | io2 | sharded | multi | 2000 | 4 | 3 | 318.429ms | 329.603ms | 10.112ms | 3% |
| gc_barabasi | iouring | btrfs | io2 | sharded | multi | 10000 | 4 | 3 | 2.590s | 3.073s | 431.516ms | 17% |
| gc_barabasi | iouring | btrfs | io2 | sharded | multi | 50000 | 4 | 3 | 18.561s | 19.257s | 878.173ms | 5% |
| gc_barabasi | iouring | btrfs | io2 | sharded | single | 2000 | 4 | 3 | 334.300ms | 340.280ms | 5.685ms | 2% |
| gc_barabasi | iouring | btrfs | io2 | sharded | single | 10000 | 4 | 3 | 2.996s | 3.386s | 372.568ms | 12% |
| gc_barabasi | iouring | btrfs | io2 | sharded | single | 50000 | 4 | 3 | 21.852s | 24.999s | 2.816s | 13% |
| gc_barabasi | iouring | btrfs | none | flat | multi | 2000 | 4 | 3 | 323.562ms | 334.223ms | 13.366ms | 4% |
| gc_barabasi | iouring | btrfs | none | flat | multi | 10000 | 4 | 3 | 1.960s | 2.059s | 114.982ms | 6% |
| gc_barabasi | iouring | btrfs | none | flat | multi | 50000 | 4 | 3 | 15.397s | 15.923s | 557.131ms | 4% |
| gc_barabasi | iouring | btrfs | none | flat | single | 2000 | 4 | 3 | 325.111ms | 330.090ms | 4.638ms | 1% |
| gc_barabasi | iouring | btrfs | none | flat | single | 10000 | 4 | 3 | 2.174s | 2.249s | 109.942ms | 5% |
| gc_barabasi | iouring | btrfs | none | flat | single | 50000 | 4 | 3 | 17.344s | 18.997s | 1.480s | 9% |
| gc_barabasi | iouring | btrfs | none | sharded | multi | 2000 | 4 | 3 | 327.512ms | 330.622ms | 2.919ms | 1% |
| gc_barabasi | iouring | btrfs | none | sharded | multi | 10000 | 4 | 3 | 2.066s | 2.091s | 30.620ms | 1% |
| gc_barabasi | iouring | btrfs | none | sharded | multi | 50000 | 4 | 3 | 15.878s | 16.285s | 680.768ms | 4% |
| gc_barabasi | iouring | btrfs | none | sharded | single | 2000 | 4 | 3 | 331.578ms | 332.824ms | 1.464ms | 0% |
| gc_barabasi | iouring | btrfs | none | sharded | single | 10000 | 4 | 3 | 2.195s | 2.322s | 113.714ms | 5% |
| gc_barabasi | iouring | btrfs | none | sharded | single | 50000 | 4 | 3 | 17.415s | 18.566s | 1.028s | 6% |
| gc_barabasi | iouring | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 317.737ms | 322.524ms | 4.669ms | 1% |
| gc_barabasi | iouring | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 2.193s | 2.289s | 88.627ms | 4% |
| gc_barabasi | iouring | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 16.259s | 16.971s | 644.851ms | 4% |
| gc_barabasi | iouring | btrfs | nvme | flat | single | 2000 | 4 | 3 | 326.708ms | 328.184ms | 1.417ms | 0% |
| gc_barabasi | iouring | btrfs | nvme | flat | single | 10000 | 4 | 3 | 2.331s | 2.553s | 204.715ms | 9% |
| gc_barabasi | iouring | btrfs | nvme | flat | single | 50000 | 4 | 3 | 18.801s | 20.346s | 1.526s | 8% |
| gc_barabasi | iouring | btrfs | nvme | sharded | multi | 2000 | 4 | 3 | 327.570ms | 341.388ms | 12.416ms | 4% |
| gc_barabasi | iouring | btrfs | nvme | sharded | multi | 10000 | 4 | 3 | 2.138s | 2.267s | 121.659ms | 6% |
| gc_barabasi | iouring | btrfs | nvme | sharded | multi | 50000 | 4 | 3 | 16.425s | 17.136s | 637.236ms | 4% |
| gc_barabasi | iouring | btrfs | nvme | sharded | single | 2000 | 4 | 3 | 338.080ms | 348.538ms | 9.950ms | 3% |
| gc_barabasi | iouring | btrfs | nvme | sharded | single | 10000 | 4 | 3 | 2.411s | 2.560s | 140.991ms | 6% |
| gc_barabasi | iouring | btrfs | nvme | sharded | single | 50000 | 4 | 3 | 18.916s | 19.084s | 270.688ms | 1% |
| gc_barabasi | iouring | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 124.738ms | 130.297ms | 5.341ms | 4% |
| gc_barabasi | iouring | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 2.073s | 2.080s | 12.612ms | 1% |
| gc_barabasi | iouring | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 71.545s | 72.040s | 445.871ms | 1% |
| gc_barabasi | iouring | ext4 | gp3 | flat | single | 2000 | 4 | 3 | 127.257ms | 133.438ms | 6.324ms | 5% |
| gc_barabasi | iouring | ext4 | gp3 | flat | single | 10000 | 4 | 3 | 2.067s | 2.081s | 17.777ms | 1% |
| gc_barabasi | iouring | ext4 | gp3 | flat | single | 50000 | 4 | 3 | 65.069s | 65.861s | 1.040s | 2% |
| gc_barabasi | iouring | ext4 | gp3 | sharded | multi | 2000 | 4 | 3 | 129.622ms | 132.124ms | 2.815ms | 2% |
| gc_barabasi | iouring | ext4 | gp3 | sharded | multi | 10000 | 4 | 3 | 2.074s | 2.087s | 11.772ms | 1% |
| gc_barabasi | iouring | ext4 | gp3 | sharded | multi | 50000 | 4 | 3 | 68.476s | 70.269s | 1.622s | 2% |
| gc_barabasi | iouring | ext4 | gp3 | sharded | single | 2000 | 4 | 3 | 131.298ms | 135.803ms | 4.038ms | 3% |
| gc_barabasi | iouring | ext4 | gp3 | sharded | single | 10000 | 4 | 3 | 2.101s | 2.107s | 9.631ms | 0% |
| gc_barabasi | iouring | ext4 | gp3 | sharded | single | 50000 | 4 | 3 | 72.690s | 73.849s | 1.467s | 2% |
| gc_barabasi | iouring | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 130.023ms | 132.857ms | 3.914ms | 3% |
| gc_barabasi | iouring | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 1.157s | 1.163s | 6.725ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 19.600s | 19.880s | 250.004ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | flat | single | 2000 | 4 | 3 | 123.952ms | 125.690ms | 1.654ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | flat | single | 10000 | 4 | 3 | 1.158s | 1.169s | 10.609ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | flat | single | 50000 | 4 | 3 | 19.318s | 19.518s | 216.275ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | sharded | multi | 2000 | 4 | 3 | 133.325ms | 138.557ms | 8.176ms | 6% |
| gc_barabasi | iouring | ext4 | io2 | sharded | multi | 10000 | 4 | 3 | 1.170s | 1.175s | 6.598ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | sharded | multi | 50000 | 4 | 3 | 19.628s | 19.759s | 143.321ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | sharded | single | 2000 | 4 | 3 | 128.529ms | 131.485ms | 2.709ms | 2% |
| gc_barabasi | iouring | ext4 | io2 | sharded | single | 10000 | 4 | 3 | 1.216s | 1.227s | 11.683ms | 1% |
| gc_barabasi | iouring | ext4 | io2 | sharded | single | 50000 | 4 | 3 | 21.186s | 21.619s | 423.043ms | 2% |
| gc_barabasi | iouring | ext4 | none | flat | multi | 2000 | 4 | 3 | 126.561ms | 129.913ms | 3.012ms | 2% |
| gc_barabasi | iouring | ext4 | none | flat | multi | 10000 | 4 | 3 | 896.393ms | 924.405ms | 25.022ms | 3% |
| gc_barabasi | iouring | ext4 | none | flat | multi | 50000 | 4 | 3 | 9.604s | 9.718s | 111.937ms | 1% |
| gc_barabasi | iouring | ext4 | none | flat | single | 2000 | 4 | 3 | 127.724ms | 133.499ms | 6.010ms | 5% |
| gc_barabasi | iouring | ext4 | none | flat | single | 10000 | 4 | 3 | 882.252ms | 888.297ms | 8.770ms | 1% |
| gc_barabasi | iouring | ext4 | none | flat | single | 50000 | 4 | 3 | 9.321s | 9.434s | 109.052ms | 1% |
| gc_barabasi | iouring | ext4 | none | sharded | multi | 2000 | 4 | 3 | 132.767ms | 141.765ms | 8.077ms | 6% |
| gc_barabasi | iouring | ext4 | none | sharded | multi | 10000 | 4 | 3 | 892.903ms | 904.377ms | 15.830ms | 2% |
| gc_barabasi | iouring | ext4 | none | sharded | multi | 50000 | 4 | 3 | 9.688s | 9.734s | 41.831ms | 0% |
| gc_barabasi | iouring | ext4 | none | sharded | single | 2000 | 4 | 3 | 133.523ms | 142.626ms | 8.126ms | 6% |
| gc_barabasi | iouring | ext4 | none | sharded | single | 10000 | 4 | 3 | 903.762ms | 918.772ms | 16.269ms | 2% |
| gc_barabasi | iouring | ext4 | none | sharded | single | 50000 | 4 | 3 | 9.648s | 9.692s | 65.491ms | 1% |
| gc_barabasi | iouring | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 131.714ms | 135.709ms | 3.579ms | 3% |
| gc_barabasi | iouring | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 983.029ms | 1.006s | 27.030ms | 3% |
| gc_barabasi | iouring | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 10.481s | 10.542s | 96.860ms | 1% |
| gc_barabasi | iouring | ext4 | nvme | flat | single | 2000 | 4 | 3 | 132.572ms | 134.449ms | 2.565ms | 2% |
| gc_barabasi | iouring | ext4 | nvme | flat | single | 10000 | 4 | 3 | 968.474ms | 988.934ms | 18.266ms | 2% |
| gc_barabasi | iouring | ext4 | nvme | flat | single | 50000 | 4 | 3 | 10.114s | 10.128s | 13.349ms | 0% |
| gc_barabasi | iouring | ext4 | nvme | sharded | multi | 2000 | 4 | 3 | 132.549ms | 134.562ms | 2.107ms | 2% |
| gc_barabasi | iouring | ext4 | nvme | sharded | multi | 10000 | 4 | 3 | 959.427ms | 960.340ms | 903.690µs | 0% |
| gc_barabasi | iouring | ext4 | nvme | sharded | multi | 50000 | 4 | 3 | 10.505s | 10.609s | 132.301ms | 1% |
| gc_barabasi | iouring | ext4 | nvme | sharded | single | 2000 | 4 | 3 | 130.618ms | 135.572ms | 4.909ms | 4% |
| gc_barabasi | iouring | ext4 | nvme | sharded | single | 10000 | 4 | 3 | 990.750ms | 995.589ms | 7.578ms | 1% |
| gc_barabasi | iouring | ext4 | nvme | sharded | single | 50000 | 4 | 3 | 10.561s | 10.684s | 110.628ms | 1% |
| gc_barabasi | iouring | tmpfs | none | flat | multi | 2000 | 4 | 3 | 100.515ms | 102.656ms | 3.320ms | 3% |
| gc_barabasi | iouring | tmpfs | none | flat | multi | 10000 | 4 | 3 | 590.807ms | 615.325ms | 23.354ms | 4% |
| gc_barabasi | iouring | tmpfs | none | flat | multi | 50000 | 4 | 3 | 3.687s | 3.716s | 40.733ms | 1% |
| gc_barabasi | iouring | tmpfs | none | flat | single | 2000 | 4 | 3 | 100.505ms | 101.521ms | 1.198ms | 1% |
| gc_barabasi | iouring | tmpfs | none | flat | single | 10000 | 4 | 3 | 585.162ms | 589.666ms | 4.612ms | 1% |
| gc_barabasi | iouring | tmpfs | none | flat | single | 50000 | 4 | 3 | 3.629s | 3.664s | 32.146ms | 1% |
| gc_barabasi | iouring | tmpfs | none | sharded | multi | 2000 | 4 | 3 | 102.970ms | 108.715ms | 5.172ms | 5% |
| gc_barabasi | iouring | tmpfs | none | sharded | multi | 10000 | 4 | 3 | 603.646ms | 637.734ms | 30.597ms | 5% |
| gc_barabasi | iouring | tmpfs | none | sharded | multi | 50000 | 4 | 3 | 3.722s | 3.749s | 40.866ms | 1% |
| gc_barabasi | iouring | tmpfs | none | sharded | single | 2000 | 4 | 3 | 104.702ms | 111.019ms | 5.749ms | 5% |
| gc_barabasi | iouring | tmpfs | none | sharded | single | 10000 | 4 | 3 | 594.396ms | 605.502ms | 10.559ms | 2% |
| gc_barabasi | iouring | tmpfs | none | sharded | single | 50000 | 4 | 3 | 3.659s | 3.671s | 16.879ms | 0% |
| gc_barabasi | iouring | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 170.365ms | 174.882ms | 4.050ms | 2% |
| gc_barabasi | iouring | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 2.926s | 3.117s | 200.680ms | 7% |
| gc_barabasi | iouring | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 34.985s | 35.103s | 111.146ms | 0% |
| gc_barabasi | iouring | xfs | gp3 | flat | single | 2000 | 4 | 3 | 169.458ms | 178.007ms | 8.419ms | 5% |
| gc_barabasi | iouring | xfs | gp3 | flat | single | 10000 | 4 | 3 | 2.963s | 2.981s | 29.817ms | 1% |
| gc_barabasi | iouring | xfs | gp3 | flat | single | 50000 | 4 | 3 | 35.125s | 35.910s | 746.487ms | 2% |
| gc_barabasi | iouring | xfs | gp3 | sharded | multi | 2000 | 4 | 3 | 195.236ms | 261.444ms | 59.110ms | 30%* |
| gc_barabasi | iouring | xfs | gp3 | sharded | multi | 10000 | 4 | 3 | 3.050s | 3.068s | 20.158ms | 1% |
| gc_barabasi | iouring | xfs | gp3 | sharded | multi | 50000 | 4 | 3 | 40.669s | 42.997s | 2.111s | 5% |
| gc_barabasi | iouring | xfs | gp3 | sharded | single | 2000 | 4 | 3 | 274.963ms | 366.824ms | 105.355ms | 38%* |
| gc_barabasi | iouring | xfs | gp3 | sharded | single | 10000 | 4 | 3 | 4.740s | 5.991s | 1.119s | 24%* |
| gc_barabasi | iouring | xfs | gp3 | sharded | single | 50000 | 4 | 3 | 53.532s | 55.055s | 1.664s | 3% |
| gc_barabasi | iouring | xfs | io2 | flat | multi | 2000 | 4 | 3 | 167.314ms | 173.823ms | 6.395ms | 4% |
| gc_barabasi | iouring | xfs | io2 | flat | multi | 10000 | 4 | 3 | 2.087s | 2.324s | 222.363ms | 11% |
| gc_barabasi | iouring | xfs | io2 | flat | multi | 50000 | 4 | 3 | 24.897s | 25.788s | 871.239ms | 3% |
| gc_barabasi | iouring | xfs | io2 | flat | single | 2000 | 4 | 3 | 163.287ms | 167.467ms | 4.090ms | 3% |
| gc_barabasi | iouring | xfs | io2 | flat | single | 10000 | 4 | 3 | 2.037s | 2.059s | 23.501ms | 1% |
| gc_barabasi | iouring | xfs | io2 | flat | single | 50000 | 4 | 3 | 24.891s | 25.129s | 269.218ms | 1% |
| gc_barabasi | iouring | xfs | io2 | sharded | multi | 2000 | 4 | 3 | 194.987ms | 254.078ms | 52.773ms | 27%* |
| gc_barabasi | iouring | xfs | io2 | sharded | multi | 10000 | 4 | 3 | 2.164s | 2.230s | 63.650ms | 3% |
| gc_barabasi | iouring | xfs | io2 | sharded | multi | 50000 | 4 | 3 | 29.251s | 29.856s | 627.864ms | 2% |
| gc_barabasi | iouring | xfs | io2 | sharded | single | 2000 | 4 | 3 | 167.724ms | 173.920ms | 5.639ms | 3% |
| gc_barabasi | iouring | xfs | io2 | sharded | single | 10000 | 4 | 3 | 3.134s | 3.354s | 205.675ms | 7% |
| gc_barabasi | iouring | xfs | io2 | sharded | single | 50000 | 4 | 3 | 44.338s | 46.472s | 1.905s | 4% |
| gc_barabasi | iouring | xfs | none | flat | multi | 2000 | 4 | 3 | 197.492ms | 271.538ms | 66.099ms | 33%* |
| gc_barabasi | iouring | xfs | none | flat | multi | 10000 | 4 | 3 | 1.814s | 1.830s | 15.031ms | 1% |
| gc_barabasi | iouring | xfs | none | flat | multi | 50000 | 4 | 3 | 21.411s | 22.940s | 1.492s | 7% |
| gc_barabasi | iouring | xfs | none | flat | single | 2000 | 4 | 3 | 172.459ms | 178.391ms | 7.790ms | 5% |
| gc_barabasi | iouring | xfs | none | flat | single | 10000 | 4 | 3 | 1.808s | 1.914s | 116.078ms | 6% |
| gc_barabasi | iouring | xfs | none | flat | single | 50000 | 4 | 3 | 21.049s | 21.540s | 702.056ms | 3% |
| gc_barabasi | iouring | xfs | none | sharded | multi | 2000 | 4 | 3 | 169.493ms | 174.436ms | 5.762ms | 3% |
| gc_barabasi | iouring | xfs | none | sharded | multi | 10000 | 4 | 3 | 1.937s | 1.959s | 22.981ms | 1% |
| gc_barabasi | iouring | xfs | none | sharded | multi | 50000 | 4 | 3 | 25.786s | 26.776s | 896.873ms | 3% |
| gc_barabasi | iouring | xfs | none | sharded | single | 2000 | 4 | 3 | 243.574ms | 288.361ms | 65.095ms | 27%* |
| gc_barabasi | iouring | xfs | none | sharded | single | 10000 | 4 | 3 | 2.883s | 2.945s | 58.155ms | 2% |
| gc_barabasi | iouring | xfs | none | sharded | single | 50000 | 4 | 3 | 39.738s | 39.892s | 143.867ms | 0% |
| gc_barabasi | iouring | xfs | nvme | flat | multi | 2000 | 4 | 3 | 162.580ms | 164.681ms | 1.945ms | 1% |
| gc_barabasi | iouring | xfs | nvme | flat | multi | 10000 | 4 | 3 | 1.953s | 2.008s | 94.975ms | 5% |
| gc_barabasi | iouring | xfs | nvme | flat | multi | 50000 | 4 | 3 | 22.535s | 23.462s | 875.689ms | 4% |
| gc_barabasi | iouring | xfs | nvme | flat | single | 2000 | 4 | 3 | 160.556ms | 164.089ms | 3.154ms | 2% |
| gc_barabasi | iouring | xfs | nvme | flat | single | 10000 | 4 | 3 | 1.861s | 2.041s | 227.176ms | 12% |
| gc_barabasi | iouring | xfs | nvme | flat | single | 50000 | 4 | 3 | 21.127s | 21.776s | 605.195ms | 3% |
| gc_barabasi | iouring | xfs | nvme | sharded | multi | 2000 | 4 | 3 | 247.106ms | 313.314ms | 79.012ms | 32%* |
| gc_barabasi | iouring | xfs | nvme | sharded | multi | 10000 | 4 | 3 | 2.033s | 2.070s | 35.068ms | 2% |
| gc_barabasi | iouring | xfs | nvme | sharded | multi | 50000 | 4 | 3 | 26.399s | 27.088s | 676.945ms | 3% |
| gc_barabasi | iouring | xfs | nvme | sharded | single | 2000 | 4 | 3 | 206.853ms | 285.627ms | 70.346ms | 34%* |
| gc_barabasi | iouring | xfs | nvme | sharded | single | 10000 | 4 | 3 | 2.977s | 3.303s | 319.956ms | 11% |
| gc_barabasi | iouring | xfs | nvme | sharded | single | 50000 | 4 | 3 | 39.648s | 41.960s | 2.084s | 5% |
| gc_barabasi | iouring | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 227.014ms | 246.786ms | 17.738ms | 8% |
| gc_barabasi | iouring | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 1.689s | 1.712s | 32.910ms | 2% |
| gc_barabasi | iouring | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 16.852s | 17.279s | 405.322ms | 2% |
| gc_barabasi | iouring | zfs | gp3 | flat | single | 2000 | 4 | 3 | 225.578ms | 232.987ms | 6.905ms | 3% |
| gc_barabasi | iouring | zfs | gp3 | flat | single | 10000 | 4 | 3 | 1.718s | 1.750s | 33.655ms | 2% |
| gc_barabasi | iouring | zfs | gp3 | flat | single | 50000 | 4 | 3 | 16.843s | 16.919s | 120.245ms | 1% |
| gc_barabasi | iouring | zfs | gp3 | sharded | multi | 2000 | 4 | 3 | 233.006ms | 242.391ms | 13.962ms | 6% |
| gc_barabasi | iouring | zfs | gp3 | sharded | multi | 10000 | 4 | 3 | 1.705s | 1.731s | 23.823ms | 1% |
| gc_barabasi | iouring | zfs | gp3 | sharded | multi | 50000 | 4 | 3 | 17.439s | 17.515s | 117.697ms | 1% |
| gc_barabasi | iouring | zfs | gp3 | sharded | single | 2000 | 4 | 3 | 234.636ms | 242.153ms | 8.554ms | 4% |
| gc_barabasi | iouring | zfs | gp3 | sharded | single | 10000 | 4 | 3 | 1.742s | 1.745s | 3.499ms | 0% |
| gc_barabasi | iouring | zfs | gp3 | sharded | single | 50000 | 4 | 3 | 20.509s | 26.640s | 5.474s | 27%* |
| gc_barabasi | iouring | zfs | io2 | flat | multi | 2000 | 4 | 3 | 225.438ms | 234.191ms | 9.498ms | 4% |
| gc_barabasi | iouring | zfs | io2 | flat | multi | 10000 | 4 | 3 | 1.685s | 1.701s | 17.447ms | 1% |
| gc_barabasi | iouring | zfs | io2 | flat | multi | 50000 | 4 | 3 | 16.531s | 16.963s | 478.195ms | 3% |
| gc_barabasi | iouring | zfs | io2 | flat | single | 2000 | 4 | 3 | 224.409ms | 231.437ms | 7.518ms | 3% |
| gc_barabasi | iouring | zfs | io2 | flat | single | 10000 | 4 | 3 | 1.734s | 1.773s | 38.503ms | 2% |
| gc_barabasi | iouring | zfs | io2 | flat | single | 50000 | 4 | 3 | 17.492s | 17.588s | 106.543ms | 1% |
| gc_barabasi | iouring | zfs | io2 | sharded | multi | 2000 | 4 | 3 | 227.927ms | 237.246ms | 9.524ms | 4% |
| gc_barabasi | iouring | zfs | io2 | sharded | multi | 10000 | 4 | 3 | 1.701s | 1.751s | 53.023ms | 3% |
| gc_barabasi | iouring | zfs | io2 | sharded | multi | 50000 | 4 | 3 | 18.733s | 21.143s | 2.158s | 12% |
| gc_barabasi | iouring | zfs | io2 | sharded | single | 2000 | 4 | 3 | 231.752ms | 237.841ms | 7.215ms | 3% |
| gc_barabasi | iouring | zfs | io2 | sharded | single | 10000 | 4 | 3 | 1.750s | 1.773s | 20.571ms | 1% |
| gc_barabasi | iouring | zfs | io2 | sharded | single | 50000 | 4 | 3 | 17.337s | 17.961s | 878.362ms | 5% |
| gc_barabasi | iouring | zfs | none | flat | multi | 2000 | 4 | 3 | 221.201ms | 226.152ms | 4.467ms | 2% |
| gc_barabasi | iouring | zfs | none | flat | multi | 10000 | 4 | 3 | 1.699s | 1.745s | 43.878ms | 3% |
| gc_barabasi | iouring | zfs | none | flat | multi | 50000 | 4 | 3 | 17.412s | 17.566s | 229.812ms | 1% |
| gc_barabasi | iouring | zfs | none | flat | single | 2000 | 4 | 3 | 230.329ms | 242.719ms | 15.122ms | 7% |
| gc_barabasi | iouring | zfs | none | flat | single | 10000 | 4 | 3 | 1.729s | 1.755s | 31.287ms | 2% |
| gc_barabasi | iouring | zfs | none | flat | single | 50000 | 4 | 3 | 18.051s | 18.915s | 847.956ms | 5% |
| gc_barabasi | iouring | zfs | none | sharded | multi | 2000 | 4 | 3 | 229.927ms | 235.219ms | 5.159ms | 2% |
| gc_barabasi | iouring | zfs | none | sharded | multi | 10000 | 4 | 3 | 1.688s | 1.704s | 19.011ms | 1% |
| gc_barabasi | iouring | zfs | none | sharded | multi | 50000 | 4 | 3 | 17.299s | 18.284s | 1.067s | 6% |
| gc_barabasi | iouring | zfs | none | sharded | single | 2000 | 4 | 3 | 228.056ms | 235.561ms | 6.738ms | 3% |
| gc_barabasi | iouring | zfs | none | sharded | single | 10000 | 4 | 3 | 1.731s | 1.784s | 47.236ms | 3% |
| gc_barabasi | iouring | zfs | none | sharded | single | 50000 | 4 | 3 | 18.023s | 19.068s | 1.129s | 6% |
| gc_barabasi | iouring | zfs | nvme | flat | multi | 2000 | 4 | 3 | 216.743ms | 219.972ms | 5.091ms | 2% |
| gc_barabasi | iouring | zfs | nvme | flat | multi | 10000 | 4 | 3 | 1.692s | 1.709s | 16.411ms | 1% |
| gc_barabasi | iouring | zfs | nvme | flat | multi | 50000 | 4 | 3 | 17.110s | 17.324s | 258.352ms | 2% |
| gc_barabasi | iouring | zfs | nvme | flat | single | 2000 | 4 | 3 | 228.969ms | 234.817ms | 7.453ms | 3% |
| gc_barabasi | iouring | zfs | nvme | flat | single | 10000 | 4 | 3 | 1.733s | 1.759s | 27.014ms | 2% |
| gc_barabasi | iouring | zfs | nvme | flat | single | 50000 | 4 | 3 | 16.821s | 17.723s | 810.398ms | 5% |
| gc_barabasi | iouring | zfs | nvme | sharded | multi | 2000 | 4 | 3 | 228.232ms | 232.230ms | 4.626ms | 2% |
| gc_barabasi | iouring | zfs | nvme | sharded | multi | 10000 | 4 | 3 | 1.687s | 1.724s | 44.213ms | 3% |
| gc_barabasi | iouring | zfs | nvme | sharded | multi | 50000 | 4 | 3 | 16.997s | 17.549s | 493.140ms | 3% |
| gc_barabasi | iouring | zfs | nvme | sharded | single | 2000 | 4 | 3 | 229.814ms | 236.485ms | 6.258ms | 3% |
| gc_barabasi | iouring | zfs | nvme | sharded | single | 10000 | 4 | 3 | 1.735s | 1.745s | 10.897ms | 1% |
| gc_barabasi | iouring | zfs | nvme | sharded | single | 50000 | 4 | 3 | 17.292s | 17.839s | 494.544ms | 3% |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 281.770ms | 284.394ms | 3.111ms | 1% |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 4.511s | 7.350s | 2.538s | 56%* |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 29.914s | 34.486s | 4.081s | 14% |
| gc_barabasi | syscall | btrfs | gp3 | flat | single | 2000 | 4 | 3 | 287.548ms | 292.759ms | 6.989ms | 2% |
| gc_barabasi | syscall | btrfs | gp3 | flat | single | 10000 | 4 | 3 | 4.769s | 7.893s | 2.789s | 58%* |
| gc_barabasi | syscall | btrfs | gp3 | flat | single | 50000 | 4 | 3 | 35.901s | 49.719s | 12.344s | 34%* |
| gc_barabasi | syscall | btrfs | gp3 | sharded | multi | 2000 | 4 | 3 | 295.943ms | 304.827ms | 8.669ms | 3% |
| gc_barabasi | syscall | btrfs | gp3 | sharded | multi | 10000 | 4 | 3 | 4.518s | 7.372s | 2.548s | 56%* |
| gc_barabasi | syscall | btrfs | gp3 | sharded | multi | 50000 | 4 | 3 | 30.480s | 34.550s | 3.857s | 13% |
| gc_barabasi | syscall | btrfs | gp3 | sharded | single | 2000 | 4 | 3 | 294.931ms | 296.715ms | 2.365ms | 1% |
| gc_barabasi | syscall | btrfs | gp3 | sharded | single | 10000 | 4 | 3 | 4.919s | 8.121s | 2.858s | 58%* |
| gc_barabasi | syscall | btrfs | gp3 | sharded | single | 50000 | 4 | 3 | 36.264s | 40.410s | 6.010s | 17% |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 278.810ms | 287.753ms | 8.541ms | 3% |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 2.379s | 2.865s | 436.317ms | 18% |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 17.233s | 17.562s | 412.447ms | 2% |
| gc_barabasi | syscall | btrfs | io2 | flat | single | 2000 | 4 | 3 | 289.777ms | 296.373ms | 6.027ms | 2% |
| gc_barabasi | syscall | btrfs | io2 | flat | single | 10000 | 4 | 3 | 2.494s | 3.067s | 513.193ms | 21%* |
| gc_barabasi | syscall | btrfs | io2 | flat | single | 50000 | 4 | 3 | 21.520s | 23.494s | 1.888s | 9% |
| gc_barabasi | syscall | btrfs | io2 | sharded | multi | 2000 | 4 | 3 | 291.508ms | 293.322ms | 1.803ms | 1% |
| gc_barabasi | syscall | btrfs | io2 | sharded | multi | 10000 | 4 | 3 | 2.364s | 2.808s | 398.266ms | 17% |
| gc_barabasi | syscall | btrfs | io2 | sharded | multi | 50000 | 4 | 3 | 17.261s | 17.408s | 141.909ms | 1% |
| gc_barabasi | syscall | btrfs | io2 | sharded | single | 2000 | 4 | 3 | 297.669ms | 303.071ms | 4.866ms | 2% |
| gc_barabasi | syscall | btrfs | io2 | sharded | single | 10000 | 4 | 3 | 2.599s | 3.126s | 471.724ms | 18% |
| gc_barabasi | syscall | btrfs | io2 | sharded | single | 50000 | 4 | 3 | 20.816s | 23.130s | 2.237s | 11% |
| gc_barabasi | syscall | btrfs | none | flat | multi | 2000 | 4 | 3 | 285.855ms | 290.404ms | 4.297ms | 2% |
| gc_barabasi | syscall | btrfs | none | flat | multi | 10000 | 4 | 3 | 1.822s | 1.932s | 125.891ms | 7% |
| gc_barabasi | syscall | btrfs | none | flat | multi | 50000 | 4 | 3 | 14.237s | 14.575s | 444.116ms | 3% |
| gc_barabasi | syscall | btrfs | none | flat | single | 2000 | 4 | 3 | 289.826ms | 293.160ms | 3.121ms | 1% |
| gc_barabasi | syscall | btrfs | none | flat | single | 10000 | 4 | 3 | 1.986s | 2.120s | 164.703ms | 8% |
| gc_barabasi | syscall | btrfs | none | flat | single | 50000 | 4 | 3 | 17.098s | 17.501s | 603.004ms | 4% |
| gc_barabasi | syscall | btrfs | none | sharded | multi | 2000 | 4 | 3 | 296.782ms | 303.194ms | 8.839ms | 3% |
| gc_barabasi | syscall | btrfs | none | sharded | multi | 10000 | 4 | 3 | 1.845s | 1.900s | 84.587ms | 5% |
| gc_barabasi | syscall | btrfs | none | sharded | multi | 50000 | 4 | 3 | 14.364s | 14.452s | 79.431ms | 1% |
| gc_barabasi | syscall | btrfs | none | sharded | single | 2000 | 4 | 3 | 290.571ms | 294.292ms | 3.386ms | 1% |
| gc_barabasi | syscall | btrfs | none | sharded | single | 10000 | 4 | 3 | 2.036s | 2.159s | 140.022ms | 7% |
| gc_barabasi | syscall | btrfs | none | sharded | single | 50000 | 4 | 3 | 15.836s | 17.043s | 1.139s | 7% |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 282.177ms | 285.086ms | 2.597ms | 1% |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 1.924s | 2.071s | 144.715ms | 8% |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 15.888s | 15.919s | 49.898ms | 0% |
| gc_barabasi | syscall | btrfs | nvme | flat | single | 2000 | 4 | 3 | 290.233ms | 297.863ms | 8.776ms | 3% |
| gc_barabasi | syscall | btrfs | nvme | flat | single | 10000 | 4 | 3 | 2.196s | 2.289s | 153.208ms | 7% |
| gc_barabasi | syscall | btrfs | nvme | flat | single | 50000 | 4 | 3 | 17.633s | 18.910s | 1.141s | 6% |
| gc_barabasi | syscall | btrfs | nvme | sharded | multi | 2000 | 4 | 3 | 288.747ms | 302.521ms | 13.807ms | 5% |
| gc_barabasi | syscall | btrfs | nvme | sharded | multi | 10000 | 4 | 3 | 2.071s | 2.129s | 69.526ms | 3% |
| gc_barabasi | syscall | btrfs | nvme | sharded | multi | 50000 | 4 | 3 | 15.290s | 15.743s | 590.397ms | 4% |
| gc_barabasi | syscall | btrfs | nvme | sharded | single | 2000 | 4 | 3 | 299.646ms | 307.331ms | 12.313ms | 4% |
| gc_barabasi | syscall | btrfs | nvme | sharded | single | 10000 | 4 | 3 | 2.130s | 2.398s | 242.213ms | 11% |
| gc_barabasi | syscall | btrfs | nvme | sharded | single | 50000 | 4 | 3 | 17.028s | 18.199s | 1.065s | 6% |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 113.135ms | 117.945ms | 6.078ms | 5% |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 1.991s | 2.004s | 14.065ms | 1% |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 68.673s | 69.841s | 1.112s | 2% |
| gc_barabasi | syscall | ext4 | gp3 | flat | single | 2000 | 4 | 3 | 112.458ms | 115.421ms | 2.912ms | 3% |
| gc_barabasi | syscall | ext4 | gp3 | flat | single | 10000 | 4 | 3 | 1.998s | 2.004s | 6.201ms | 0% |
| gc_barabasi | syscall | ext4 | gp3 | flat | single | 50000 | 4 | 3 | 66.140s | 66.393s | 310.321ms | 0% |
| gc_barabasi | syscall | ext4 | gp3 | sharded | multi | 2000 | 4 | 3 | 118.664ms | 130.910ms | 10.941ms | 9% |
| gc_barabasi | syscall | ext4 | gp3 | sharded | multi | 10000 | 4 | 3 | 1.996s | 2.001s | 5.553ms | 0% |
| gc_barabasi | syscall | ext4 | gp3 | sharded | multi | 50000 | 4 | 3 | 68.399s | 71.096s | 2.497s | 4% |
| gc_barabasi | syscall | ext4 | gp3 | sharded | single | 2000 | 4 | 3 | 113.782ms | 119.702ms | 5.732ms | 5% |
| gc_barabasi | syscall | ext4 | gp3 | sharded | single | 10000 | 4 | 3 | 2.022s | 2.025s | 3.721ms | 0% |
| gc_barabasi | syscall | ext4 | gp3 | sharded | single | 50000 | 4 | 3 | 71.280s | 72.876s | 1.431s | 2% |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 112.500ms | 115.206ms | 2.610ms | 2% |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 1.098s | 1.118s | 19.076ms | 2% |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 18.989s | 19.291s | 269.601ms | 1% |
| gc_barabasi | syscall | ext4 | io2 | flat | single | 2000 | 4 | 3 | 117.116ms | 121.427ms | 4.513ms | 4% |
| gc_barabasi | syscall | ext4 | io2 | flat | single | 10000 | 4 | 3 | 1.102s | 1.113s | 11.973ms | 1% |
| gc_barabasi | syscall | ext4 | io2 | flat | single | 50000 | 4 | 3 | 18.978s | 19.285s | 329.965ms | 2% |
| gc_barabasi | syscall | ext4 | io2 | sharded | multi | 2000 | 4 | 3 | 116.574ms | 123.701ms | 6.806ms | 6% |
| gc_barabasi | syscall | ext4 | io2 | sharded | multi | 10000 | 4 | 3 | 1.093s | 1.098s | 7.352ms | 1% |
| gc_barabasi | syscall | ext4 | io2 | sharded | multi | 50000 | 4 | 3 | 18.752s | 19.146s | 424.981ms | 2% |
| gc_barabasi | syscall | ext4 | io2 | sharded | single | 2000 | 4 | 3 | 116.999ms | 123.789ms | 6.550ms | 6% |
| gc_barabasi | syscall | ext4 | io2 | sharded | single | 10000 | 4 | 3 | 1.133s | 1.146s | 11.358ms | 1% |
| gc_barabasi | syscall | ext4 | io2 | sharded | single | 50000 | 4 | 3 | 20.062s | 20.137s | 84.460ms | 0% |
| gc_barabasi | syscall | ext4 | none | flat | multi | 2000 | 4 | 3 | 115.355ms | 120.927ms | 5.345ms | 5% |
| gc_barabasi | syscall | ext4 | none | flat | multi | 10000 | 4 | 3 | 819.141ms | 831.006ms | 11.390ms | 1% |
| gc_barabasi | syscall | ext4 | none | flat | multi | 50000 | 4 | 3 | 9.151s | 9.275s | 110.567ms | 1% |
| gc_barabasi | syscall | ext4 | none | flat | single | 2000 | 4 | 3 | 113.519ms | 117.436ms | 3.497ms | 3% |
| gc_barabasi | syscall | ext4 | none | flat | single | 10000 | 4 | 3 | 822.417ms | 829.885ms | 12.239ms | 1% |
| gc_barabasi | syscall | ext4 | none | flat | single | 50000 | 4 | 3 | 8.866s | 8.962s | 89.884ms | 1% |
| gc_barabasi | syscall | ext4 | none | sharded | multi | 2000 | 4 | 3 | 117.161ms | 124.711ms | 6.753ms | 6% |
| gc_barabasi | syscall | ext4 | none | sharded | multi | 10000 | 4 | 3 | 814.271ms | 820.760ms | 6.466ms | 1% |
| gc_barabasi | syscall | ext4 | none | sharded | multi | 50000 | 4 | 3 | 9.035s | 9.247s | 198.982ms | 2% |
| gc_barabasi | syscall | ext4 | none | sharded | single | 2000 | 4 | 3 | 115.835ms | 121.081ms | 4.909ms | 4% |
| gc_barabasi | syscall | ext4 | none | sharded | single | 10000 | 4 | 3 | 835.113ms | 853.817ms | 16.698ms | 2% |
| gc_barabasi | syscall | ext4 | none | sharded | single | 50000 | 4 | 3 | 9.023s | 9.164s | 188.021ms | 2% |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 112.615ms | 118.889ms | 5.601ms | 5% |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 907.912ms | 914.196ms | 7.599ms | 1% |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 10.121s | 10.224s | 118.362ms | 1% |
| gc_barabasi | syscall | ext4 | nvme | flat | single | 2000 | 4 | 3 | 113.011ms | 119.120ms | 5.463ms | 5% |
| gc_barabasi | syscall | ext4 | nvme | flat | single | 10000 | 4 | 3 | 906.738ms | 922.248ms | 14.155ms | 2% |
| gc_barabasi | syscall | ext4 | nvme | flat | single | 50000 | 4 | 3 | 9.724s | 9.777s | 47.620ms | 0% |
| gc_barabasi | syscall | ext4 | nvme | sharded | multi | 2000 | 4 | 3 | 114.404ms | 116.974ms | 2.397ms | 2% |
| gc_barabasi | syscall | ext4 | nvme | sharded | multi | 10000 | 4 | 3 | 909.261ms | 911.485ms | 3.122ms | 0% |
| gc_barabasi | syscall | ext4 | nvme | sharded | multi | 50000 | 4 | 3 | 9.969s | 10.031s | 99.443ms | 1% |
| gc_barabasi | syscall | ext4 | nvme | sharded | single | 2000 | 4 | 3 | 117.068ms | 119.187ms | 2.161ms | 2% |
| gc_barabasi | syscall | ext4 | nvme | sharded | single | 10000 | 4 | 3 | 924.549ms | 938.168ms | 12.484ms | 1% |
| gc_barabasi | syscall | ext4 | nvme | sharded | single | 50000 | 4 | 3 | 10.000s | 10.068s | 62.737ms | 1% |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 2000 | 4 | 3 | 85.312ms | 90.168ms | 5.118ms | 6% |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 10000 | 4 | 3 | 518.830ms | 538.312ms | 17.760ms | 3% |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 50000 | 4 | 3 | 3.394s | 3.438s | 55.787ms | 2% |
| gc_barabasi | syscall | tmpfs | none | flat | single | 2000 | 4 | 3 | 87.099ms | 88.742ms | 2.265ms | 3% |
| gc_barabasi | syscall | tmpfs | none | flat | single | 10000 | 4 | 3 | 519.040ms | 537.804ms | 18.060ms | 3% |
| gc_barabasi | syscall | tmpfs | none | flat | single | 50000 | 4 | 3 | 3.379s | 3.422s | 44.457ms | 1% |
| gc_barabasi | syscall | tmpfs | none | sharded | multi | 2000 | 4 | 3 | 87.784ms | 90.142ms | 3.231ms | 4% |
| gc_barabasi | syscall | tmpfs | none | sharded | multi | 10000 | 4 | 3 | 505.522ms | 514.638ms | 8.457ms | 2% |
| gc_barabasi | syscall | tmpfs | none | sharded | multi | 50000 | 4 | 3 | 3.337s | 3.356s | 17.364ms | 1% |
| gc_barabasi | syscall | tmpfs | none | sharded | single | 2000 | 4 | 3 | 87.559ms | 94.937ms | 6.782ms | 8% |
| gc_barabasi | syscall | tmpfs | none | sharded | single | 10000 | 4 | 3 | 508.333ms | 523.716ms | 16.054ms | 3% |
| gc_barabasi | syscall | tmpfs | none | sharded | single | 50000 | 4 | 3 | 3.324s | 3.368s | 40.371ms | 1% |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 147.905ms | 152.768ms | 6.142ms | 4% |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 3.100s | 3.289s | 183.107ms | 6% |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 33.660s | 34.730s | 994.079ms | 3% |
| gc_barabasi | syscall | xfs | gp3 | flat | single | 2000 | 4 | 3 | 147.938ms | 151.249ms | 2.956ms | 2% |
| gc_barabasi | syscall | xfs | gp3 | flat | single | 10000 | 4 | 3 | 2.933s | 3.169s | 274.368ms | 9% |
| gc_barabasi | syscall | xfs | gp3 | flat | single | 50000 | 4 | 3 | 32.106s | 32.684s | 679.254ms | 2% |
| gc_barabasi | syscall | xfs | gp3 | sharded | multi | 2000 | 4 | 3 | 137.914ms | 138.180ms | 400.051µs | 0% |
| gc_barabasi | syscall | xfs | gp3 | sharded | multi | 10000 | 4 | 3 | 3.076s | 3.307s | 205.731ms | 7% |
| gc_barabasi | syscall | xfs | gp3 | sharded | multi | 50000 | 4 | 3 | 39.412s | 41.886s | 2.223s | 6% |
| gc_barabasi | syscall | xfs | gp3 | sharded | single | 2000 | 4 | 3 | 171.149ms | 235.360ms | 57.333ms | 33%* |
| gc_barabasi | syscall | xfs | gp3 | sharded | single | 10000 | 4 | 3 | 4.108s | 4.218s | 107.676ms | 3% |
| gc_barabasi | syscall | xfs | gp3 | sharded | single | 50000 | 4 | 3 | 51.103s | 53.472s | 2.586s | 5% |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 2000 | 4 | 3 | 147.703ms | 152.029ms | 5.436ms | 4% |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 10000 | 4 | 3 | 2.125s | 2.344s | 212.757ms | 10% |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 50000 | 4 | 3 | 22.718s | 23.143s | 491.091ms | 2% |
| gc_barabasi | syscall | xfs | io2 | flat | single | 2000 | 4 | 3 | 148.051ms | 150.838ms | 3.052ms | 2% |
| gc_barabasi | syscall | xfs | io2 | flat | single | 10000 | 4 | 3 | 2.111s | 2.225s | 192.837ms | 9% |
| gc_barabasi | syscall | xfs | io2 | flat | single | 50000 | 4 | 3 | 23.676s | 24.208s | 590.498ms | 2% |
| gc_barabasi | syscall | xfs | io2 | sharded | multi | 2000 | 4 | 3 | 182.041ms | 276.768ms | 84.568ms | 46%* |
| gc_barabasi | syscall | xfs | io2 | sharded | multi | 10000 | 4 | 3 | 2.193s | 2.508s | 351.956ms | 16% |
| gc_barabasi | syscall | xfs | io2 | sharded | multi | 50000 | 4 | 3 | 29.345s | 30.652s | 1.167s | 4% |
| gc_barabasi | syscall | xfs | io2 | sharded | single | 2000 | 4 | 3 | 178.372ms | 250.793ms | 64.894ms | 36%* |
| gc_barabasi | syscall | xfs | io2 | sharded | single | 10000 | 4 | 3 | 3.243s | 3.353s | 170.787ms | 5% |
| gc_barabasi | syscall | xfs | io2 | sharded | single | 50000 | 4 | 3 | 40.151s | 41.400s | 1.272s | 3% |
| gc_barabasi | syscall | xfs | none | flat | multi | 2000 | 4 | 3 | 150.356ms | 157.621ms | 6.485ms | 4% |
| gc_barabasi | syscall | xfs | none | flat | multi | 10000 | 4 | 3 | 1.814s | 2.026s | 216.933ms | 12% |
| gc_barabasi | syscall | xfs | none | flat | multi | 50000 | 4 | 3 | 20.957s | 21.687s | 693.461ms | 3% |
| gc_barabasi | syscall | xfs | none | flat | single | 2000 | 4 | 3 | 153.261ms | 156.248ms | 3.071ms | 2% |
| gc_barabasi | syscall | xfs | none | flat | single | 10000 | 4 | 3 | 1.924s | 2.246s | 300.677ms | 16% |
| gc_barabasi | syscall | xfs | none | flat | single | 50000 | 4 | 3 | 19.987s | 21.288s | 1.672s | 8% |
| gc_barabasi | syscall | xfs | none | sharded | multi | 2000 | 4 | 3 | 203.752ms | 246.977ms | 58.451ms | 29%* |
| gc_barabasi | syscall | xfs | none | sharded | multi | 10000 | 4 | 3 | 1.807s | 1.928s | 141.809ms | 8% |
| gc_barabasi | syscall | xfs | none | sharded | multi | 50000 | 4 | 3 | 26.079s | 26.705s | 806.456ms | 3% |
| gc_barabasi | syscall | xfs | none | sharded | single | 2000 | 4 | 3 | 196.012ms | 301.536ms | 94.202ms | 48%* |
| gc_barabasi | syscall | xfs | none | sharded | single | 10000 | 4 | 3 | 2.677s | 2.732s | 62.445ms | 2% |
| gc_barabasi | syscall | xfs | none | sharded | single | 50000 | 4 | 3 | 39.244s | 39.438s | 261.085ms | 1% |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 2000 | 4 | 3 | 145.750ms | 147.900ms | 2.424ms | 2% |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 10000 | 4 | 3 | 1.953s | 2.139s | 244.071ms | 12% |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 50000 | 4 | 3 | 21.199s | 22.163s | 1.383s | 7% |
| gc_barabasi | syscall | xfs | nvme | flat | single | 2000 | 4 | 3 | 148.152ms | 152.578ms | 4.721ms | 3% |
| gc_barabasi | syscall | xfs | nvme | flat | single | 10000 | 4 | 3 | 2.037s | 2.113s | 85.667ms | 4% |
| gc_barabasi | syscall | xfs | nvme | flat | single | 50000 | 4 | 3 | 20.580s | 21.509s | 840.088ms | 4% |
| gc_barabasi | syscall | xfs | nvme | sharded | multi | 2000 | 4 | 3 | 182.153ms | 255.787ms | 65.777ms | 36%* |
| gc_barabasi | syscall | xfs | nvme | sharded | multi | 10000 | 4 | 3 | 2.164s | 2.228s | 100.377ms | 5% |
| gc_barabasi | syscall | xfs | nvme | sharded | multi | 50000 | 4 | 3 | 26.148s | 28.038s | 1.804s | 7% |
| gc_barabasi | syscall | xfs | nvme | sharded | single | 2000 | 4 | 3 | 137.538ms | 138.608ms | 1.177ms | 1% |
| gc_barabasi | syscall | xfs | nvme | sharded | single | 10000 | 4 | 3 | 2.981s | 3.162s | 178.206ms | 6% |
| gc_barabasi | syscall | xfs | nvme | sharded | single | 50000 | 4 | 3 | 39.765s | 40.574s | 745.154ms | 2% |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 208.381ms | 220.097ms | 12.395ms | 6% |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 1.565s | 1.589s | 24.251ms | 2% |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 16.316s | 16.819s | 450.046ms | 3% |
| gc_barabasi | syscall | zfs | gp3 | flat | single | 2000 | 4 | 3 | 207.832ms | 218.177ms | 10.259ms | 5% |
| gc_barabasi | syscall | zfs | gp3 | flat | single | 10000 | 4 | 3 | 1.585s | 1.617s | 35.746ms | 2% |
| gc_barabasi | syscall | zfs | gp3 | flat | single | 50000 | 4 | 3 | 16.348s | 16.651s | 456.363ms | 3% |
| gc_barabasi | syscall | zfs | gp3 | sharded | multi | 2000 | 4 | 3 | 192.691ms | 198.174ms | 6.082ms | 3% |
| gc_barabasi | syscall | zfs | gp3 | sharded | multi | 10000 | 4 | 3 | 1.478s | 1.512s | 44.354ms | 3% |
| gc_barabasi | syscall | zfs | gp3 | sharded | multi | 50000 | 4 | 3 | 16.551s | 16.808s | 422.823ms | 3% |
| gc_barabasi | syscall | zfs | gp3 | sharded | single | 2000 | 4 | 3 | 195.130ms | 200.386ms | 5.520ms | 3% |
| gc_barabasi | syscall | zfs | gp3 | sharded | single | 10000 | 4 | 3 | 1.499s | 1.514s | 24.629ms | 2% |
| gc_barabasi | syscall | zfs | gp3 | sharded | single | 50000 | 4 | 3 | 16.262s | 17.382s | 1.018s | 6% |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 2000 | 4 | 3 | 199.412ms | 202.837ms | 5.057ms | 3% |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 10000 | 4 | 3 | 1.606s | 1.640s | 48.148ms | 3% |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 50000 | 4 | 3 | 16.811s | 17.623s | 738.036ms | 4% |
| gc_barabasi | syscall | zfs | io2 | flat | single | 2000 | 4 | 3 | 205.951ms | 220.271ms | 13.599ms | 7% |
| gc_barabasi | syscall | zfs | io2 | flat | single | 10000 | 4 | 3 | 1.563s | 1.575s | 11.751ms | 1% |
| gc_barabasi | syscall | zfs | io2 | flat | single | 50000 | 4 | 3 | 15.911s | 16.042s | 116.458ms | 1% |
| gc_barabasi | syscall | zfs | io2 | sharded | multi | 2000 | 4 | 3 | 195.906ms | 202.937ms | 9.124ms | 5% |
| gc_barabasi | syscall | zfs | io2 | sharded | multi | 10000 | 4 | 3 | 1.503s | 1.529s | 34.009ms | 2% |
| gc_barabasi | syscall | zfs | io2 | sharded | multi | 50000 | 4 | 3 | 17.666s | 18.016s | 313.320ms | 2% |
| gc_barabasi | syscall | zfs | io2 | sharded | single | 2000 | 4 | 3 | 193.629ms | 195.771ms | 2.046ms | 1% |
| gc_barabasi | syscall | zfs | io2 | sharded | single | 10000 | 4 | 3 | 1.527s | 1.569s | 38.766ms | 3% |
| gc_barabasi | syscall | zfs | io2 | sharded | single | 50000 | 4 | 3 | 15.745s | 16.363s | 715.462ms | 5% |
| gc_barabasi | syscall | zfs | none | flat | multi | 2000 | 4 | 3 | 206.358ms | 214.838ms | 8.824ms | 4% |
| gc_barabasi | syscall | zfs | none | flat | multi | 10000 | 4 | 3 | 1.597s | 1.646s | 43.353ms | 3% |
| gc_barabasi | syscall | zfs | none | flat | multi | 50000 | 4 | 3 | 16.947s | 18.906s | 1.788s | 11% |
| gc_barabasi | syscall | zfs | none | flat | single | 2000 | 4 | 3 | 211.715ms | 223.761ms | 13.239ms | 6% |
| gc_barabasi | syscall | zfs | none | flat | single | 10000 | 4 | 3 | 1.586s | 1.619s | 28.997ms | 2% |
| gc_barabasi | syscall | zfs | none | flat | single | 50000 | 4 | 3 | 18.837s | 24.528s | 5.101s | 27%* |
| gc_barabasi | syscall | zfs | none | sharded | multi | 2000 | 4 | 3 | 194.874ms | 205.486ms | 11.172ms | 6% |
| gc_barabasi | syscall | zfs | none | sharded | multi | 10000 | 4 | 3 | 1.464s | 1.485s | 20.009ms | 1% |
| gc_barabasi | syscall | zfs | none | sharded | multi | 50000 | 4 | 3 | 15.907s | 16.166s | 232.020ms | 1% |
| gc_barabasi | syscall | zfs | none | sharded | single | 2000 | 4 | 3 | 193.027ms | 199.730ms | 6.941ms | 4% |
| gc_barabasi | syscall | zfs | none | sharded | single | 10000 | 4 | 3 | 1.519s | 1.536s | 24.242ms | 2% |
| gc_barabasi | syscall | zfs | none | sharded | single | 50000 | 4 | 3 | 17.030s | 18.816s | 1.603s | 9% |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 2000 | 4 | 3 | 205.076ms | 207.057ms | 1.798ms | 1% |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 10000 | 4 | 3 | 1.583s | 1.643s | 57.945ms | 4% |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 50000 | 4 | 3 | 16.909s | 17.123s | 271.138ms | 2% |
| gc_barabasi | syscall | zfs | nvme | flat | single | 2000 | 4 | 3 | 207.009ms | 215.752ms | 8.325ms | 4% |
| gc_barabasi | syscall | zfs | nvme | flat | single | 10000 | 4 | 3 | 1.571s | 1.605s | 44.290ms | 3% |
| gc_barabasi | syscall | zfs | nvme | flat | single | 50000 | 4 | 3 | 16.424s | 16.888s | 414.054ms | 3% |
| gc_barabasi | syscall | zfs | nvme | sharded | multi | 2000 | 4 | 3 | 194.718ms | 205.916ms | 11.625ms | 6% |
| gc_barabasi | syscall | zfs | nvme | sharded | multi | 10000 | 4 | 3 | 1.506s | 1.622s | 103.979ms | 7% |
| gc_barabasi | syscall | zfs | nvme | sharded | multi | 50000 | 4 | 3 | 16.560s | 16.629s | 68.933ms | 0% |
| gc_barabasi | syscall | zfs | nvme | sharded | single | 2000 | 4 | 3 | 194.529ms | 202.879ms | 11.954ms | 6% |
| gc_barabasi | syscall | zfs | nvme | sharded | single | 10000 | 4 | 3 | 1.499s | 1.505s | 8.563ms | 1% |
| gc_barabasi | syscall | zfs | nvme | sharded | single | 50000 | 4 | 3 | 16.221s | 17.022s | 785.853ms | 5% |
| gc_clusters | syscall | btrfs | gp3 | flat | single | 2000 | 4 | 3 | 296.995ms | 300.567ms | 3.372ms | 1% |
| gc_clusters | syscall | btrfs | gp3 | flat | single | 10000 | 4 | 3 | 4.879s | 7.934s | 2.727s | 56%* |
| gc_clusters | syscall | btrfs | gp3 | flat | single | 50000 | 4 | 3 | 34.386s | 44.688s | 9.930s | 29%* |
| gc_clusters | syscall | btrfs | io2 | flat | single | 2000 | 4 | 3 | 308.173ms | 317.193ms | 8.279ms | 3% |
| gc_clusters | syscall | btrfs | io2 | flat | single | 10000 | 4 | 3 | 2.621s | 3.166s | 489.896ms | 19% |
| gc_clusters | syscall | btrfs | io2 | flat | single | 50000 | 4 | 3 | 19.141s | 20.684s | 1.381s | 7% |
| gc_clusters | syscall | btrfs | none | flat | single | 2000 | 4 | 3 | 307.595ms | 309.417ms | 2.113ms | 1% |
| gc_clusters | syscall | btrfs | none | flat | single | 10000 | 4 | 3 | 2.098s | 2.225s | 130.619ms | 6% |
| gc_clusters | syscall | btrfs | none | flat | single | 50000 | 4 | 3 | 14.405s | 15.198s | 716.924ms | 5% |
| gc_clusters | syscall | btrfs | nvme | flat | single | 2000 | 4 | 3 | 308.571ms | 321.210ms | 11.395ms | 4% |
| gc_clusters | syscall | btrfs | nvme | flat | single | 10000 | 4 | 3 | 2.168s | 2.360s | 185.828ms | 9% |
| gc_clusters | syscall | btrfs | nvme | flat | single | 50000 | 4 | 3 | 15.495s | 15.936s | 705.233ms | 5% |
| gc_clusters | syscall | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 117.863ms | 123.708ms | 5.665ms | 5% |
| gc_clusters | syscall | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 2.029s | 2.047s | 20.217ms | 1% |
| gc_clusters | syscall | ext4 | gp3 | flat | single | 2000 | 4 | 3 | 118.954ms | 124.073ms | 4.648ms | 4% |
| gc_clusters | syscall | ext4 | gp3 | flat | single | 10000 | 4 | 3 | 2.059s | 2.082s | 36.945ms | 2% |
| gc_clusters | syscall | ext4 | gp3 | flat | single | 50000 | 4 | 3 | 72.439s | 73.637s | 1.074s | 1% |
| gc_clusters | syscall | ext4 | io2 | flat | single | 2000 | 4 | 3 | 122.338ms | 125.182ms | 3.164ms | 3% |
| gc_clusters | syscall | ext4 | io2 | flat | single | 10000 | 4 | 3 | 1.116s | 1.125s | 9.188ms | 1% |
| gc_clusters | syscall | ext4 | io2 | flat | single | 50000 | 4 | 3 | 20.410s | 20.736s | 297.836ms | 1% |
| gc_clusters | syscall | ext4 | none | flat | single | 2000 | 4 | 3 | 122.061ms | 126.426ms | 3.925ms | 3% |
| gc_clusters | syscall | ext4 | none | flat | single | 10000 | 4 | 3 | 860.237ms | 864.201ms | 5.289ms | 1% |
| gc_clusters | syscall | ext4 | none | flat | single | 50000 | 4 | 3 | 9.122s | 9.131s | 14.368ms | 0% |
| gc_clusters | syscall | ext4 | nvme | flat | single | 2000 | 4 | 3 | 120.288ms | 121.889ms | 1.467ms | 1% |
| gc_clusters | syscall | ext4 | nvme | flat | single | 10000 | 4 | 3 | 929.592ms | 938.684ms | 8.208ms | 1% |
| gc_clusters | syscall | ext4 | nvme | flat | single | 50000 | 4 | 3 | 10.057s | 10.109s | 54.119ms | 1% |
| gc_clusters | syscall | tmpfs | none | flat | single | 2000 | 4 | 3 | 95.080ms | 98.895ms | 3.777ms | 4% |
| gc_clusters | syscall | tmpfs | none | flat | single | 10000 | 4 | 3 | 561.958ms | 573.621ms | 10.523ms | 2% |
| gc_clusters | syscall | tmpfs | none | flat | single | 50000 | 4 | 3 | 3.507s | 3.516s | 8.564ms | 0% |
| gc_clusters | syscall | xfs | gp3 | flat | single | 2000 | 4 | 3 | 179.531ms | 231.011ms | 46.259ms | 26%* |
| gc_clusters | syscall | xfs | gp3 | flat | single | 10000 | 4 | 3 | 3.108s | 3.517s | 373.174ms | 12% |
| gc_clusters | syscall | xfs | gp3 | flat | single | 50000 | 4 | 3 | 31.125s | 32.679s | 1.461s | 5% |
| gc_clusters | syscall | xfs | io2 | flat | single | 2000 | 4 | 3 | 159.644ms | 166.746ms | 8.705ms | 5% |
| gc_clusters | syscall | xfs | io2 | flat | single | 10000 | 4 | 3 | 2.277s | 2.618s | 350.091ms | 15% |
| gc_clusters | syscall | xfs | io2 | flat | single | 50000 | 4 | 3 | 21.682s | 22.643s | 1.039s | 5% |
| gc_clusters | syscall | xfs | none | flat | single | 2000 | 4 | 3 | 157.172ms | 157.536ms | 361.691µs | 0% |
| gc_clusters | syscall | xfs | none | flat | single | 10000 | 4 | 3 | 1.883s | 2.341s | 414.169ms | 22%* |
| gc_clusters | syscall | xfs | none | flat | single | 50000 | 4 | 3 | 20.075s | 20.699s | 557.056ms | 3% |
| gc_clusters | syscall | xfs | nvme | flat | single | 2000 | 4 | 3 | 154.122ms | 157.785ms | 4.609ms | 3% |
| gc_clusters | syscall | xfs | nvme | flat | single | 10000 | 4 | 3 | 1.907s | 2.132s | 316.408ms | 17% |
| gc_clusters | syscall | xfs | nvme | flat | single | 50000 | 4 | 3 | 20.601s | 21.393s | 785.885ms | 4% |
| gc_clusters | syscall | zfs | gp3 | flat | single | 2000 | 4 | 3 | 212.455ms | 219.501ms | 10.724ms | 5% |
| gc_clusters | syscall | zfs | gp3 | flat | single | 10000 | 4 | 3 | 1.643s | 1.660s | 18.146ms | 1% |
| gc_clusters | syscall | zfs | gp3 | flat | single | 50000 | 4 | 3 | 17.607s | 17.826s | 196.578ms | 1% |
| gc_clusters | syscall | zfs | io2 | flat | single | 2000 | 4 | 3 | 214.561ms | 221.928ms | 6.648ms | 3% |
| gc_clusters | syscall | zfs | io2 | flat | single | 10000 | 4 | 3 | 1.653s | 1.678s | 28.619ms | 2% |
| gc_clusters | syscall | zfs | io2 | flat | single | 50000 | 4 | 3 | 18.846s | 20.746s | 1.697s | 9% |
| gc_clusters | syscall | zfs | none | flat | single | 2000 | 4 | 3 | 214.259ms | 222.452ms | 8.208ms | 4% |
| gc_clusters | syscall | zfs | none | flat | single | 10000 | 4 | 3 | 1.644s | 1.655s | 10.891ms | 1% |
| gc_clusters | syscall | zfs | none | flat | single | 50000 | 4 | 3 | 18.291s | 18.712s | 619.706ms | 3% |
| gc_clusters | syscall | zfs | nvme | flat | single | 2000 | 4 | 3 | 213.673ms | 219.626ms | 6.921ms | 3% |
| gc_clusters | syscall | zfs | nvme | flat | single | 10000 | 4 | 3 | 1.632s | 1.675s | 38.625ms | 2% |
| gc_clusters | syscall | zfs | nvme | flat | single | 50000 | 4 | 3 | 18.668s | 19.452s | 700.591ms | 4% |
| optimise | none | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 308.317ms | 311.168ms | 3.147ms | 1% |
| optimise | none | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 1.576s | 1.585s | 7.983ms | 1% |
| optimise | none | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 10.218s | 11.782s | 1.948s | 19% |
| optimise | none | btrfs | gp3 | flat | single | 2000 | 4 | 3 | 297.630ms | 302.202ms | 4.117ms | 1% |
| optimise | none | btrfs | gp3 | flat | single | 10000 | 4 | 3 | 962.119ms | 975.021ms | 11.584ms | 1% |
| optimise | none | btrfs | gp3 | flat | single | 50000 | 4 | 3 | 2.456s | 2.474s | 26.045ms | 1% |
| optimise | none | btrfs | gp3 | sharded | multi | 2000 | 4 | 3 | 327.273ms | 330.707ms | 4.528ms | 1% |
| optimise | none | btrfs | gp3 | sharded | multi | 10000 | 4 | 3 | 1.627s | 1.647s | 18.569ms | 1% |
| optimise | none | btrfs | gp3 | sharded | multi | 50000 | 4 | 3 | 9.172s | 10.878s | 1.537s | 17% |
| optimise | none | btrfs | gp3 | sharded | single | 2000 | 4 | 3 | 304.564ms | 306.003ms | 1.875ms | 1% |
| optimise | none | btrfs | gp3 | sharded | single | 10000 | 4 | 3 | 848.863ms | 874.973ms | 25.381ms | 3% |
| optimise | none | btrfs | gp3 | sharded | single | 50000 | 4 | 3 | 2.358s | 3.226s | 774.618ms | 33%* |
| optimise | none | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 312.044ms | 318.609ms | 6.815ms | 2% |
| optimise | none | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 1.595s | 1.601s | 5.682ms | 0% |
| optimise | none | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 10.339s | 11.144s | 1.305s | 13% |
| optimise | none | btrfs | io2 | flat | single | 2000 | 4 | 3 | 293.652ms | 294.392ms | 688.353µs | 0% |
| optimise | none | btrfs | io2 | flat | single | 10000 | 4 | 3 | 969.434ms | 972.953ms | 5.941ms | 1% |
| optimise | none | btrfs | io2 | flat | single | 50000 | 4 | 3 | 2.562s | 2.765s | 181.025ms | 7% |
| optimise | none | btrfs | io2 | sharded | multi | 2000 | 4 | 3 | 331.565ms | 332.364ms | 993.138µs | 0% |
| optimise | none | btrfs | io2 | sharded | multi | 10000 | 4 | 3 | 1.603s | 1.622s | 17.404ms | 1% |
| optimise | none | btrfs | io2 | sharded | multi | 50000 | 4 | 3 | 9.279s | 11.018s | 1.597s | 17% |
| optimise | none | btrfs | io2 | sharded | single | 2000 | 4 | 3 | 305.178ms | 312.655ms | 7.905ms | 3% |
| optimise | none | btrfs | io2 | sharded | single | 10000 | 4 | 3 | 841.883ms | 851.755ms | 15.488ms | 2% |
| optimise | none | btrfs | io2 | sharded | single | 50000 | 4 | 3 | 2.851s | 3.468s | 776.976ms | 27%* |
| optimise | none | btrfs | none | flat | multi | 2000 | 4 | 3 | 317.201ms | 325.086ms | 8.245ms | 3% |
| optimise | none | btrfs | none | flat | multi | 10000 | 4 | 3 | 1.596s | 1.598s | 2.581ms | 0% |
| optimise | none | btrfs | none | flat | multi | 50000 | 4 | 3 | 9.457s | 11.672s | 2.016s | 21%* |
| optimise | none | btrfs | none | flat | single | 2000 | 4 | 3 | 301.249ms | 303.182ms | 2.166ms | 1% |
| optimise | none | btrfs | none | flat | single | 10000 | 4 | 3 | 959.602ms | 962.098ms | 2.417ms | 0% |
| optimise | none | btrfs | none | flat | single | 50000 | 4 | 3 | 3.369s | 3.945s | 814.953ms | 24%* |
| optimise | none | btrfs | none | sharded | multi | 2000 | 4 | 3 | 329.319ms | 340.204ms | 9.893ms | 3% |
| optimise | none | btrfs | none | sharded | multi | 10000 | 4 | 3 | 1.612s | 1.639s | 24.783ms | 2% |
| optimise | none | btrfs | none | sharded | multi | 50000 | 4 | 3 | 8.228s | 8.686s | 408.621ms | 5% |
| optimise | none | btrfs | none | sharded | single | 2000 | 4 | 3 | 304.634ms | 309.764ms | 4.580ms | 2% |
| optimise | none | btrfs | none | sharded | single | 10000 | 4 | 3 | 824.333ms | 830.384ms | 5.405ms | 1% |
| optimise | none | btrfs | none | sharded | single | 50000 | 4 | 3 | 2.731s | 3.218s | 720.218ms | 26%* |
| optimise | none | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 319.802ms | 324.000ms | 5.823ms | 2% |
| optimise | none | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 1.581s | 1.588s | 7.587ms | 0% |
| optimise | none | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 10.354s | 11.556s | 2.056s | 20% |
| optimise | none | btrfs | nvme | flat | single | 2000 | 4 | 3 | 297.218ms | 301.539ms | 5.274ms | 2% |
| optimise | none | btrfs | nvme | flat | single | 10000 | 4 | 3 | 973.250ms | 977.605ms | 5.752ms | 1% |
| optimise | none | btrfs | nvme | flat | single | 50000 | 4 | 3 | 3.123s | 4.104s | 875.574ms | 28%* |
| optimise | none | btrfs | nvme | sharded | multi | 2000 | 4 | 3 | 326.067ms | 333.540ms | 7.197ms | 2% |
| optimise | none | btrfs | nvme | sharded | multi | 10000 | 4 | 3 | 1.618s | 1.643s | 23.739ms | 1% |
| optimise | none | btrfs | nvme | sharded | multi | 50000 | 4 | 3 | 9.679s | 11.608s | 1.746s | 18% |
| optimise | none | btrfs | nvme | sharded | single | 2000 | 4 | 3 | 305.393ms | 305.752ms | 357.089µs | 0% |
| optimise | none | btrfs | nvme | sharded | single | 10000 | 4 | 3 | 838.432ms | 846.264ms | 7.065ms | 1% |
| optimise | none | btrfs | nvme | sharded | single | 50000 | 4 | 3 | 2.750s | 3.323s | 706.092ms | 26%* |
| optimise | none | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 131.425ms | 134.160ms | 3.055ms | 2% |
| optimise | none | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 747.877ms | 752.533ms | 4.157ms | 1% |
| optimise | none | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 53.926s | 55.209s | 1.475s | 3% |
| optimise | none | ext4 | gp3 | flat | single | 2000 | 4 | 3 | 130.888ms | 132.009ms | 1.578ms | 1% |
| optimise | none | ext4 | gp3 | flat | single | 10000 | 4 | 3 | 745.052ms | 751.734ms | 10.837ms | 1% |
| optimise | none | ext4 | gp3 | flat | single | 50000 | 4 | 3 | 44.788s | 54.010s | 14.199s | 32%* |
| optimise | none | ext4 | gp3 | sharded | multi | 2000 | 4 | 3 | 131.702ms | 134.845ms | 2.892ms | 2% |
| optimise | none | ext4 | gp3 | sharded | multi | 10000 | 4 | 3 | 752.228ms | 772.409ms | 18.018ms | 2% |
| optimise | none | ext4 | gp3 | sharded | multi | 50000 | 4 | 3 | 51.481s | 53.515s | 3.123s | 6% |
| optimise | none | ext4 | gp3 | sharded | single | 2000 | 4 | 3 | 123.978ms | 128.263ms | 3.837ms | 3% |
| optimise | none | ext4 | gp3 | sharded | single | 10000 | 4 | 3 | 455.870ms | 461.796ms | 5.619ms | 1% |
| optimise | none | ext4 | gp3 | sharded | single | 50000 | 4 | 3 | 31.847s | 33.289s | 1.292s | 4% |
| optimise | none | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 133.820ms | 136.816ms | 4.862ms | 4% |
| optimise | none | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 748.183ms | 758.650ms | 9.540ms | 1% |
| optimise | none | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 13.023s | 13.075s | 56.449ms | 0% |
| optimise | none | ext4 | io2 | flat | single | 2000 | 4 | 3 | 129.875ms | 130.421ms | 498.566µs | 0% |
| optimise | none | ext4 | io2 | flat | single | 10000 | 4 | 3 | 743.770ms | 757.888ms | 12.659ms | 2% |
| optimise | none | ext4 | io2 | flat | single | 50000 | 4 | 3 | 11.908s | 12.522s | 982.357ms | 8% |
| optimise | none | ext4 | io2 | sharded | multi | 2000 | 4 | 3 | 131.745ms | 134.364ms | 2.350ms | 2% |
| optimise | none | ext4 | io2 | sharded | multi | 10000 | 4 | 3 | 731.067ms | 739.921ms | 9.274ms | 1% |
| optimise | none | ext4 | io2 | sharded | multi | 50000 | 4 | 3 | 12.931s | 13.277s | 375.148ms | 3% |
| optimise | none | ext4 | io2 | sharded | single | 2000 | 4 | 3 | 123.735ms | 124.562ms | 1.386ms | 1% |
| optimise | none | ext4 | io2 | sharded | single | 10000 | 4 | 3 | 452.641ms | 454.089ms | 1.453ms | 0% |
| optimise | none | ext4 | io2 | sharded | single | 50000 | 4 | 3 | 7.461s | 8.045s | 541.948ms | 7% |
| optimise | none | ext4 | none | flat | multi | 2000 | 4 | 3 | 137.668ms | 152.131ms | 12.912ms | 9% |
| optimise | none | ext4 | none | flat | multi | 10000 | 4 | 3 | 749.118ms | 765.162ms | 15.399ms | 2% |
| optimise | none | ext4 | none | flat | multi | 50000 | 4 | 3 | 6.632s | 6.795s | 156.075ms | 2% |
| optimise | none | ext4 | none | flat | single | 2000 | 4 | 3 | 134.612ms | 137.513ms | 4.902ms | 4% |
| optimise | none | ext4 | none | flat | single | 10000 | 4 | 3 | 743.913ms | 757.629ms | 12.328ms | 2% |
| optimise | none | ext4 | none | flat | single | 50000 | 4 | 3 | 6.377s | 6.452s | 67.046ms | 1% |
| optimise | none | ext4 | none | sharded | multi | 2000 | 4 | 3 | 128.753ms | 130.002ms | 1.849ms | 1% |
| optimise | none | ext4 | none | sharded | multi | 10000 | 4 | 3 | 730.944ms | 741.379ms | 12.085ms | 2% |
| optimise | none | ext4 | none | sharded | multi | 50000 | 4 | 3 | 6.750s | 6.885s | 127.316ms | 2% |
| optimise | none | ext4 | none | sharded | single | 2000 | 4 | 3 | 125.331ms | 127.818ms | 2.446ms | 2% |
| optimise | none | ext4 | none | sharded | single | 10000 | 4 | 3 | 451.301ms | 456.189ms | 4.514ms | 1% |
| optimise | none | ext4 | none | sharded | single | 50000 | 4 | 3 | 3.227s | 3.413s | 216.352ms | 7% |
| optimise | none | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 132.176ms | 136.308ms | 3.730ms | 3% |
| optimise | none | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 744.751ms | 748.144ms | 3.272ms | 0% |
| optimise | none | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 6.717s | 6.848s | 148.810ms | 2% |
| optimise | none | ext4 | nvme | flat | single | 2000 | 4 | 3 | 130.970ms | 132.091ms | 1.019ms | 1% |
| optimise | none | ext4 | nvme | flat | single | 10000 | 4 | 3 | 751.893ms | 754.444ms | 4.291ms | 1% |
| optimise | none | ext4 | nvme | flat | single | 50000 | 4 | 3 | 6.322s | 6.452s | 115.821ms | 2% |
| optimise | none | ext4 | nvme | sharded | multi | 2000 | 4 | 3 | 132.168ms | 133.194ms | 956.611µs | 1% |
| optimise | none | ext4 | nvme | sharded | multi | 10000 | 4 | 3 | 737.467ms | 739.278ms | 1.848ms | 0% |
| optimise | none | ext4 | nvme | sharded | multi | 50000 | 4 | 3 | 6.784s | 6.948s | 151.293ms | 2% |
| optimise | none | ext4 | nvme | sharded | single | 2000 | 4 | 3 | 122.748ms | 127.474ms | 4.227ms | 3% |
| optimise | none | ext4 | nvme | sharded | single | 10000 | 4 | 3 | 455.219ms | 461.872ms | 7.081ms | 2% |
| optimise | none | ext4 | nvme | sharded | single | 50000 | 4 | 3 | 3.206s | 3.333s | 125.630ms | 4% |
| optimise | none | tmpfs | none | flat | multi | 2000 | 4 | 3 | 62.536ms | 66.388ms | 3.645ms | 6% |
| optimise | none | tmpfs | none | flat | multi | 10000 | 4 | 3 | 333.500ms | 337.797ms | 4.320ms | 1% |
| optimise | none | tmpfs | none | flat | multi | 50000 | 4 | 3 | 2.112s | 2.115s | 4.876ms | 0% |
| optimise | none | tmpfs | none | flat | single | 2000 | 4 | 3 | 57.973ms | 59.812ms | 1.686ms | 3% |
| optimise | none | tmpfs | none | flat | single | 10000 | 4 | 3 | 257.885ms | 258.614ms | 712.380µs | 0% |
| optimise | none | tmpfs | none | flat | single | 50000 | 4 | 3 | 1.021s | 1.035s | 12.592ms | 1% |
| optimise | none | tmpfs | none | sharded | multi | 2000 | 4 | 3 | 62.366ms | 67.001ms | 4.151ms | 7% |
| optimise | none | tmpfs | none | sharded | multi | 10000 | 4 | 3 | 335.582ms | 338.189ms | 2.390ms | 1% |
| optimise | none | tmpfs | none | sharded | multi | 50000 | 4 | 3 | 2.213s | 2.227s | 15.255ms | 1% |
| optimise | none | tmpfs | none | sharded | single | 2000 | 4 | 3 | 57.637ms | 58.288ms | 595.689µs | 1% |
| optimise | none | tmpfs | none | sharded | single | 10000 | 4 | 3 | 241.209ms | 242.872ms | 1.513ms | 1% |
| optimise | none | tmpfs | none | sharded | single | 50000 | 4 | 3 | 972.547ms | 981.832ms | 8.722ms | 1% |
| optimise | none | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 198.778ms | 310.620ms | 99.839ms | 50%* |
| optimise | none | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 1.925s | 1.985s | 53.820ms | 3% |
| optimise | none | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 18.572s | 19.380s | 933.735ms | 5% |
| optimise | none | xfs | gp3 | flat | single | 2000 | 4 | 3 | 252.830ms | 304.572ms | 86.647ms | 34%* |
| optimise | none | xfs | gp3 | flat | single | 10000 | 4 | 3 | 1.930s | 1.969s | 65.218ms | 3% |
| optimise | none | xfs | gp3 | flat | single | 50000 | 4 | 3 | 18.297s | 18.723s | 551.495ms | 3% |
| optimise | none | xfs | gp3 | sharded | multi | 2000 | 4 | 3 | 171.752ms | 176.014ms | 6.367ms | 4% |
| optimise | none | xfs | gp3 | sharded | multi | 10000 | 4 | 3 | 2.137s | 2.193s | 57.284ms | 3% |
| optimise | none | xfs | gp3 | sharded | multi | 50000 | 4 | 3 | 19.407s | 19.423s | 16.639ms | 0% |
| optimise | none | xfs | gp3 | sharded | single | 2000 | 4 | 3 | 152.035ms | 161.829ms | 8.763ms | 6% |
| optimise | none | xfs | gp3 | sharded | single | 10000 | 4 | 3 | 727.537ms | 911.981ms | 172.813ms | 24%* |
| optimise | none | xfs | gp3 | sharded | single | 50000 | 4 | 3 | 5.384s | 5.652s | 361.473ms | 7% |
| optimise | none | xfs | io2 | flat | multi | 2000 | 4 | 3 | 153.911ms | 162.999ms | 8.306ms | 5% |
| optimise | none | xfs | io2 | flat | multi | 10000 | 4 | 3 | 1.990s | 2.021s | 43.483ms | 2% |
| optimise | none | xfs | io2 | flat | multi | 50000 | 4 | 3 | 18.935s | 19.115s | 305.130ms | 2% |
| optimise | none | xfs | io2 | flat | single | 2000 | 4 | 3 | 150.571ms | 157.610ms | 7.061ms | 5% |
| optimise | none | xfs | io2 | flat | single | 10000 | 4 | 3 | 2.004s | 2.050s | 45.116ms | 2% |
| optimise | none | xfs | io2 | flat | single | 50000 | 4 | 3 | 18.184s | 18.450s | 275.176ms | 2% |
| optimise | none | xfs | io2 | sharded | multi | 2000 | 4 | 3 | 166.201ms | 170.679ms | 4.450ms | 3% |
| optimise | none | xfs | io2 | sharded | multi | 10000 | 4 | 3 | 2.068s | 2.334s | 244.588ms | 12% |
| optimise | none | xfs | io2 | sharded | multi | 50000 | 4 | 3 | 19.470s | 19.809s | 320.769ms | 2% |
| optimise | none | xfs | io2 | sharded | single | 2000 | 4 | 3 | 158.952ms | 167.619ms | 8.461ms | 5% |
| optimise | none | xfs | io2 | sharded | single | 10000 | 4 | 3 | 545.848ms | 554.554ms | 10.975ms | 2% |
| optimise | none | xfs | io2 | sharded | single | 50000 | 4 | 3 | 5.123s | 5.341s | 351.133ms | 7% |
| optimise | none | xfs | none | flat | multi | 2000 | 4 | 3 | 201.497ms | 303.658ms | 91.792ms | 46%* |
| optimise | none | xfs | none | flat | multi | 10000 | 4 | 3 | 2.008s | 2.058s | 55.675ms | 3% |
| optimise | none | xfs | none | flat | multi | 50000 | 4 | 3 | 17.962s | 18.336s | 341.566ms | 2% |
| optimise | none | xfs | none | flat | single | 2000 | 4 | 3 | 152.462ms | 179.923ms | 25.460ms | 17% |
| optimise | none | xfs | none | flat | single | 10000 | 4 | 3 | 1.943s | 2.026s | 92.544ms | 5% |
| optimise | none | xfs | none | flat | single | 50000 | 4 | 3 | 18.230s | 18.933s | 658.806ms | 4% |
| optimise | none | xfs | none | sharded | multi | 2000 | 4 | 3 | 224.223ms | 324.644ms | 89.710ms | 40%* |
| optimise | none | xfs | none | sharded | multi | 10000 | 4 | 3 | 2.060s | 2.093s | 31.581ms | 2% |
| optimise | none | xfs | none | sharded | multi | 50000 | 4 | 3 | 19.609s | 19.651s | 37.579ms | 0% |
| optimise | none | xfs | none | sharded | single | 2000 | 4 | 3 | 156.548ms | 166.242ms | 8.658ms | 6% |
| optimise | none | xfs | none | sharded | single | 10000 | 4 | 3 | 556.735ms | 572.655ms | 16.415ms | 3% |
| optimise | none | xfs | none | sharded | single | 50000 | 4 | 3 | 5.443s | 5.668s | 210.319ms | 4% |
| optimise | none | xfs | nvme | flat | multi | 2000 | 4 | 3 | 189.757ms | 273.398ms | 74.901ms | 39%* |
| optimise | none | xfs | nvme | flat | multi | 10000 | 4 | 3 | 1.935s | 1.995s | 89.406ms | 5% |
| optimise | none | xfs | nvme | flat | multi | 50000 | 4 | 3 | 18.406s | 18.844s | 394.620ms | 2% |
| optimise | none | xfs | nvme | flat | single | 2000 | 4 | 3 | 217.184ms | 337.758ms | 107.766ms | 50%* |
| optimise | none | xfs | nvme | flat | single | 10000 | 4 | 3 | 1.988s | 2.071s | 77.412ms | 4% |
| optimise | none | xfs | nvme | flat | single | 50000 | 4 | 3 | 17.714s | 17.919s | 350.088ms | 2% |
| optimise | none | xfs | nvme | sharded | multi | 2000 | 4 | 3 | 221.055ms | 322.428ms | 90.567ms | 41%* |
| optimise | none | xfs | nvme | sharded | multi | 10000 | 4 | 3 | 2.131s | 2.177s | 72.256ms | 3% |
| optimise | none | xfs | nvme | sharded | multi | 50000 | 4 | 3 | 19.232s | 19.718s | 522.970ms | 3% |
| optimise | none | xfs | nvme | sharded | single | 2000 | 4 | 3 | 218.291ms | 335.716ms | 104.832ms | 48%* |
| optimise | none | xfs | nvme | sharded | single | 10000 | 4 | 3 | 702.949ms | 981.053ms | 248.271ms | 35%* |
| optimise | none | xfs | nvme | sharded | single | 50000 | 4 | 3 | 5.285s | 5.743s | 532.468ms | 10% |
| optimise | none | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 248.251ms | 255.243ms | 6.374ms | 3% |
| optimise | none | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 1.260s | 1.273s | 12.786ms | 1% |
| optimise | none | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 8.712s | 8.981s | 337.068ms | 4% |
| optimise | none | zfs | gp3 | flat | single | 2000 | 4 | 3 | 235.487ms | 237.729ms | 2.029ms | 1% |
| optimise | none | zfs | gp3 | flat | single | 10000 | 4 | 3 | 773.759ms | 780.544ms | 6.615ms | 1% |
| optimise | none | zfs | gp3 | flat | single | 50000 | 4 | 3 | 2.496s | 2.501s | 5.936ms | 0% |
| optimise | none | zfs | gp3 | sharded | multi | 2000 | 4 | 3 | 261.226ms | 280.751ms | 17.483ms | 7% |
| optimise | none | zfs | gp3 | sharded | multi | 10000 | 4 | 3 | 1.250s | 1.262s | 12.221ms | 1% |
| optimise | none | zfs | gp3 | sharded | multi | 50000 | 4 | 3 | 8.430s | 8.919s | 471.017ms | 6% |
| optimise | none | zfs | gp3 | sharded | single | 2000 | 4 | 3 | 232.976ms | 239.715ms | 7.613ms | 3% |
| optimise | none | zfs | gp3 | sharded | single | 10000 | 4 | 3 | 672.494ms | 680.706ms | 7.371ms | 1% |
| optimise | none | zfs | gp3 | sharded | single | 50000 | 4 | 3 | 2.053s | 2.072s | 17.910ms | 1% |
| optimise | none | zfs | io2 | flat | multi | 2000 | 4 | 3 | 242.781ms | 252.535ms | 8.815ms | 4% |
| optimise | none | zfs | io2 | flat | multi | 10000 | 4 | 3 | 1.317s | 1.404s | 79.684ms | 6% |
| optimise | none | zfs | io2 | flat | multi | 50000 | 4 | 3 | 8.374s | 8.494s | 161.024ms | 2% |
| optimise | none | zfs | io2 | flat | single | 2000 | 4 | 3 | 238.298ms | 245.012ms | 6.255ms | 3% |
| optimise | none | zfs | io2 | flat | single | 10000 | 4 | 3 | 782.616ms | 786.424ms | 3.501ms | 0% |
| optimise | none | zfs | io2 | flat | single | 50000 | 4 | 3 | 2.531s | 2.543s | 18.446ms | 1% |
| optimise | none | zfs | io2 | sharded | multi | 2000 | 4 | 3 | 249.013ms | 260.778ms | 11.101ms | 4% |
| optimise | none | zfs | io2 | sharded | multi | 10000 | 4 | 3 | 1.267s | 1.283s | 15.989ms | 1% |
| optimise | none | zfs | io2 | sharded | multi | 50000 | 4 | 3 | 8.387s | 8.872s | 433.140ms | 5% |
| optimise | none | zfs | io2 | sharded | single | 2000 | 4 | 3 | 230.997ms | 237.638ms | 7.246ms | 3% |
| optimise | none | zfs | io2 | sharded | single | 10000 | 4 | 3 | 673.237ms | 676.760ms | 3.165ms | 0% |
| optimise | none | zfs | io2 | sharded | single | 50000 | 4 | 3 | 2.059s | 2.068s | 14.587ms | 1% |
| optimise | none | zfs | none | flat | multi | 2000 | 4 | 3 | 245.012ms | 247.374ms | 2.684ms | 1% |
| optimise | none | zfs | none | flat | multi | 10000 | 4 | 3 | 1.261s | 1.271s | 11.152ms | 1% |
| optimise | none | zfs | none | flat | multi | 50000 | 4 | 3 | 8.467s | 8.501s | 46.585ms | 1% |
| optimise | none | zfs | none | flat | single | 2000 | 4 | 3 | 247.123ms | 262.805ms | 13.999ms | 6% |
| optimise | none | zfs | none | flat | single | 10000 | 4 | 3 | 780.602ms | 786.357ms | 6.699ms | 1% |
| optimise | none | zfs | none | flat | single | 50000 | 4 | 3 | 2.585s | 2.658s | 69.184ms | 3% |
| optimise | none | zfs | none | sharded | multi | 2000 | 4 | 3 | 252.391ms | 255.808ms | 3.994ms | 2% |
| optimise | none | zfs | none | sharded | multi | 10000 | 4 | 3 | 1.267s | 1.284s | 21.531ms | 2% |
| optimise | none | zfs | none | sharded | multi | 50000 | 4 | 3 | 8.493s | 8.630s | 159.877ms | 2% |
| optimise | none | zfs | none | sharded | single | 2000 | 4 | 3 | 232.728ms | 235.867ms | 3.031ms | 1% |
| optimise | none | zfs | none | sharded | single | 10000 | 4 | 3 | 676.386ms | 680.899ms | 6.405ms | 1% |
| optimise | none | zfs | none | sharded | single | 50000 | 4 | 3 | 2.086s | 2.093s | 9.990ms | 0% |
| optimise | none | zfs | nvme | flat | multi | 2000 | 4 | 3 | 248.153ms | 248.456ms | 496.990µs | 0% |
| optimise | none | zfs | nvme | flat | multi | 10000 | 4 | 3 | 1.254s | 1.262s | 9.670ms | 1% |
| optimise | none | zfs | nvme | flat | multi | 50000 | 4 | 3 | 8.420s | 8.637s | 324.252ms | 4% |
| optimise | none | zfs | nvme | flat | single | 2000 | 4 | 3 | 235.888ms | 242.141ms | 6.142ms | 3% |
| optimise | none | zfs | nvme | flat | single | 10000 | 4 | 3 | 780.990ms | 782.459ms | 1.529ms | 0% |
| optimise | none | zfs | nvme | flat | single | 50000 | 4 | 3 | 2.516s | 2.531s | 15.440ms | 1% |
| optimise | none | zfs | nvme | sharded | multi | 2000 | 4 | 3 | 255.864ms | 267.060ms | 10.296ms | 4% |
| optimise | none | zfs | nvme | sharded | multi | 10000 | 4 | 3 | 1.257s | 1.260s | 3.163ms | 0% |
| optimise | none | zfs | nvme | sharded | multi | 50000 | 4 | 3 | 8.350s | 8.489s | 179.175ms | 2% |
| optimise | none | zfs | nvme | sharded | single | 2000 | 4 | 3 | 231.950ms | 234.917ms | 4.140ms | 2% |
| optimise | none | zfs | nvme | sharded | single | 10000 | 4 | 3 | 677.343ms | 686.535ms | 10.238ms | 2% |
| optimise | none | zfs | nvme | sharded | single | 50000 | 4 | 3 | 2.047s | 2.060s | 14.331ms | 1% |
| optimise_migrate | none | btrfs | gp3 | flat | single | 2000 | 4 | 3 | 120.612ms | 122.609ms | 2.646ms | 2% |
| optimise_migrate | none | btrfs | gp3 | flat | single | 10000 | 4 | 3 | 1.176s | 1.184s | 7.470ms | 1% |
| optimise_migrate | none | btrfs | gp3 | flat | single | 50000 | 4 | 3 | 14.224s | 16.026s | 2.206s | 16% |
| optimise_migrate | none | btrfs | io2 | flat | single | 2000 | 4 | 3 | 120.517ms | 125.078ms | 4.075ms | 3% |
| optimise_migrate | none | btrfs | io2 | flat | single | 10000 | 4 | 3 | 1.171s | 1.194s | 22.486ms | 2% |
| optimise_migrate | none | btrfs | io2 | flat | single | 50000 | 4 | 3 | 13.453s | 14.809s | 2.327s | 17% |
| optimise_migrate | none | btrfs | none | flat | single | 2000 | 4 | 3 | 119.278ms | 126.034ms | 6.224ms | 5% |
| optimise_migrate | none | btrfs | none | flat | single | 10000 | 4 | 3 | 1.175s | 1.181s | 5.781ms | 0% |
| optimise_migrate | none | btrfs | none | flat | single | 50000 | 4 | 3 | 14.389s | 14.854s | 710.993ms | 5% |
| optimise_migrate | none | btrfs | nvme | flat | single | 2000 | 4 | 3 | 120.719ms | 125.020ms | 3.969ms | 3% |
| optimise_migrate | none | btrfs | nvme | flat | single | 10000 | 4 | 3 | 1.173s | 1.192s | 17.489ms | 1% |
| optimise_migrate | none | btrfs | nvme | flat | single | 50000 | 4 | 3 | 14.257s | 14.947s | 831.936ms | 6% |
| optimise_migrate | none | ext4 | gp3 | flat | single | 2000 | 4 | 3 | 58.079ms | 59.077ms | 897.706µs | 2% |
| optimise_migrate | none | ext4 | gp3 | flat | single | 10000 | 4 | 3 | 310.849ms | 319.233ms | 8.443ms | 3% |
| optimise_migrate | none | ext4 | gp3 | flat | single | 50000 | 4 | 3 | 36.000s | 36.051s | 83.024ms | 0% |
| optimise_migrate | none | ext4 | io2 | flat | single | 2000 | 4 | 3 | 58.795ms | 59.315ms | 486.862µs | 1% |
| optimise_migrate | none | ext4 | io2 | flat | single | 10000 | 4 | 3 | 309.704ms | 313.299ms | 3.828ms | 1% |
| optimise_migrate | none | ext4 | io2 | flat | single | 50000 | 4 | 3 | 8.244s | 8.351s | 95.770ms | 1% |
| optimise_migrate | none | ext4 | none | flat | single | 2000 | 4 | 3 | 63.670ms | 70.367ms | 5.986ms | 9% |
| optimise_migrate | none | ext4 | none | flat | single | 10000 | 4 | 3 | 304.492ms | 306.098ms | 1.625ms | 1% |
| optimise_migrate | none | ext4 | none | flat | single | 50000 | 4 | 3 | 3.545s | 3.681s | 121.624ms | 3% |
| optimise_migrate | none | ext4 | nvme | flat | single | 2000 | 4 | 3 | 59.432ms | 60.938ms | 1.520ms | 3% |
| optimise_migrate | none | ext4 | nvme | flat | single | 10000 | 4 | 3 | 306.130ms | 309.642ms | 3.139ms | 1% |
| optimise_migrate | none | ext4 | nvme | flat | single | 50000 | 4 | 3 | 3.638s | 3.759s | 114.124ms | 3% |
| optimise_migrate | none | tmpfs | none | flat | single | 2000 | 4 | 3 | 35.409ms | 35.726ms | 534.415µs | 2% |
| optimise_migrate | none | tmpfs | none | flat | single | 10000 | 4 | 3 | 301.943ms | 303.745ms | 1.687ms | 1% |
| optimise_migrate | none | tmpfs | none | flat | single | 50000 | 4 | 3 | 2.475s | 2.516s | 36.959ms | 1% |
| optimise_migrate | none | xfs | gp3 | flat | single | 2000 | 4 | 3 | 62.662ms | 72.641ms | 8.908ms | 14% |
| optimise_migrate | none | xfs | gp3 | flat | single | 10000 | 4 | 3 | 310.458ms | 311.316ms | 876.445µs | 0% |
| optimise_migrate | none | xfs | gp3 | flat | single | 50000 | 4 | 3 | 2.992s | 3.480s | 436.583ms | 15% |
| optimise_migrate | none | xfs | io2 | flat | single | 2000 | 4 | 3 | 60.749ms | 64.354ms | 3.457ms | 6% |
| optimise_migrate | none | xfs | io2 | flat | single | 10000 | 4 | 3 | 308.912ms | 310.464ms | 1.390ms | 0% |
| optimise_migrate | none | xfs | io2 | flat | single | 50000 | 4 | 3 | 3.075s | 3.179s | 117.637ms | 4% |
| optimise_migrate | none | xfs | none | flat | single | 2000 | 4 | 3 | 59.074ms | 61.572ms | 2.231ms | 4% |
| optimise_migrate | none | xfs | none | flat | single | 10000 | 4 | 3 | 304.121ms | 304.939ms | 1.314ms | 0% |
| optimise_migrate | none | xfs | none | flat | single | 50000 | 4 | 3 | 3.064s | 3.207s | 230.019ms | 8% |
| optimise_migrate | none | xfs | nvme | flat | single | 2000 | 4 | 3 | 60.478ms | 62.335ms | 1.657ms | 3% |
| optimise_migrate | none | xfs | nvme | flat | single | 10000 | 4 | 3 | 312.965ms | 314.428ms | 2.319ms | 1% |
| optimise_migrate | none | xfs | nvme | flat | single | 50000 | 4 | 3 | 3.033s | 3.141s | 152.627ms | 5% |
| optimise_migrate | none | zfs | gp3 | flat | single | 2000 | 4 | 3 | 113.833ms | 117.342ms | 3.241ms | 3% |
| optimise_migrate | none | zfs | gp3 | flat | single | 10000 | 4 | 3 | 1.061s | 1.093s | 28.826ms | 3% |
| optimise_migrate | none | zfs | gp3 | flat | single | 50000 | 4 | 3 | 13.961s | 15.061s | 982.709ms | 7% |
| optimise_migrate | none | zfs | io2 | flat | single | 2000 | 4 | 3 | 112.414ms | 114.220ms | 2.188ms | 2% |
| optimise_migrate | none | zfs | io2 | flat | single | 10000 | 4 | 3 | 1.066s | 1.103s | 32.857ms | 3% |
| optimise_migrate | none | zfs | io2 | flat | single | 50000 | 4 | 3 | 13.132s | 13.281s | 216.465ms | 2% |
| optimise_migrate | none | zfs | none | flat | single | 2000 | 4 | 3 | 111.729ms | 112.313ms | 984.805µs | 1% |
| optimise_migrate | none | zfs | none | flat | single | 10000 | 4 | 3 | 1.062s | 1.100s | 34.449ms | 3% |
| optimise_migrate | none | zfs | none | flat | single | 50000 | 4 | 3 | 13.654s | 13.850s | 214.180ms | 2% |
| optimise_migrate | none | zfs | nvme | flat | single | 2000 | 4 | 3 | 113.291ms | 115.651ms | 3.089ms | 3% |
| optimise_migrate | none | zfs | nvme | flat | single | 10000 | 4 | 3 | 1.036s | 1.040s | 5.166ms | 0% |
| optimise_migrate | none | zfs | nvme | flat | single | 50000 | 4 | 3 | 13.464s | 13.747s | 402.721ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 196.462ms | 205.365ms | 8.209ms | 4% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 1.895s | 2.133s | 211.952ms | 11% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | single | 10000 | 4 | 3 | 206.325ms | 209.537ms | 4.774ms | 2% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | single | 50000 | 4 | 3 | 3.042s | 4.401s | 1.214s | 40%* |
| optimise_with_concurrent_gc | none | btrfs | gp3 | sharded | multi | 10000 | 4 | 3 | 192.159ms | 204.300ms | 11.003ms | 6% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | sharded | multi | 50000 | 4 | 3 | 2.086s | 2.267s | 245.660ms | 12% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | sharded | single | 10000 | 4 | 3 | 229.855ms | 232.233ms | 2.949ms | 1% |
| optimise_with_concurrent_gc | none | btrfs | gp3 | sharded | single | 50000 | 4 | 3 | 9.887s | 22.988s | 11.711s | 118%* |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 194.082ms | 202.084ms | 7.143ms | 4% |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 1.996s | 2.253s | 229.485ms | 11% |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | single | 10000 | 4 | 3 | 221.431ms | 233.771ms | 12.019ms | 5% |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | single | 50000 | 4 | 3 | 3.777s | 6.601s | 2.521s | 67%* |
| optimise_with_concurrent_gc | none | btrfs | io2 | sharded | multi | 10000 | 4 | 3 | 189.483ms | 197.753ms | 8.261ms | 4% |
| optimise_with_concurrent_gc | none | btrfs | io2 | sharded | multi | 50000 | 4 | 3 | 1.988s | 2.161s | 164.859ms | 8% |
| optimise_with_concurrent_gc | none | btrfs | io2 | sharded | single | 10000 | 4 | 3 | 227.199ms | 229.022ms | 3.066ms | 1% |
| optimise_with_concurrent_gc | none | btrfs | io2 | sharded | single | 50000 | 4 | 3 | 2.656s | 3.237s | 518.301ms | 20% |
| optimise_with_concurrent_gc | none | btrfs | none | flat | multi | 10000 | 4 | 3 | 184.998ms | 190.657ms | 5.153ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | none | flat | multi | 50000 | 4 | 3 | 1.929s | 2.171s | 220.377ms | 11% |
| optimise_with_concurrent_gc | none | btrfs | none | flat | single | 10000 | 4 | 3 | 210.606ms | 215.006ms | 3.990ms | 2% |
| optimise_with_concurrent_gc | none | btrfs | none | flat | single | 50000 | 4 | 3 | 2.661s | 2.890s | 336.256ms | 13% |
| optimise_with_concurrent_gc | none | btrfs | none | sharded | multi | 10000 | 4 | 3 | 188.841ms | 196.146ms | 6.614ms | 4% |
| optimise_with_concurrent_gc | none | btrfs | none | sharded | multi | 50000 | 4 | 3 | 1.985s | 2.104s | 106.875ms | 5% |
| optimise_with_concurrent_gc | none | btrfs | none | sharded | single | 10000 | 4 | 3 | 237.383ms | 244.426ms | 7.930ms | 3% |
| optimise_with_concurrent_gc | none | btrfs | none | sharded | single | 50000 | 4 | 3 | 2.866s | 3.233s | 346.224ms | 12% |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 186.641ms | 190.806ms | 3.896ms | 2% |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 1.939s | 2.233s | 264.033ms | 14% |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | single | 10000 | 4 | 3 | 220.272ms | 231.927ms | 10.495ms | 5% |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | single | 50000 | 4 | 3 | 3.369s | 3.886s | 840.134ms | 25%* |
| optimise_with_concurrent_gc | none | btrfs | nvme | sharded | multi | 10000 | 4 | 3 | 187.344ms | 195.181ms | 8.019ms | 4% |
| optimise_with_concurrent_gc | none | btrfs | nvme | sharded | multi | 50000 | 4 | 3 | 1.957s | 2.105s | 132.906ms | 7% |
| optimise_with_concurrent_gc | none | btrfs | nvme | sharded | single | 10000 | 4 | 3 | 230.047ms | 236.230ms | 10.130ms | 4% |
| optimise_with_concurrent_gc | none | btrfs | nvme | sharded | single | 50000 | 4 | 3 | 2.689s | 3.120s | 405.893ms | 15% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 164.354ms | 167.779ms | 3.135ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 4.632s | 5.163s | 894.033ms | 19% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | single | 10000 | 4 | 3 | 169.686ms | 174.618ms | 5.190ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | single | 50000 | 4 | 3 | 5.172s | 5.194s | 23.198ms | 0% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | sharded | multi | 10000 | 4 | 3 | 174.593ms | 175.102ms | 761.956µs | 0% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | sharded | multi | 50000 | 4 | 3 | 5.178s | 5.217s | 36.181ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | sharded | single | 10000 | 4 | 3 | 192.082ms | 193.791ms | 2.536ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | gp3 | sharded | single | 50000 | 4 | 3 | 3.136s | 3.146s | 9.080ms | 0% |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 169.591ms | 178.566ms | 8.012ms | 5% |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 2.525s | 2.600s | 67.630ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | single | 10000 | 4 | 3 | 162.559ms | 167.110ms | 4.275ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | single | 50000 | 4 | 3 | 2.433s | 2.464s | 33.318ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | io2 | sharded | multi | 10000 | 4 | 3 | 178.722ms | 181.946ms | 3.113ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | io2 | sharded | multi | 50000 | 4 | 3 | 2.547s | 2.594s | 70.228ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | io2 | sharded | single | 10000 | 4 | 3 | 194.388ms | 201.231ms | 7.324ms | 4% |
| optimise_with_concurrent_gc | none | ext4 | io2 | sharded | single | 50000 | 4 | 3 | 2.186s | 2.235s | 45.460ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | none | flat | multi | 10000 | 4 | 3 | 167.111ms | 180.890ms | 12.301ms | 7% |
| optimise_with_concurrent_gc | none | ext4 | none | flat | multi | 50000 | 4 | 3 | 1.990s | 2.019s | 26.783ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | none | flat | single | 10000 | 4 | 3 | 164.197ms | 166.073ms | 2.667ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | none | flat | single | 50000 | 4 | 3 | 1.996s | 2.028s | 39.677ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | none | sharded | multi | 10000 | 4 | 3 | 168.724ms | 169.955ms | 1.176ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | none | sharded | multi | 50000 | 4 | 3 | 2.016s | 2.017s | 1.407ms | 0% |
| optimise_with_concurrent_gc | none | ext4 | none | sharded | single | 10000 | 4 | 3 | 199.760ms | 215.378ms | 14.219ms | 7% |
| optimise_with_concurrent_gc | none | ext4 | none | sharded | single | 50000 | 4 | 3 | 2.080s | 2.121s | 37.649ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 167.298ms | 173.460ms | 5.574ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 1.960s | 1.973s | 14.034ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | single | 10000 | 4 | 3 | 171.666ms | 175.119ms | 4.811ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | single | 50000 | 4 | 3 | 1.982s | 1.991s | 15.439ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | nvme | sharded | multi | 10000 | 4 | 3 | 174.534ms | 177.462ms | 4.124ms | 2% |
| optimise_with_concurrent_gc | none | ext4 | nvme | sharded | multi | 50000 | 4 | 3 | 1.975s | 1.999s | 22.428ms | 1% |
| optimise_with_concurrent_gc | none | ext4 | nvme | sharded | single | 10000 | 4 | 3 | 191.353ms | 196.556ms | 4.972ms | 3% |
| optimise_with_concurrent_gc | none | ext4 | nvme | sharded | single | 50000 | 4 | 3 | 2.057s | 2.071s | 21.220ms | 1% |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | multi | 10000 | 4 | 3 | 156.025ms | 163.608ms | 6.952ms | 4% |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | multi | 50000 | 4 | 3 | 1.595s | 1.641s | 61.773ms | 4% |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | single | 10000 | 4 | 3 | 164.573ms | 166.778ms | 2.244ms | 1% |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | single | 50000 | 4 | 3 | 1.669s | 1.706s | 40.076ms | 2% |
| optimise_with_concurrent_gc | none | tmpfs | none | sharded | multi | 10000 | 4 | 3 | 159.006ms | 161.716ms | 3.178ms | 2% |
| optimise_with_concurrent_gc | none | tmpfs | none | sharded | multi | 50000 | 4 | 3 | 1.685s | 1.857s | 153.480ms | 9% |
| optimise_with_concurrent_gc | none | tmpfs | none | sharded | single | 10000 | 4 | 3 | 174.457ms | 181.256ms | 7.238ms | 4% |
| optimise_with_concurrent_gc | none | tmpfs | none | sharded | single | 50000 | 4 | 3 | 1.743s | 1.788s | 39.819ms | 2% |
| optimise_with_concurrent_gc | none | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 1.813s | 1.833s | 32.199ms | 2% |
| optimise_with_concurrent_gc | none | xfs | gp3 | flat | single | 50000 | 4 | 3 | 1.742s | 1.829s | 86.561ms | 5% |
| optimise_with_concurrent_gc | none | xfs | gp3 | sharded | multi | 10000 | 4 | 3 | 171.169ms | 181.528ms | 9.422ms | 6% |
| optimise_with_concurrent_gc | none | xfs | gp3 | sharded | multi | 50000 | 4 | 3 | 1.850s | 1.934s | 92.612ms | 5% |
| optimise_with_concurrent_gc | none | xfs | gp3 | sharded | single | 50000 | 4 | 3 | 3.571s | 3.624s | 72.737ms | 2% |
| optimise_with_concurrent_gc | none | xfs | io2 | flat | multi | 50000 | 4 | 3 | 1.761s | 1.795s | 40.232ms | 2% |
| optimise_with_concurrent_gc | none | xfs | io2 | flat | single | 50000 | 4 | 3 | 1.802s | 1.899s | 156.227ms | 9% |
| optimise_with_concurrent_gc | none | xfs | io2 | sharded | multi | 50000 | 4 | 3 | 1.835s | 1.903s | 65.495ms | 4% |
| optimise_with_concurrent_gc | none | xfs | io2 | sharded | single | 50000 | 4 | 3 | 2.560s | 2.781s | 231.359ms | 9% |
| optimise_with_concurrent_gc | none | xfs | none | flat | multi | 50000 | 4 | 3 | 1.787s | 1.840s | 54.909ms | 3% |
| optimise_with_concurrent_gc | none | xfs | none | flat | single | 10000 | 4 | 3 | 162.327ms | 164.185ms | 2.833ms | 2% |
| optimise_with_concurrent_gc | none | xfs | none | flat | single | 50000 | 4 | 3 | 1.764s | 1.895s | 116.724ms | 7% |
| optimise_with_concurrent_gc | none | xfs | none | sharded | multi | 10000 | 4 | 3 | 166.762ms | 175.616ms | 8.308ms | 5% |
| optimise_with_concurrent_gc | none | xfs | none | sharded | multi | 50000 | 4 | 3 | 1.760s | 1.851s | 127.219ms | 7% |
| optimise_with_concurrent_gc | none | xfs | none | sharded | single | 50000 | 4 | 3 | 2.461s | 2.533s | 91.207ms | 4% |
| optimise_with_concurrent_gc | none | xfs | nvme | flat | multi | 50000 | 4 | 3 | 1.762s | 1.834s | 79.962ms | 5% |
| optimise_with_concurrent_gc | none | xfs | nvme | flat | single | 50000 | 4 | 3 | 1.785s | 1.838s | 71.350ms | 4% |
| optimise_with_concurrent_gc | none | xfs | nvme | sharded | multi | 50000 | 4 | 3 | 1.787s | 1.890s | 98.297ms | 5% |
| optimise_with_concurrent_gc | none | xfs | nvme | sharded | single | 50000 | 4 | 3 | 2.504s | 2.846s | 305.690ms | 12% |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 189.555ms | 192.762ms | 3.089ms | 2% |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 1.844s | 1.868s | 24.604ms | 1% |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | single | 10000 | 4 | 3 | 210.239ms | 214.449ms | 4.139ms | 2% |
| optimise_with_concurrent_gc | none | zfs | gp3 | sharded | multi | 10000 | 4 | 3 | 198.468ms | 202.921ms | 7.062ms | 4% |
| optimise_with_concurrent_gc | none | zfs | gp3 | sharded | multi | 50000 | 4 | 3 | 1.895s | 1.922s | 27.248ms | 1% |
| optimise_with_concurrent_gc | none | zfs | gp3 | sharded | single | 10000 | 4 | 3 | 239.047ms | 243.348ms | 5.212ms | 2% |
| optimise_with_concurrent_gc | none | zfs | gp3 | sharded | single | 50000 | 4 | 3 | 2.449s | 2.468s | 21.549ms | 1% |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | multi | 10000 | 4 | 3 | 181.021ms | 183.402ms | 2.447ms | 1% |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | multi | 50000 | 4 | 3 | 1.874s | 1.900s | 24.819ms | 1% |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | single | 50000 | 4 | 3 | 2.480s | 2.562s | 72.661ms | 3% |
| optimise_with_concurrent_gc | none | zfs | io2 | sharded | multi | 10000 | 4 | 3 | 202.367ms | 204.682ms | 3.230ms | 2% |
| optimise_with_concurrent_gc | none | zfs | io2 | sharded | multi | 50000 | 4 | 3 | 1.878s | 1.925s | 47.469ms | 3% |
| optimise_with_concurrent_gc | none | zfs | io2 | sharded | single | 10000 | 4 | 3 | 243.329ms | 255.722ms | 11.285ms | 5% |
| optimise_with_concurrent_gc | none | zfs | io2 | sharded | single | 50000 | 4 | 3 | 2.467s | 2.549s | 74.537ms | 3% |
| optimise_with_concurrent_gc | none | zfs | none | flat | multi | 50000 | 4 | 3 | 1.847s | 1.862s | 25.051ms | 1% |
| optimise_with_concurrent_gc | none | zfs | none | flat | single | 50000 | 4 | 3 | 2.435s | 2.456s | 18.432ms | 1% |
| optimise_with_concurrent_gc | none | zfs | none | sharded | multi | 10000 | 4 | 3 | 205.139ms | 210.698ms | 8.461ms | 4% |
| optimise_with_concurrent_gc | none | zfs | none | sharded | multi | 50000 | 4 | 3 | 1.903s | 1.930s | 27.829ms | 1% |
| optimise_with_concurrent_gc | none | zfs | none | sharded | single | 10000 | 4 | 3 | 232.425ms | 234.760ms | 3.569ms | 2% |
| optimise_with_concurrent_gc | none | zfs | none | sharded | single | 50000 | 4 | 3 | 2.467s | 2.481s | 14.037ms | 1% |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | multi | 10000 | 4 | 3 | 186.145ms | 187.018ms | 1.078ms | 1% |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | multi | 50000 | 4 | 3 | 1.833s | 1.862s | 26.985ms | 1% |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | single | 50000 | 4 | 3 | 2.479s | 2.553s | 71.212ms | 3% |
| optimise_with_concurrent_gc | none | zfs | nvme | sharded | multi | 10000 | 4 | 3 | 205.885ms | 208.729ms | 2.551ms | 1% |
| optimise_with_concurrent_gc | none | zfs | nvme | sharded | multi | 50000 | 4 | 3 | 1.937s | 1.996s | 55.337ms | 3% |
| optimise_with_concurrent_gc | none | zfs | nvme | sharded | single | 10000 | 4 | 3 | 239.129ms | 240.749ms | 1.865ms | 1% |
| optimise_with_concurrent_gc | none | zfs | nvme | sharded | single | 50000 | 4 | 3 | 2.518s | 2.568s | 50.657ms | 2% |

```console
> python3 ../nix/tests/nixos/nix-store-bench/bench.py summary-matrix --results-dir-a ubuntu-14900k-nvme-ext4/before --results-dir-b ubuntu-14900k-nvme-ext4/after --format md
```

```
# 133 pair(s); skipping 71 cell(s) only in before, 701 cell(s) only in after (--show-missing to list)
# 71 cell(s) only in before:
#   bench ∈ {gc_clusters(49), optimise_with_concurrent_gc(22)}
#   dispatch ∈ {none(22), syscall(49)}
#   fs ∈ {btrfs(16), ext4(14), tmpfs(4), xfs(20), zfs(17)}
#   throttle ∈ {gp3(15), io2(17), none(22), nvme(17)}
#   layout = flat
#   replica = multi
#   npaths ∈ {10000(21), 2000(33), 50000(17)}
#   threads = 4
# 701 cell(s) only in after:
#   bench ∈ {gc_barabasi(357), gc_clusters(51), optimise(153), optimise_migrate(51), optimise_with_concurrent_gc(89)}
#   dispatch ∈ {iouring(204), none(293), syscall(204)}
#   fs ∈ {btrfs(168), ext4(168), tmpfs(42), xfs(159), zfs(164)}
#   throttle ∈ {gp3(165), io2(164), none(208), nvme(164)}
#   layout ∈ {flat(333), sharded(368)}
#   replica ∈ {multi(236), single(465)}
#   npaths ∈ {10000(243), 2000(204), 50000(254)}
#   threads = 4
```

| bench | dispatch | fs | throttle | layout | replica | n | t | before_iters | after_iters | before_mean | after_mean | Δmean | before_p99 | after_p99 | before_cv | after_cv | verdict |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 282.886ms | 281.770ms | -0.4% | 289.010ms | 284.394ms | 2% | 1% | fail |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 6.768s | 4.511s | -33.4% | 11.258s | 7.350s | 59%* | 56%* | pass |
| gc_barabasi | syscall | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 27.724s | 29.914s | +7.9% | 27.839s | 34.486s | 1% | 14% | fail |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 3 | 279.971ms | 278.810ms | -0.4% | 283.529ms | 287.753ms | 1% | 3% | fail |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 2.991s | 2.379s | -20.5% | 3.740s | 2.865s | 22%* | 18% | pass |
| gc_barabasi | syscall | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 17.925s | 17.233s | -3.9% | 19.441s | 17.562s | 8% | 2% | pass |
| gc_barabasi | syscall | btrfs | none | flat | multi | 2000 | 4 | 3 | 3 | 279.514ms | 285.855ms | +2.3% | 283.976ms | 290.404ms | 2% | 2% | fail |
| gc_barabasi | syscall | btrfs | none | flat | multi | 10000 | 4 | 3 | 3 | 2.102s | 1.822s | -13.3% | 2.297s | 1.932s | 8% | 7% | pass |
| gc_barabasi | syscall | btrfs | none | flat | multi | 50000 | 4 | 3 | 3 | 13.773s | 14.237s | +3.4% | 13.942s | 14.575s | 2% | 3% | pass |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 3 | 277.189ms | 282.177ms | +1.8% | 281.547ms | 285.086ms | 2% | 1% | fail |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 3 | 2.324s | 1.924s | -17.2% | 2.595s | 2.071s | 10% | 8% | pass |
| gc_barabasi | syscall | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 14.942s | 15.888s | +6.3% | 15.580s | 15.919s | 4% | 0% | fail |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 186.773ms | 113.135ms | -39.4% | 196.586ms | 117.945ms | 5% | 5% | fail |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 3.762s | 1.991s | -47.1% | 3.784s | 2.004s | 1% | 1% | pass |
| gc_barabasi | syscall | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 59.735s | 68.673s | +15.0% | 62.939s | 69.841s | 5% | 2% | fail |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 3 | 186.397ms | 112.500ms | -39.6% | 190.696ms | 115.206ms | 2% | 2% | fail |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 3 | 1.945s | 1.098s | -43.5% | 1.969s | 1.118s | 2% | 2% | pass |
| gc_barabasi | syscall | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 3 | 18.363s | 18.989s | +3.4% | 18.917s | 19.291s | 3% | 1% | pass |
| gc_barabasi | syscall | ext4 | none | flat | multi | 2000 | 4 | 3 | 3 | 186.538ms | 115.355ms | -38.2% | 189.553ms | 120.927ms | 2% | 5% | fail |
| gc_barabasi | syscall | ext4 | none | flat | multi | 10000 | 4 | 3 | 3 | 1.402s | 819.141ms | -41.6% | 1.404s | 831.006ms | 0% | 1% | pass |
| gc_barabasi | syscall | ext4 | none | flat | multi | 50000 | 4 | 3 | 3 | 10.526s | 9.151s | -13.1% | 10.674s | 9.275s | 1% | 1% | pass |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 3 | 186.547ms | 112.615ms | -39.6% | 190.437ms | 118.889ms | 2% | 5% | fail |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 3 | 1.560s | 907.912ms | -41.8% | 1.574s | 914.196ms | 1% | 1% | pass |
| gc_barabasi | syscall | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 3 | 11.140s | 10.121s | -9.2% | 11.242s | 10.224s | 1% | 1% | pass |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 2000 | 4 | 3 | 3 | 125.182ms | 85.312ms | -31.9% | 131.728ms | 90.168ms | 5% | 6% | fail |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 10000 | 4 | 3 | 3 | 733.591ms | 518.830ms | -29.3% | 744.766ms | 538.312ms | 2% | 3% | pass |
| gc_barabasi | syscall | tmpfs | none | flat | multi | 50000 | 4 | 3 | 3 | 4.554s | 3.394s | -25.5% | 4.575s | 3.438s | 1% | 2% | pass |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 213.923ms | 147.905ms | -30.9% | 218.636ms | 152.768ms | 3% | 4% | fail |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 4.425s | 3.100s | -29.9% | 4.446s | 3.289s | 1% | 6% | pass |
| gc_barabasi | syscall | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 33.155s | 33.660s | +1.5% | 33.555s | 34.730s | 1% | 3% | pass |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 2000 | 4 | 3 | 3 | 217.106ms | 147.703ms | -32.0% | 220.406ms | 152.029ms | 2% | 4% | fail |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 2.607s | 2.125s | -18.5% | 2.669s | 2.344s | 2% | 10% | pass |
| gc_barabasi | syscall | xfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 21.476s | 22.718s | +5.8% | 21.967s | 23.143s | 4% | 2% | fail |
| gc_barabasi | syscall | xfs | none | flat | multi | 2000 | 4 | 3 | 3 | 220.132ms | 150.356ms | -31.7% | 223.368ms | 157.621ms | 1% | 4% | fail |
| gc_barabasi | syscall | xfs | none | flat | multi | 10000 | 4 | 3 | 3 | 2.067s | 1.814s | -12.2% | 2.116s | 2.026s | 3% | 12% | pass |
| gc_barabasi | syscall | xfs | none | flat | multi | 50000 | 4 | 3 | 3 | 19.122s | 20.957s | +9.6% | 19.411s | 21.687s | 2% | 3% | fail |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 2000 | 4 | 3 | 3 | 216.068ms | 145.750ms | -32.5% | 222.361ms | 147.900ms | 3% | 2% | fail |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 10000 | 4 | 3 | 3 | 2.196s | 1.953s | -11.1% | 2.221s | 2.139s | 2% | 12% | pass |
| gc_barabasi | syscall | xfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 20.479s | 21.199s | +3.5% | 20.928s | 22.163s | 3% | 7% | pass |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 334.689ms | 208.381ms | -37.7% | 340.587ms | 220.097ms | 2% | 6% | fail |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 2.476s | 1.565s | -36.8% | 2.498s | 1.589s | 1% | 2% | pass |
| gc_barabasi | syscall | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 16.098s | 16.316s | +1.3% | 17.043s | 16.819s | 6% | 3% | pass |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 2000 | 4 | 3 | 3 | 326.477ms | 199.412ms | -38.9% | 333.752ms | 202.837ms | 3% | 3% | fail |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 2.477s | 1.606s | -35.1% | 2.494s | 1.640s | 1% | 3% | pass |
| gc_barabasi | syscall | zfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 16.589s | 16.811s | +1.3% | 16.841s | 17.623s | 2% | 4% | pass |
| gc_barabasi | syscall | zfs | none | flat | multi | 2000 | 4 | 3 | 3 | 332.868ms | 206.358ms | -38.0% | 339.557ms | 214.838ms | 2% | 4% | fail |
| gc_barabasi | syscall | zfs | none | flat | multi | 10000 | 4 | 3 | 3 | 2.472s | 1.597s | -35.4% | 2.490s | 1.646s | 1% | 3% | pass |
| gc_barabasi | syscall | zfs | none | flat | multi | 50000 | 4 | 3 | 3 | 16.602s | 16.947s | +2.1% | 17.375s | 18.906s | 4% | 11% | pass |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 2000 | 4 | 3 | 3 | 340.945ms | 205.076ms | -39.9% | 356.835ms | 207.057ms | 4% | 1% | fail |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 10000 | 4 | 3 | 3 | 2.470s | 1.583s | -35.9% | 2.513s | 1.643s | 2% | 4% | pass |
| gc_barabasi | syscall | zfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 16.400s | 16.909s | +3.1% | 16.749s | 17.123s | 2% | 2% | pass |
| gc_clusters | syscall | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 181.533ms | 117.863ms | -35.1% | 187.421ms | 123.708ms | 4% | 5% | fail |
| gc_clusters | syscall | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 3.764s | 2.029s | -46.1% | 3.775s | 2.047s | 0% | 1% | pass |
| optimise | none | btrfs | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 521.105ms | 308.317ms | -40.8% | 532.434ms | 311.168ms | 2% | 1% | pass |
| optimise | none | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 2.823s | 1.576s | -44.2% | 2.859s | 1.585s | 1% | 1% | pass |
| optimise | none | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 14.997s | 10.218s | -31.9% | 15.946s | 11.782s | 6% | 19% | pass |
| optimise | none | btrfs | io2 | flat | multi | 2000 | 4 | 3 | 3 | 526.739ms | 312.044ms | -40.8% | 537.980ms | 318.609ms | 2% | 2% | pass |
| optimise | none | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 2.751s | 1.595s | -42.0% | 2.784s | 1.601s | 1% | 0% | pass |
| optimise | none | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 16.871s | 10.339s | -38.7% | 18.345s | 11.144s | 13% | 13% | pass |
| optimise | none | btrfs | none | flat | multi | 2000 | 4 | 3 | 3 | 525.276ms | 317.201ms | -39.6% | 545.039ms | 325.086ms | 3% | 3% | pass |
| optimise | none | btrfs | none | flat | multi | 10000 | 4 | 3 | 3 | 2.751s | 1.596s | -42.0% | 2.775s | 1.598s | 1% | 0% | pass |
| optimise | none | btrfs | none | flat | multi | 50000 | 4 | 3 | 3 | 15.890s | 9.457s | -40.5% | 18.145s | 11.672s | 13% | 21%* | pass |
| optimise | none | btrfs | nvme | flat | multi | 2000 | 4 | 3 | 3 | 532.872ms | 319.802ms | -40.0% | 541.345ms | 324.000ms | 2% | 2% | pass |
| optimise | none | btrfs | nvme | flat | multi | 10000 | 4 | 3 | 3 | 2.796s | 1.581s | -43.5% | 2.813s | 1.588s | 1% | 0% | pass |
| optimise | none | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 15.689s | 10.354s | -34.0% | 18.092s | 11.556s | 14% | 20% | pass |
| optimise | none | ext4 | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 356.717ms | 131.425ms | -63.2% | 357.572ms | 134.160ms | 0% | 2% | pass |
| optimise | none | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 1.967s | 747.877ms | -62.0% | 1.992s | 752.533ms | 1% | 1% | pass |
| optimise | none | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 61.097s | 53.926s | -11.7% | 63.239s | 55.209s | 4% | 3% | pass |
| optimise | none | ext4 | io2 | flat | multi | 2000 | 4 | 3 | 3 | 359.739ms | 133.820ms | -62.8% | 363.736ms | 136.816ms | 1% | 4% | pass |
| optimise | none | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 3 | 1.999s | 748.183ms | -62.6% | 2.017s | 758.650ms | 1% | 1% | pass |
| optimise | none | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 3 | 19.901s | 13.023s | -34.6% | 20.052s | 13.075s | 1% | 0% | pass |
| optimise | none | ext4 | none | flat | multi | 2000 | 4 | 3 | 3 | 360.837ms | 137.668ms | -61.8% | 362.986ms | 152.131ms | 1% | 9% | pass |
| optimise | none | ext4 | none | flat | multi | 10000 | 4 | 3 | 3 | 2.043s | 749.118ms | -63.3% | 2.053s | 765.162ms | 0% | 2% | pass |
| optimise | none | ext4 | none | flat | multi | 50000 | 4 | 3 | 3 | 12.539s | 6.632s | -47.1% | 12.641s | 6.795s | 1% | 2% | pass |
| optimise | none | ext4 | nvme | flat | multi | 2000 | 4 | 3 | 3 | 362.697ms | 132.176ms | -63.6% | 365.887ms | 136.308ms | 1% | 3% | pass |
| optimise | none | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 3 | 1.967s | 744.751ms | -62.1% | 2.001s | 748.144ms | 2% | 0% | pass |
| optimise | none | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 3 | 12.473s | 6.717s | -46.1% | 12.588s | 6.848s | 1% | 2% | pass |
| optimise | none | tmpfs | none | flat | multi | 2000 | 4 | 3 | 3 | 245.909ms | 62.536ms | -74.6% | 251.746ms | 66.388ms | 2% | 6% | pass |
| optimise | none | tmpfs | none | flat | multi | 10000 | 4 | 3 | 3 | 1.223s | 333.500ms | -72.7% | 1.226s | 337.797ms | 0% | 1% | pass |
| optimise | none | tmpfs | none | flat | multi | 50000 | 4 | 3 | 3 | 6.176s | 2.112s | -65.8% | 6.183s | 2.115s | 0% | 0% | pass |
| optimise | none | xfs | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 425.929ms | 198.778ms | -53.3% | 430.261ms | 310.620ms | 2% | 50%* | pass |
| optimise | none | xfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 2.885s | 1.925s | -33.3% | 2.957s | 1.985s | 3% | 3% | pass |
| optimise | none | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 19.428s | 18.572s | -4.4% | 19.829s | 19.380s | 2% | 5% | pass |
| optimise | none | xfs | io2 | flat | multi | 2000 | 4 | 3 | 3 | 423.132ms | 153.911ms | -63.6% | 430.390ms | 162.999ms | 2% | 5% | pass |
| optimise | none | xfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 3.012s | 1.990s | -33.9% | 3.204s | 2.021s | 6% | 2% | pass |
| optimise | none | xfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 20.068s | 18.935s | -5.6% | 21.231s | 19.115s | 5% | 2% | pass |
| optimise | none | xfs | none | flat | multi | 2000 | 4 | 3 | 3 | 455.630ms | 201.497ms | -55.8% | 534.871ms | 303.658ms | 16% | 46%* | pass |
| optimise | none | xfs | none | flat | multi | 10000 | 4 | 3 | 3 | 2.908s | 2.008s | -31.0% | 2.947s | 2.058s | 1% | 3% | pass |
| optimise | none | xfs | none | flat | multi | 50000 | 4 | 3 | 3 | 19.446s | 17.962s | -7.6% | 19.877s | 18.336s | 2% | 2% | pass |
| optimise | none | xfs | nvme | flat | multi | 2000 | 4 | 3 | 3 | 520.010ms | 189.757ms | -63.5% | 586.633ms | 273.398ms | 18% | 39%* | pass |
| optimise | none | xfs | nvme | flat | multi | 10000 | 4 | 3 | 3 | 2.837s | 1.935s | -31.8% | 2.858s | 1.995s | 1% | 5% | pass |
| optimise | none | xfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 19.573s | 18.406s | -6.0% | 19.987s | 18.844s | 2% | 2% | pass |
| optimise | none | zfs | gp3 | flat | multi | 2000 | 4 | 3 | 3 | 553.178ms | 248.251ms | -55.1% | 563.271ms | 255.243ms | 2% | 3% | pass |
| optimise | none | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 3.049s | 1.260s | -58.7% | 3.062s | 1.273s | 0% | 1% | pass |
| optimise | none | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 15.705s | 8.712s | -44.5% | 15.911s | 8.981s | 1% | 4% | pass |
| optimise | none | zfs | io2 | flat | multi | 2000 | 4 | 3 | 3 | 557.282ms | 242.781ms | -56.4% | 563.949ms | 252.535ms | 2% | 4% | pass |
| optimise | none | zfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 3.025s | 1.317s | -56.5% | 3.069s | 1.404s | 2% | 6% | pass |
| optimise | none | zfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 15.508s | 8.374s | -46.0% | 15.617s | 8.494s | 1% | 2% | pass |
| optimise | none | zfs | none | flat | multi | 2000 | 4 | 3 | 3 | 567.753ms | 245.012ms | -56.8% | 583.065ms | 247.374ms | 4% | 1% | pass |
| optimise | none | zfs | none | flat | multi | 10000 | 4 | 3 | 3 | 2.980s | 1.261s | -57.7% | 3.009s | 1.271s | 1% | 1% | pass |
| optimise | none | zfs | none | flat | multi | 50000 | 4 | 3 | 3 | 15.443s | 8.467s | -45.2% | 15.557s | 8.501s | 1% | 1% | pass |
| optimise | none | zfs | nvme | flat | multi | 2000 | 4 | 3 | 3 | 545.442ms | 248.153ms | -54.5% | 561.998ms | 248.456ms | 3% | 0% | pass |
| optimise | none | zfs | nvme | flat | multi | 10000 | 4 | 3 | 3 | 3.053s | 1.254s | -58.9% | 3.101s | 1.262s | 2% | 1% | pass |
| optimise | none | zfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 15.442s | 8.420s | -45.5% | 15.456s | 8.637s | 0% | 4% | pass |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 2.673s | 196.462ms | -92.6% | 2.722s | 205.365ms | 2% | 4% | pass |
| optimise_with_concurrent_gc | none | btrfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 53.875s | 1.895s | -96.5% | 57.496s | 2.133s | 8% | 11% | pass |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 2.756s | 194.082ms | -93.0% | 2.848s | 202.084ms | 3% | 4% | pass |
| optimise_with_concurrent_gc | none | btrfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 45.642s | 1.996s | -95.6% | 46.257s | 2.253s | 2% | 11% | pass |
| optimise_with_concurrent_gc | none | btrfs | none | flat | multi | 10000 | 4 | 3 | 3 | 2.711s | 184.998ms | -93.2% | 2.762s | 190.657ms | 3% | 3% | pass |
| optimise_with_concurrent_gc | none | btrfs | none | flat | multi | 50000 | 4 | 3 | 3 | 46.578s | 1.929s | -95.9% | 47.019s | 2.171s | 1% | 11% | pass |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | multi | 10000 | 4 | 3! | 3 | 1.862s | 186.641ms | -90.0% | 2.710s | 190.806ms | 78%* | 2% | fail |
| optimise_with_concurrent_gc | none | btrfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 46.234s | 1.939s | -95.8% | 47.880s | 2.233s | 3% | 14% | pass |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 2.005s | 164.354ms | -91.8% | 2.032s | 167.779ms | 1% | 2% | pass |
| optimise_with_concurrent_gc | none | ext4 | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 41.262s | 4.632s | -88.8% | 42.367s | 5.163s | 3% | 19% | pass |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | multi | 10000 | 4 | 3 | 3 | 1.971s | 169.591ms | -91.4% | 2.065s | 178.566ms | 4% | 5% | pass |
| optimise_with_concurrent_gc | none | ext4 | io2 | flat | multi | 50000 | 4 | 3 | 3 | 34.544s | 2.525s | -92.7% | 34.875s | 2.600s | 1% | 3% | pass |
| optimise_with_concurrent_gc | none | ext4 | none | flat | multi | 10000 | 4 | 3 | 3 | 2.040s | 167.111ms | -91.8% | 2.101s | 180.890ms | 3% | 7% | pass |
| optimise_with_concurrent_gc | none | ext4 | none | flat | multi | 50000 | 4 | 3 | 3 | 33.137s | 1.990s | -94.0% | 33.332s | 2.019s | 1% | 1% | pass |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | multi | 10000 | 4 | 3 | 3 | 1.975s | 167.298ms | -91.5% | 2.014s | 173.460ms | 2% | 3% | pass |
| optimise_with_concurrent_gc | none | ext4 | nvme | flat | multi | 50000 | 4 | 3 | 3 | 33.772s | 1.960s | -94.2% | 34.212s | 1.973s | 1% | 1% | pass |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | multi | 10000 | 4 | 3! | 3 | 1.030s | 156.025ms | -84.9% | 1.466s | 163.608ms | 73%* | 4% | fail |
| optimise_with_concurrent_gc | none | tmpfs | none | flat | multi | 50000 | 4 | 3 | 3 | 23.379s | 1.595s | -93.2% | 23.629s | 1.641s | 1% | 4% | pass |
| optimise_with_concurrent_gc | none | xfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 44.093s | 1.813s | -95.9% | 44.494s | 1.833s | 1% | 2% | pass |
| optimise_with_concurrent_gc | none | xfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 43.621s | 1.761s | -96.0% | 44.317s | 1.795s | 1% | 2% | pass |
| optimise_with_concurrent_gc | none | xfs | none | flat | multi | 50000 | 4 | 3 | 3 | 44.542s | 1.787s | -96.0% | 45.302s | 1.840s | 2% | 3% | pass |
| optimise_with_concurrent_gc | none | xfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 43.608s | 1.762s | -96.0% | 43.755s | 1.834s | 0% | 5% | pass |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | multi | 10000 | 4 | 3 | 3 | 2.278s | 189.555ms | -91.7% | 2.337s | 192.762ms | 3% | 2% | pass |
| optimise_with_concurrent_gc | none | zfs | gp3 | flat | multi | 50000 | 4 | 3 | 3 | 36.108s | 1.844s | -94.9% | 36.519s | 1.868s | 1% | 1% | pass |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | multi | 10000 | 4 | 3 | 3 | 2.288s | 181.021ms | -92.1% | 2.409s | 183.402ms | 5% | 1% | pass |
| optimise_with_concurrent_gc | none | zfs | io2 | flat | multi | 50000 | 4 | 3 | 3 | 35.799s | 1.874s | -94.8% | 36.049s | 1.900s | 1% | 1% | pass |
| optimise_with_concurrent_gc | none | zfs | none | flat | multi | 50000 | 4 | 3 | 3 | 36.447s | 1.847s | -94.9% | 37.099s | 1.862s | 2% | 1% | pass |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | multi | 10000 | 4 | 3 | 3 | 2.264s | 186.145ms | -91.8% | 2.290s | 187.018ms | 1% | 1% | pass |
| optimise_with_concurrent_gc | none | zfs | nvme | flat | multi | 50000 | 4 | 3 | 3 | 35.623s | 1.833s | -94.9% | 35.852s | 1.862s | 1% | 1% | pass |

```
# `!` next to iters = cell reported caught throws; row is suspect
# `*` after a CV value = noise > 20%; Δmean is mostly statistical
# 133 pair(s)  pass: 108  fail: 25  missing: 0
```
