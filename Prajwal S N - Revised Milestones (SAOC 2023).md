# Rationale

Upon discussion with my mentor (Razvan Nitu), we have both come to the conclusion that the current monolithic architecture of dfmt makes it difficult to test, and cumbersome to refactor while ensuring that it works as intended. So far, the lexer dependency has been replaced through a 1-to-1 port from libdparse to dmd. While this is fine, it is considerably harder to do this with the parser. Without being able to verify if the AST is being built successfully, it is not possible to rewrite the transformation passes performed by dfmt and guarantee that they will work. If, at the end of doing a brute force port, dfmt is buggy, broken, or refuses to compile, it could very well mean that all the effort went down the drain.

A smarter approach to solving this problem would be to eliminate the existing transformation passes that use libdparse, and start with a clean slate. First, _just_ the AST will be built, and verified to be correct. Then, the transformations will be added incrementally, along with appropriate unit tests for each transformation to ensure that it works as intended and nothing breaks. This approach contains the added benefit that dfmt will be in a working state at all points during the rewrite, albeit with fewer transformations. Thus, if someone else wishes to contribute to the project and rewrite a transformation, they would be able to do so with a working, testable application.

> **NOTE**: dfmt currently provides 18 transformations. These have been split into 4+7+7 across the milestones. Upon writing one transformation correctly and making it work, porting the rest of them should be considerably easier.

# Milestones

- **Milestone 1: Replace `libdparse.lexer` with `dmd.tokens` and `dmd.lexer`**
	- `tokens.d` deals with token lengths. It also uses mixins to generate the cases for keywords, which can be eliminated and replaced with `dmd.tokens.Token.isKeyword()`.
	- libdparse uses `libdparse.lexer.IdType` to represent token types, and also provides the `libdparse.lexer.tok` template to construct `IdType` using a string. The equivalent for this type would be `dmd.tokens.TOK`.
	- The rewrite mainly involves replacing all instances of `libdparse.lexer.tok` with `dmd.tokens.TOK`. Since the former is a template and the latter an enum, care needs to be taken to ensure that the correct variants are being used in the replacement.
	- `wrapping.d` contains only one instance of `libdparse.lexer.tok`, which can be replaced instantly with `dmd.tokens.TOK` after `tokens.d` is made to work.
	- `indentation.d` uses the dependency to identify various boundary tokens and infer the correct indentation level. The rewrite will be similar to `tokens.d`, where every instance of `libdparse.lexer.tok` will have to be mapped to its respective `dmd.tokens.TOK` member and replaced.
	- `formatter.d` uses the lexer to identify different tokens, and appropriately format the subsequent AST nodes. The token matching will be rewritten to use `dmd.tokens.TOK` over `libdparse.lexer.tok`, similar to `tokens.d`, `indentation.d`.
- **Milestone 2: Replace parser in `ast_info.d` and `formatter.d`, with 4 transformations**
	- The first file stores the locations of AST nodes that will be used for formatting. It largely comprises visitors for each type of node. The `dparse.ast.ASTVisitor` class from libdparse will be replaced with the `dmd.visitor.Visitor` class from DMD, along with the appropriate changes to each visitor function.
	- The second file is the driver, and uses the parser dependency to generate the AST and invoke the transformations. The module parsing will be changed to use `dmd.dmodule.Module.parseModule`.
	- Upon successfully generating the AST with DMD, a minimum of 4 transformations will be rewritten to use the new AST and work as expected. Unit tests will also be added to ensure that the functionality is consistent.
- **Milestone 3: Rewrite 7 more transformations**
	- 7 more transformations will be rewritten with the new AST and unit tests, similar to the previous 4.
- **Milestone 4: Replace the remaining 7 transformations**
	- The last 7 transformations will be rewritten, thus reaching feature parity with the older dfmt implementation. This will complete the port to DMD, ensure behavioural consistency through tests, and fully eliminate the dependency on libdparse.
