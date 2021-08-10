<p align="center">
<a href="https://www.microsoft.com/en-us/research/"><img style="padding: 20px;" alt="drawing" src="assets/microsoft_research.jpg" height="200"></a>
<a href="https://vowpalwabbit.org/"><img style="padding: 20px;" alt="drawing" src="assets/VW.png" height="200"></a>
</p>

[Presentation link](https://docs.google.com/presentation/d/1SXanD0kRcpRYMmKSy1oy-NWq81bb_PcuzNlCVO9K8u8/edit?usp=sharing)


## About the program (from website):

The [Reinforcement Learning (RL) Open Source Fest](https://www.microsoft.com/en-us/research/academic-program/rl-open-source-fest/) is a global online program focused on introducing students to open source reinforcement learning programs and software development while working alongside researchers, data scientists, and engineers on the [Real World Reinforcement Learning](https://www.microsoft.com/en-us/research/project/real-world-reinforcement-learning/) team at Microsoft Research NYC. Students will work on a four-month research programming project during their break from university (May-August 2021). Accepted students will receive a $10,000 USD stipend.

Our goal is to bring together a diverse group of students from around the world to collectively solve open source reinforcement learning problems and advance the state-of-the-art research and development alongside the RL community while providing open source code written and released to benefit all.

At the end of the program, students will present each of their projects to the Microsoft Research Real World Reinforcement Learning team online.

## Project Abstract (proposed):
Vowpal Wabbit is known for its blazing-fast performance. However, VW's parsers can be a bottleneck for most operations, so an effective way to multithread the parsers is required to unleash their true potential. Last year, parallel parsing support for text input format was provided. This project builds upon that by providing a better and more efficient way to read and write cache, support for multiple passes, multiline examples, and JSON/DsJSON input formats.

- **Goal**: Design and implement an extension to the cache file format to support efficient parallel parsing.

- **Outcome**: Ability to utilize multicore machines more effectively when training with cache format.

## Pages:
* [How to run](htr/htr.md)
* [Previous work](prev_work/prev_work.md)
* Major changes made:
    * [Relocations](changes/changes.md)
    * [Cache](cache/cache.md)
    * [JSON](pages/json.md)
* [Blog](pages/blog.md)

## Code:
All the work can be found here in the form of pull requests. [link](https://github.com/nishantkr18/vowpal_wabbit/pulls).

Work on Parallel parsing was performed last year by Cassandra. My work this year builds upon the previous work. So, the updated branch (containing all the work done last year, and updated with the master branch(just compiles)) is the [`multithread_parser_with_passes`](https://github.com/nishantkr18/vowpal_wabbit/tree/multithread_parser_with_passes). Hence, I've created pull requests against that branch for easy comparisions.

Currently, there are three active branches in [`nishantkr18/vowpal_wabbit`](https://github.com/nishantkr18/vowpal_wabbit):
- `multithread_parser_with_passes`: contains work from last year. All new changes compared against this.
- `simplified_interaction`: contains all the work done this year for multithreading.
*(The name "simplified interaction" comes from the most efficient plan we could think of for supporting multiple passes, after several complex ideas)*
- `master-benchmark` : contains an extra timer script for creating benchmarks for the master branch.

Inactive branches (not updated):
- `cache_reader_working`: modifies the cache reader functions to read from a character` pointer directly, therefore doesn’t require any dummy io_buf instance.

The rest of the branches contain implementation of ideas that were eventually dropped.

## Lessons learnt:
- There can be huge performance differences between the debug and release build. So, we make sure to run all benchmarks on the release build.
- Using a tool like valgrind slows down the program a lot, so the CPU waiting time for lock contentions cannot be studied properly.

## Resources:
These resources could be helpful for getting started with multithreading in c++.

- [link 1](https://youtube.com/playlist?list=PLk6CEY9XxSIAeK-EAh3hB4fgNvYkYmghp)
- [link 2](https://www.youtube.com/playlist?list=PL1835A90FC78FF8BE)

## Contribute?
Please feel free to contribute by making pull requests or opening issues on the [repo](https://github.com/nishantkr18/vowpal_wabbit/). I would also love to hear your thoughts about this project. Please use any form of contact mentioned on my website or github bio.