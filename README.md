<p align="center">
   <img src="res/logo.png" alt="Sunny D logo"></img>
   <!-- I'm sorry everyone but I spell it that way so that screen readers will say it correctly. This spelling is
      - not endorsed in any way by the sunneed development team. -->
</p>

[![Build Status](https://dev.azure.com/gwsystems/sunneed/_apis/build/status/gwsystems.sunneed?branchName=master)](https://dev.azure.com/gwsystems/sunneed/_build/latest?definitionId=3&branchName=master)

`sunneed` (pronounced "Sunny D") is a framework for tracking and managing the distribution of power consumption of individual 
processes in multi-tenant computing environments. In systems with uncertain reserves of power available, such as a computer
powered by a solar battery, it is impossible to guarantee unlimited power to each tenant. The basis of this project is to make
toolset for tenants to run **power-constrained code** on the system, limited by a power budget described by `sunneed`.

# Installation

Below is instructions for getting a basic instance of `sunneed` running on a Linux host. Package names are intentionally
left ambiguous due to the variety of naming schemes for packages across distros; see the `misc/install_dependencies`
file for package names on Ubuntu 18.04.

If you are running Ubuntu 18.04 (and likely other versions of Ubuntu and Debian), you may run`misc/install_dependencies` 
(with root privileges) and then skip to [Dependencies of Dependencies](#dependencies-of-dependencies).

## Getting the dependencies

`sunneed` uses protobufs for I2C. Specifically, it uses the `protobufs-c` library. Both the `protoc-c` compiler and the
`libprotobuf-c` headers and libraries must be installed.

### Dependencies of dependencies

We also need to manually compile some dependencies. Run `git submodule update --init --recursive`. This will download
the code for NNG and libbq27441, which must be compiled manually on common distros.

For building NNG, you will need CMake > 3.13 and Ninja. These are available as packages on common distros.

For building libbq27441, you will need the headers for the Linux I2C library. This comes under different names depending
on the distro; you want whichever package gives you the file `/usr/include/linux/i2c-dev.h`.

## Compilation

Once you have all the dependencies in place, we can begin compilation. First, create the output directory by running
`mkdir build`. Then, begin compilation by running `make` in the root of the `sunneed` directory. This should compile all
the local dependencies, the `sunneed` runtime overlay, client library, and main executable.

After all this, there should be a file `build/sunneed` in the `sunneed` directory. This is the core `sunneed` binary.
You can run this, and then run one of the example programs to test connectivity to `sunneed`.


## Running with overlay tester

Once you have successfully built sunneed and verified that the `build/sunneed` binary has been made, we can run the overlay tester to verify that sunneed is correctly intercepting `open()` and `write()` requests. *Pro tip: open a second terminal so that you don't have to type around the sunneed log prints.* Execute the sunneed binary by running `sudo ./sunneed &`. Note that the `&` is not necessary if you are running in a second terminal. 
![Sunneed](./res/Run_SunneeD.JPG)

In your second terminal or after sunneed begins execution, we can run the overlay_tester located in `sunneed/build/`. Before running it, we have to `LD_PRELOAD` SunneeD's overlay so that the `open()` and `write()` calls are redirected correctly. You can do all of this with the following command: `LD_PRELOAD=<absolute path to sunneed_overlay.so> ./build/overlay_tester` from the sunneed directory. **Note: the argument to LD_PRELOAD must be the ABSOLUTE path of sunneed_overlay.so, using a relative path will not work.**

![Overlay_test](./res/Run_Overlay_LDPRELOAD.JPG)
<!-- vim: set tw=120: -->
