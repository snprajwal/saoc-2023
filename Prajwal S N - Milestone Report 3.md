# Milestone

The milestone was to implement **7** more transformations using the AST from DMD as a library.

# Roadblocks and solutions

Upon successfully being able to reproduce D source code, we realised that the different types of D comments pose an issue to dfmt. Although the lexer generates comment tokens (`TOK.comment`), the actual comment text is discarded in the lexing stage *unless* it is a DDoc comment. During parsing, the DDoc comments are attached to their relevant top-level nodes. Enabling regular comments to be stored and retrieved during AST traversal can be performed in two ways:

- Introduce a separate field for raw comments in `struct Token` in the lexer, and similarly process the comments into a separate hash table in the parser.
- Introduce a pass before the existing syntactic parsing stage, where a concrete syntax tree (CST) is built, preserving all whitespace, newline, and comment data. In the subsequent pass, strip these nodes from the CST to generate the current AST that DMD creates.

While the second option is a better option to provide a rich syntax tree that can be used by tools like dfmt, the first option was chosen since it is minimally invasive and does not require significant changes in the compiler pipeline. These DMD changes are in progress, and upon completion will enable dfmt to retain, reproduce, and format all types of comments in the source code.

# Programming work

The following transformations have been implemented:

- `dfmt_brace_style` (**large**): Allows configuring brace styles across [Allman](https://en.wikipedia.org/wiki/Indentation_style#Allman_style) (default), [OTBS](https://en.wikipedia.org/wiki/Indentation_style#Variant:_1TBS_%28OTBS%29), [K&R](https://en.wikipedia.org/wiki/Indentation_style#K&R_style), and [Stroustrup](https://en.wikipedia.org/wiki/Indentation_style#Variant:_Stroustrup).
- `dfmt_selective_import_space`: Adds a space between the module name and the colon for selective imports, e.g. `import std.stdio : writeln`. This is enabled by default.
- `dfmt_space_after_keywords`: Adds a space between keywords like `if`, `while`, `for`, `foreach`, `switch`, etc., and the opening parenthesis, e.g. `if (foo == bar)`. This is enabled by default.
- `dfmt_compact_labeled_statements`: Places labels on the same line as the labeled statement, e.g. `foo: while (1) {}`. This is enabled by default.
- `dfmt_space_before_named_arg_colon`: Adds a space between a named function argument or struct constructor argument and the colon, e.g. `foo(a : 0, b : true);`. This is disabled by default.
- `dfmt_template_constraint_style` (_partial_): Allows configuring the formatting of template constraints across `always_newline`, `always_newline_indent`, `conditional_newline`, and `conditional_newline_indent`. Two of the configuration options (`always_newline` and `always_newline_indent`) have been fully implemented, and the remaining two options (`conditional_newline` and `conditional_newline_indent`) will be taken up after implementing the line length configuration options (`max_line_length` and `dfmt_soft_max_line_length`).

# Commits

- [feat: add `dfmt_space_after_keywords`](https://github.com/snprajwal/dfmt/commit/62e47bb5d4cc543de97797403216fe7932008d58)
- [feat: add `dfmt_selective_import_space`](https://github.com/snprajwal/dfmt/commit/b4e36d998b80d07a0ca3fe6bdd4c6f2573bda291)
- [feat: add brace styles](https://github.com/snprajwal/dfmt/commit/07bfe5c2086386de9fd272dc8ac4ba63c45b49af)
- [feat: add `dfmt_compact_labeled_statements`](https://github.com/snprajwal/dfmt/commit/14024a57252afdd7008646c5780421f22ee07bf8)
- [feat: add `dfmt_space_before_named_arg_colon`](https://github.com/snprajwal/dfmt/commit/0259887791a2f0982e416357e4780ba6c98f1a9f)
- [feat: non-conditional template constraints styles](https://github.com/snprajwal/dfmt/commit/0897c83e86c1f21aa842f98eaf9e6a348b7426c4)

# Weekly forum updates

- [Update #10](https://forum.dlang.org/post/sejdjydejcwkmehgilhc@forum.dlang.org)
- [Update #11](https://forum.dlang.org/post/kbrecolcwikdaidvplxl@forum.dlang.org)
- [Update #12](https://forum.dlang.org/post/rdjgfmxepqxlddegihbp@forum.dlang.org)
- [Update #13](https://forum.dlang.org/post/efuodaxkawouggcsnwxu@forum.dlang.org)

# Dropped transformations

The `dfmt_single_template_constraint_indent` and `dfmt_single_indent` transformations both allow configuring whether the code on the following line is indented by a single tab instead of two tabs. They have been removed from the list of remaining transformations for the rewrite, since they introduce formatting inconsistencies in the code, which is a problem dfmt is meant to eliminate. In all scenarios, code on the following line should ideally be indented by a single tab, irrespective of the context of the newline. This ensures formatting consistency across all types of expressions. A D forum post will be created to get some community feedback on the necessity of these two transformations before implementing them.

The `dfmt_outdent_attributes` transform has not been implemented in the existing dfmt project yet, and hence will not be a goal for the rewrite; it will instead be a good-to-have transform, outside of the scope of the next milestone.

# Next milestone

The next milestone involves completing the `dfmt_template_constraint_style` transformation and implementing the remaining **3** feasible transformations. It will also involve research work about the changes required in DMD to support the `dfmt_keep_line_breaks` configuration, which requires newline information to be preserved and available in the AST.
