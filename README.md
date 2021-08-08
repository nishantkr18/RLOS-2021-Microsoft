<p align="center">
<a href="https://www.microsoft.com/en-us/research/"><img style="padding: 20px;" alt="drawing" src="assets/microsoft_research.jpg" height="200"></a>
<a href="https://vowpalwabbit.org/"><img style="padding: 20px;" alt="drawing" src="assets/VW.png" height="200"></a>
</p>


## About the program (from website):

The [Reinforcement Learning (RL) Open Source Fest](https://www.microsoft.com/en-us/research/academic-program/rl-open-source-fest/) is a global online program focused on introducing students to open source reinforcement learning programs and software development while working alongside researchers, data scientists, and engineers on the [Real World Reinforcement Learning](https://www.microsoft.com/en-us/research/project/real-world-reinforcement-learning/) team at Microsoft Research NYC. Students will work on a four-month research programming project during their break from university (May-August 2021). Accepted students will receive a $10,000 USD stipend.

Our goal is to bring together a diverse group of students from around the world to collectively solve open source reinforcement learning problems and advance the state-of-the-art research and development alongside the RL community while providing open source code written and released to benefit all.

At the end of the program, students will present each of their projects to the Microsoft Research Real World Reinforcement Learning team online.

## Project Abstract:
Example parsing is an expensive part of VW training pipeline. Support for parallel parsing of text input was contributed last year, but it doesn’t support the more efficient cache format.

- **Goals**: Design and implement an extension to the cache file format to support efficient parallel parsing.

- **Outcomes**: Ability to utilize multicore machines more effectively when training with cache format.

## Code:
All the work can be found here in the form of pull requests. [link](https://github.com/nishantkr18/vowpal_wabbit/pulls).

Work on Parallel parsing was performed last year by Cassandra. My work this year builds upon the previous work. So, the updated branch (containing all the work done last year, and updated with the master branch(just compiles)) is the [`multithread_parser_with_passes`](https://github.com/nishantkr18/vowpal_wabbit/tree/multithread_parser_with_passes). Hence, I've created pull requests against that branch for easy comparisions.

Currently, there are three active branches in [`nishantkr18/vowpal_wabbit`](https://github.com/nishantkr18/vowpal_wabbit):
- `multithread_parser_with_passes`: contains work from last year. All new changes compared against this.
- `simplified_interaction`: contains all the work done this year for multithreading.
*(The name "simplified interaction" comes from the most efficient plan we could think of for supporting multiple passes, after several complex ideas)*
- `master-benchmark` : contains an extra timer script for creating benchmarks for the master branch.

Inactive branches (not updated):
- `cache_reader_working`: modifies the cache reader functions to read from a character pointer directly, therefore doesn’t require any dummy io_buf instance.

The rest of the branches contain implementation of ideas that were eventually dropped.

## How to run:

### Building:
Initially I used Debian(Ubuntu 20.04) for the project, which is easy to work on, given that all the dependencies and build instructions are provided on the vowpal wabbit github wiki.

Later, I shifted to Arch, for which I just had to install the additional `cmake` and `boost` libraries (can be installed using a simple `pacman -S`).

**Tip:** To avoid getting the annoying warning everytime you build VW, use: `cmake ../ -DWARNINGS=OFF`. 
### Benchmark dataset:
All benchmarks are carried out on a repeated sample of the dataset 0001.dat available in the repository tests [here](https://github.com/nishantkr18/vowpal_wabbit/blob/simplified_interaction/test/train-sets/0001.dat).
We have used two variants of the dataset:
- **hundred thousand examples**: to generate, run `for i in {1..500}; do cat ../test/train-sets/0001.dat >> ../../0001_ht.dat; done `
- **million examples**: to generate, run `for i in {1..5000}; do cat ../test/train-sets/0001.dat >> ../../0001_million.dat; done `

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

## Lessons learnt:
- There can be huge performance differences between the debug and release build. So, we make sure to run all benchmarks on the release build.
- Using a tool like valgrind slows down the program a lot, so the CPU waiting time for lock contentions cannot be studied properly.

## Resources:
These resources could be helpful for getting started with multithreading in c++.

- [link 1](https://youtube.com/playlist?list=PLk6CEY9XxSIAeK-EAh3hB4fgNvYkYmghp)
- [link 2](https://www.youtube.com/playlist?list=PL1835A90FC78FF8BE)

## Contribute?
Please feel free to contribute by making pull requests or opening issues on the [repo](https://github.com/nishantkr18/vowpal_wabbit/). I would also love to hear your thoughts about this project. Please use any form of contact mentioned on my website or github bio.