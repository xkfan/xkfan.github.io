# Simulating Arm SVE with The Gem5 Simulator

## Set SVE vector length

[ref in stackoverflow](https://stackoverflow.com/questions/57692765/how-to-change-the-gem5-arm-sve-vector-length)

### Command line option

For SE:

```bash
se.py --param 'system.cpu[:].isa[:].sve_vl_se = 2'
```

For FS:

```bash
fs.py --param 'system.sve_vl = 2'
```

where the values are given in multiples of 128 bits, so 2 means length 256.

### Inside the simulation (only in Full system mode):

```bash
echo VL >/proc/sys/abi/sve_default_vector_length
```

where 'VL' is in multiples of bytes, 32 means length 256 bits.
