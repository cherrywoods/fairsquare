# Installing FairSquare in 2024.
This may be incomplete, but there were the steps I had to do to get FairSquare running.
I was installing FairSquare on Ubuntu 22.04.

Install System Dependences
```bash
sudo apt install curl wget make autoconf libtool-bin
```

Install Miniconda (if conda is not already installed)
```bash
mkdir -p ~/.miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/.miniconda3/miniconda.sh
sha256sum ~/.miniconda3/miniconda.sh | grep "c9ae82568e9665b1105117b4b1e499607d2a920f0aea6f94410e417a0eff1b9c"
```
```bash
bash ~/.miniconda3/miniconda.sh -b -u -p ~/.miniconda3
rm -rf ~/.miniconda3/miniconda.sh
~/.miniconda3/bin/conda init bash
```

Create a new conda environment with python 3.6.
```bash
conda create -n fairsquare python=3.6
conda activate fairsquare
pip install six==1.11.0
pip install numpy==1.13.1
pip install scipy==0.18.1 
pip install matplotlib==2.0.2
pip install pandas==0.20.3 
pip install scikit-learn==0.19.1 
pip install z3-solver==4.5.1.0 
pip install codegen==1.0.0 
pip install asteval==0.9.9
```

Download Redlog/REDUCE
```bash
cd fairsquare/src
mkdir tools
cd tools
```

For i386:
```bash
curl -SL https://sourceforge.net/projects/reduce-algebra/files/snapshot_2017-09-22/linux-tar/reduce-complete_2017-09-22_i386.tgz/download | tar -xzC .
```

For amd64:
```bash
curl -SL https://sourceforge.net/projects/reduce-algebra/files/snapshot_2017-09-22/linux-tar/reduce-complete_2017-09-22_amd64.tgz/download | tar -xzC .
```

Link Fairsquare to REDUCE
```bash
ln usr/lib/reduce/cslbuild/csl/reduce .
ln usr/lib/reduce/cslbuild/csl/reduce.img .
```

Test run Fairsquare.
```bash
cd ..  # now in fairsquare/src
python fairProve.py -f ../oopsla/noqual/M_ind_F_NN_V2_H1.fr -mi -mf
```

This may complain about missing libraries (.so files).
I had to install:

- libxrandr
```bash
sudo apt install libxrandr-dev
# And set the LD_LIBRARY_PATH environment variable to include this library
# For me, the library was located in
LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/lib/x86_64-linux-gnu"
```

- libtinfo5
```bash
sudo apt install libncurses5
```

- libffi6 (Ubuntu 22.04 has libffi7)
```bash
curl -LO http://archive.ubuntu.com/ubuntu/pool/main/libf/libffi/libffi6_3.2.1-8_amd64.deb
sudo dpkg -i libffi6_3.2.1-8_amd64.deb
```

---

# ![logo](logo/logo.svg) FairSquare

A tool to verify algorithmic fairness properties, as seen in the OOPSLA 2017
paper **Probabilistic Verification of Program Fairness** by
[Aws Albarghouthi](http://pages.cs.wisc.edu/~aws/),
[Loris D'Antoni](http://pages.cs.wisc.edu/~loris/),
[Samuel Drews](http://pages.cs.wisc.edu/~sdrews/), and
[Aditya Nori](https://www.microsoft.com/en-us/research/people/adityan/).

## Installation

FairSquare runs on Linux and uses Python 3.
Additionally, it uses the following dependencies:
[z3](http://github.com/Z3Prover/z3),
the [SciPy](http://scipy.org/) stack,
and the python packages `codegen` and `asteval`
(easily obtained using [pip](http://pypi.python.org/pypi/pip):
`pip install --user codegen asteval`)

By default, FairSqaure uses
[redlog](http://www.redlog.eu/get-redlog/)
for quantifier elimination, which must be downloaded separately:
FairSquare expects the downloaded files to be placed in `src/tools/`
(specifically, it runs `src/tools/reduce`,
which relies on the existence of `src/tools/reduce.img`).
Alternatively, FairSquare can use a DNF-based quantifier elimination
implemented with z3 by using the `-z` flag when running the tool.

## Running the tool

Once the dependencies are installed, navigate to the `src` directory.
Basic usage of the tool involves a command like

```python fairProve.py -f ex.fr -mi -mf```

`-f ex.fr` specifies that `ex.fr` contains the fairness verification problem to be analyzed
(in fact, `src/ex.fr` contains the illustrative example from the OOPSLA paper),
and `-mi -mf` are optimizations that should always be used.
A full list of command-line flags is available from
`python fairProve.py -h`.

### OOPSLA Benchmarks

You can reproduce our published benchmarks by navigating
to the `oopsla` directory (at the top level).
The benchmarks are divided, based on their fairness postconditions,
into `oopsla/qual` and `oopsla/noqual`.
The `oopsla/run.sh` script can be used to run batches of benchmarks;
see `oopsla/README` for more information.

## Contributors

* [Samuel Drews](http://pages.cs.wisc.edu/~sdrews/) (primary contact)
* [David Merrell](https://dpmerrell.github.io/about/)
* [Aws Albarghouthi](http://pages.cs.wisc.edu/~aws/)
* [Loris D'Antoni](http://pages.cs.wisc.edu/~loris/)
* [Aditya Nori](https://www.microsoft.com/en-us/research/people/adityan/)
