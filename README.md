# Symmetry Autumn of Code 2023

**Project**: [Replace libdparse with DMD as a library in dfmt](https://github.com/dlang/project-ideas/issues/103)
<br>
**Mentor**: [Razvan Nitu](https://github.com/RazvanN7)
<br>
**Proposal**: [Prajwal S N - Proposal (SAOC 2023)](/Prajwal%20S%20N%20-%20Proposal%20%28SAOC%202023%29.md)
<br>
**Resume**: [Prajwal S N - Resume](/Prajwal%20S%20N%20-%20Resume.md)

# Overview

[dfmt](https://github.com/dlang-community/dfmt) is a D code formatter. It internally uses [libdparse](https://github.com/dlang-community/libdparse), a third-party D lexer and parser, to obtain a sequence of tokens and an AST, which is then used to generate D code that respects the given guidelines. Since libdparse has a different implementation from the reference D frontend, every time a parser change occurs, libdparse and its dependencies need to be updated. This project aims to replace libdparse in dfmt with the library packages offered by DMD for the lexer and parser, taking the community one step closer to having a single source of truth for the AST across the D compilers and tools ecosystem.

# Contribution

All of the work done is available on the [`saoc-2023`](https://github.com/snprajwal/dfmt/tree/saoc-2023) branch on my fork of dfmt.

# Acknowledgements

I would like to thank my mentor Razvan for his encouragement, insight, and constant support - I'll miss all the cool discussions and jokes in the weekly meetings :'). I extend my gratitude to the D Language Foundation and Symmetry Investments for making it possible to work with this amazing community.
