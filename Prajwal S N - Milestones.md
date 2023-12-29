# Milestones

- **Milestone 1: Replace lexer in `tokens.d`**
    - This file deals with token lengths. It also uses mixins to generate the more laborious cases, which will be rewritten for DMD's tokens.
- **Milestone 2: Replace lexer in `wrapping.d` and `indentation.d`**
    - The first file uses the dependency only for the `Token` type, and can be replaced instantly after `tokens.d` is made to work.
    - The second file uses the dependency to identify various boundary tokens and infer the correct indentation level. The token matching will be rewritten to use DMD's tokens over libdparse's.
- **Milestone 3: Replace lexer and parser in `ast_info.d`**
    - This file stores the locations of AST nodes that will be used for formatting. It mostly comprises of visitors for each type of node. The `dparse.ast.ASTVisitor` class from libdparse will be replaced with the `dmd.visitor.Visitor` class from DMD, along with the appropriate changes to each visitor function.
- **Milestone 4: Replace lexer and parser in `formatter.d`**
    - This file mostly uses only the lexer to identify different tokens, and appropriately format the subsequent AST nodes. It uses the parser dependency only to generate the AST. The token matching will be rewritten to use DMD's tokens over libdparse's, similar to `indentation.d`. Finally, the module parsing will be changed to use `dmd.dmodule.Module.parseModule`.
