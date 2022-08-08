# Linux binary


## objdump

```bash
objdump -x libxxxxx.so | grep NEEDED          # Check dependencies of shared libs
```

## readelf

```bash
readelf -l exe          # Display the program headers
                        # (helpful to deal with 'not found' problem of executables)
readelf -d exe          # Display the dynamic section (if present)
```
