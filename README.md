# hsperf

Prints HotSpot perf counters, even when the target JVM is started with `-XX:+PerfDisableSharedMem` flag.
Unlike other similar utilities, it does **not** rely on access to `/tmp/hsperfdata_user` files.

### Usage

```
hsperf <pid> [-i <ms>] [<counter>...]
```

If only `<pid>` is specified, the program prints all counters with their names.  
If a space separated list of counter names is given, the program prints values
of the specified counters, one value per line. If `-i <ms>` is given, the counters
will be queried and printed all `<ms>` milliseconds.

### How it works

1. Reads `/proc/[pid]/maps` to find the location and the base address of `libjvm.so`.
2. Parses `libjvm.so` to get the address of `PerfData` structure.
3. Calls [`process_vm_readv`](https://man7.org/linux/man-pages/man2/process_vm_readv.2.html)
   to read `PerfData` of the target JVM.

If `libjvm.so` does not contain debug symbols, the program gets the address of
`VMStructs` instead (which is always available) and then looks for `PerfData`
addresses using `VMStructs`.

### Supported OS

Linux 3.2+ 64-bit
