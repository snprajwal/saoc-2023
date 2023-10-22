# Milestone

The milestone was to eliminate the lexer dependency from libdparse by replacing `libdparse.lexer` with `dmd.tokens` and `dmd.lexer` across the dfmt codebase.

# Research work

Upon realising that the rewrite would not be a simple one-to-one port, I used the opportunity to explore how dfmt can be designed better with the DMD library. Currently, dfmt roughly works as follows:

- Lex the source code and store the token stream
- Build the AST and walk it, storing location information for different types of nodes
- Traverse the token stream and format it, occasionally using the location information to make decisions

While this works, it requires storing both the token stream and the AST in memory to be able to format the code. I looked into two different approaches:

- dfmt does not perform any semantic analysis or use any of the data that the AST provides. Validating the correctness of the code being formatted (whether it can generate a valid AST or not) is not the responsibility of the formatter. So, we might be able to perform the formatting passes purely using the token stream from the lexer. Upon researching deeper into this approach, I decided to not pursue it due to poor error handling, lack of semantic information, and poor future-proofing in case we want to add context-sensitive format rules going forward.
- By using the AST built with DMD, we can walk the nodes in preorder and format them without having the store the lexed token stream in memory. The location information would also not be required, since the context of a particular node can be identified by climbing up the tree with the node's parents. This is the approach currently being used to implement the formatting passes.

I read a lot of articles and forum posts, but the two most insightful ones are below:

- [Compiling an AST back to source code](https://stackoverflow.com/questions/5832412/compiling-an-ast-back-to-source-code/5834775#5834775)
- [The Hardest Program I’ve Ever Written](https://journal.stuffwithstuff.com/2015/09/08/the-hardest-program-ive-ever-written)

# Programming work

The first step in doing this was to introduce the DMD library as a dependency of dfmt in dub with the [dmd](https://code.dlang.org/packages/dmd) package. Then, the correct compile commands had to be added to the Makefile to compile the necessary library files and expose all the features provided in the library. A number of these features are gated behind the `DMDLIB` version flag, which was identified and added to the compile commands later. Following this, the rewrite mainly involved replacing all instances of `libdparse.lexer.tok` with `dmd.tokens.TOK`, and also required a change in DMD to mark a utility function with `pure @nogc`. Adding these attributes enabled us to retain the existing attributes on most of the functions in dfmt.

# Commits

- [chore(deps): add dmd library](https://github.com/snprajwal/dfmt/commit/a98bdb79c97e5c79b8644156c260be25840838cd)
- [refactor: lexer in `tokens.d`](https://github.com/snprajwal/dfmt/commit/e160202f08d7cd6e9bae4d0e78730d42b64b0a2b)
- [refactor: lexer in `indentation.d` and `wrapping.d`](https://github.com/snprajwal/dfmt/commit/09a580e270e8c2c6cff4fd2757510372018df385)
- [feat: make it compile with dmd AST](https://github.com/snprajwal/dfmt/commit/c083f83378076a6cbb074a050a434f33e77278f3)

# Weekly forum updates

- [Update #1](https://forum.dlang.org/post/suliewxakqlpuiugeuae@forum.dlang.org)
- [Update #2](https://forum.dlang.org/post/hfiwokkbafmzbouhuktf@forum.dlang.org)
- [Update #3](https://forum.dlang.org/post/qnorsogwqbkatbozdnpg@forum.dlang.org)
- [Update #4](https://forum.dlang.org/post/qsucteoapzkxuaffvhsz@forum.dlang.org)

# Next milestone

The next milestone involves two components:

- Eliminate the parser dependency from libdparse and completely move to the DMD library
- Implement 4 transformation passes using the DMD AST
