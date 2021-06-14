# VW Parallel parsing improvements

<p align="center">
<a href="https://www.mlpack.org/"><img style="padding: 20px;" alt="drawing" src="microsoft_research.jpg" height="200"></a>
<a href="https://www.mlpack.org/"><img style="padding: 20px;" alt="drawing" src="VW.png" height="200"></a>
</p>

## Abstract:
Example parsing is an expensive part of VW training pipeline. Support for parallel parsing of text input was contributed last year, but it doesn’t support the more efficient cache format.

- **Goals**: Design and implement an extension to the cache file format to support efficient parallel parsing.

- **Outcomes**: Ability to utilize multicore machines more effectively when training with cache format.

## the Code:
All the work can be found here in the form of pull requests. [link](https://github.com/nishantkr18/vowpal_wabbit/pulls).