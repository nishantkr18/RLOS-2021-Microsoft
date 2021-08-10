# How to run:
*This page helps in creating and running the tests for this project.*

### Building:
Initially I used Debian(Ubuntu 20.04) for the project, which is easy to work on, given that all the dependencies and build instructions are provided on the vowpal wabbit github wiki.

Later, I shifted to Arch, for which I just had to install the additional `cmake` and `boost` libraries (can be installed using a simple `pacman -S`).

**Tip:** To avoid getting the annoying warning everytime you build VW, use: `cmake ../ -DWARNINGS=OFF`. 
### Benchmark dataset:
All benchmarks are carried out on a repeated sample of the dataset 0001.dat available in the repository tests [here](https://github.com/nishantkr18/vowpal_wabbit/blob/simplified_interaction/test/train-sets/0001.dat).
We have used two variants of the dataset:
- **hundred thousand examples**: to generate, run `for i in {1..500}; do cat ../test/train-sets/0001.dat >> ../../0001_ht.dat; done ` from the build directory.
- **million examples**: to generate, run `for i in {1..5000}; do cat ../test/train-sets/0001.dat >> ../../0001_million.dat; done ` from the build directory.

### Commands used for testing:
A typical command used for testing and benchmarks is:
`time vowpalwabbit/vw ~/i2.dat --num_parse_threads=100 --passes=100 -c -k`

Here I list the purpose of some important flags which you should use for your own tests:
- `-c`: Use cache.
- `-k`: Overwrite cache file.
- `--json`: If you are using JSON input format.
- `--passes`: To indicate number of passes.
- `--holdout_off`: Dont early stop (in the case of multiple passes).
- `--num_parse_threads`: number of parser threads to use. 
- `--no_learner`: Essentially mocks the learner. Skips the all.learn call.
- `--quiet`: No output is printed. Advised to use with `time` to avoid distracting outputs when benchmarking.

Additionally, the `timer_0001.sh` file contains snippets to create benchmarks on the 0001 dataset variants for text, JSON and cache formats, including multiple passes.

### Using callgrind:
The popular profiler Valgrind has this amazing tool called calgrind which helps visualize the total CPU time taken to run parts of the code. Try it out using:
```bash
valgrind --tool=callgrind vowpalwabbit/vw <data file>
kcachegrind <callgrind output file>
```
Note: For better source code tracking, use a debug build.
