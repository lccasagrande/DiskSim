# DiskSim

This repository contains the [DiskSim](https://www.pdl.cmu.edu/DiskSim/index.shtml) storage subsystem simulator with some [modifications](https://github.com/westerndigitalcorporation/DiskSim).

## Requirements

You need to install the cmake, g++5, bison (YACC-compatible parser generator) and flex (fast lexical analyzer generator).

For the Ubuntu distribution:

```bash
sudo apt-get -y install bison flex cmake g++-5
```


## Instructions

1.Clone the repository and navigate to the downloaded folder:

```bash
git clone https://github.com/lccasagrande/DiskSim.git
cd DiskSim
```

2.Build the project:

```bash
mkdir build && cd build && CXX=g++-5 cmake ..
make
```

3.Install the project:

```bash
sudo make install
```

4.Navigate to the examples directory and run an example:

```bash
./runvalid
```
