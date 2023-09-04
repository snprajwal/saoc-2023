# Replacing libdparse with DMD library in dfmt

#### Mentor: [Razvan Nitu](mailto:razvan.nitu1305@gmail.com)

## Table of Contents

1. **[Abstract](#1-abstract)**
2. **[Why this project?](#2-why-this-project)**
3. **[Technical details](#3-technical-details)**
	3.1. [Replacing the lexer](#31-replacing-the-lexer)
	3.2. [Replacing the parser](#32-replacing-the-parser)
4. **[Timeline](#4-timeline)**

## 1. Abstract

[dfmt](https://github.com/dlang-community/dfmt) is a D code formatter. It internally uses [libdparse](https://github.com/dlang-community/libdparse), a third-party D lexer and parser, to obtain a sequence of tokens and an AST, which is then used to generate D code that respects the given guidelines. Since libdparse has a different implementation from the reference D frontend, every time a parser change occurs, libdparse and its dependencies need to be updated. This project aims to replace libdparse in dfmt with the library packages offered by DMD for the lexer and parser, taking the community one step closer to having a single source of truth for the AST across the D compilers and tools ecosystem.

## 2. Why this project?

A formatter is crucial to ensure cohesiveness and readability across a codebase, especially for large projects. By using DMD's library packages with dfmt, the burden of regularly updating a third-party library for the lexer and parser is eliminated. Subtle bugs and implementation details will no longer introduce differences between the language specification and dfmt, as it will use the same AST that is generated and used for compilation. This will significantly reduce inconsistency and duplicated effort across the project, and benefit the community by saving valuable developer hours that can be invested in more important work.

## 3. Technical details

The dfmt codebase currently imports the libdparse library across five different files:

- `src/dfmt/tokens.d`
- `src/dfmt/wrapping.d`
- `src/dfmt/indentation.d`
- `src/dfmt/ast_info.d`
- `src/dfmt/formatter.d`

Of these files, the first three use only the lexer, while the other two use both the lexer and the parser. A phased approach will be used to replace libdparse with the DMD library, first replacing the lexer dependency, and subsequently the parser. The aim is to ensure that dfmt's functionality remains intact while transitioning to a unified source of truth for the AST.

### 3.1 Replacing the lexer

The `tokens.d`, `wrapping.d`, and `indentation.d` files do not use the parser, and exclusively depend on the lexer. Hence, these are the easiest to rewrite. The approach is straightforward:

- **Import replacement**: Update the imports, replacing libdparse with the DMD lexer library
- **Codebase update**: Update the codebase to utilize the DMD lexer functions
- **API update**: If necessary, update DMD to expose any information that is not already available through the library API
- **Behaviour alignment**: Refactor any parts of the code that rely on libdparse-specific lexing behaviour

### 3.2 Replacing the parser

The `ast_info.d` and `formatter.d` files make use of both the lexer and parser. Since the structs and classes used by libdparse and DMD are different, both the lexer and parser will have to be replaced together. The DMD library conveniently exposes and provides different visitors to implement the same features provided by libdparse.
The approach is identical to the previous section.

## 4. Timeline

The project can be fully completed within the duration of the program, with an estimated 20-25 hours a week of work.

- **Before Sep 15**: Explore the DMD lexer and parser to understand their functionalities, API, and integration process with dfmt.
- **Sep 15 - Oct 14 (Phase I)**: Begin replacing the lexer dependency in `tokens.d`, and make the necessary changes across the rest of the codebase.
- **Oct 15 - Nov 14 (Phase II)**: Continue the lexer replacement in `wrapping.d` and `indentation.d`, and verify that the functionality is identical to the libdparse implementation.
- **Nov 15 - Dec 14 (Phase III)**: Start the replacement of the lexer and parser in `ast_info.d`. Integrate the DMD parser for generating the AST.
- **Dec 15 - Jan 14 (Phase IV)**: Complete the parser replacement in `formatter.d`, and verify that the functionality is identical to the libdparse implementation.

The last week of Phase IV (Jan 7 - Jan 14) is reserved as a buffer to accommodate any delays or unexpected emergencies.
