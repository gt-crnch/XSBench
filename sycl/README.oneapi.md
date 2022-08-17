
### Compiling
This Makefile likely looks somewhat strange but this is because there is some overlap in flags between FPGA emulation, report generation, and HW bitstream generation.

To compile, pick one of the following options:
```
# CPU only
make CC=dpcpp
# FPGA emulation
make CC=dpcpp DPCPP_MODE=fpga_emu
# FPGA report
make CC=dpcpp DPCPP_MODE=fpga_report
# HW bitstream
make CC=dpcpp DPCPP_MODE=fpga_hw
#HW and Profile
make CC=dpcpp DPCPP_MODE=fpga_profile
```

### Running the code
Right now, the code specifies which device is used via an environment variable: XS_DEVICE

CPU execution
```
XS_DEVICE=cpu ./XSBench -m event
```

Emulation and FPGA code requires the use of this env variable:
```
./XSBench.fpga_emu -m event
...
Initializing device buffers and JIT compiling kernel...
terminate called after throwing an instance of 'cl::sycl::runtime_error'
  what():  Native API failed. Native API returns: -42 (CL_INVALID_BINARY) -42 (CL_INVALID_BINARY)
Aborted (core dumped)
#Run with the env variable
$ XS_DEVICE=FPGA_EMU ./XSBench.fpga_emu -m event
```

CPU execution of the extra-large (3.89 GB size) simulation using the hash grid search method. The -b flag
allows for writing the inputs to a file for use with future simulations. Note that the XL simulation size
will create a 3.9 GB file.
```
XS_DEVICE=FPGA ./XSBench.cpu -m event -b write -s XL -G hash
```
