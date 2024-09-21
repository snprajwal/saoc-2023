# Milestone

The milestone was to complete the `dfmt_template_constraint_style` transformation and implementing the remaining **3** feasible transformations.

# Resolutions and work in progress

The [D forum post](https://forum.dlang.org/post/uvifwbbzggoomllwmftk@forum.dlang.org) requesting community opinion on single vs double indent inconsistency was met with a variety of answers, which made the original situation even more confusing. For now, the decision has been made to implement the two passes *exactly the same way* as dfmt currently does, and later decide whether they should be deprecated or not.

A [pull request](https://github.com/dlang/dmd/pull/15944) has been opened on DMD for storing raw comments with their respective nodes in the AST. There are a few bugs with the implementation that are being resolved - upon merging, this will enable dmdfmt to properly reproduce comments.

# Roadblocks

The D AST is currently highly optimised - so optimised, in fact, that it is impossible to implement certain functionalities of a formatter due to the lack of auxiliary information. Consider the below code snippet:

```d
fn foo() {
    {
        {
            // This is a comment
        }
    }
}
```

A regular parser would generate a function body containing a block, which in turn contains an empty block. This would later be optimised away before code generation. But the D parser optimises this right off the bat and does not store the inner blocks at all! If a function body is empty, then we have no way of deducing whether there were any other empty blocks or comments present, which leads to information being lost while formatting. I do not see any way forward apart from introducing a new pass into the parser to store the rich syntax tree along with all auxiliary information, which can be stripped to generate the current AST. This would not require any of the other compilation phases to be modified - it would simply add an extra pass _before_ the current AST passes.

# Programming work

The following transformations have been implemented:

- `dfmt_template_constraint_style` (_complete_): The two remaining options (`conditional_newline` and `conditional_newline_indent`) from the previous milestone have been implemented, completing the transformation. Both conditional and non-conditional options are now available.
- `dfmt_split_operator_at_line_end`: Places operators at the end of the previous line when split. This is disabled by default.
- `dfmt_single_template_constraint_indent`: Indents the template constraint by one tab instead of two if it is split to a new line. This is disabled by default.
- `dfmt_single_ident` (_in progress_): Indents the code inside parentheses by one tab instead of two. This is disabled by default.

# Commits

- [fix: use temporary buffer for conditional newline](https://github.com/snprajwal/dfmt/commits/90bb91d2c670b05eb9290d9e8ad163f1f59c67b3)
- [chore: revert changes to README](https://github.com/snprajwal/dfmt/commits/6ef9fba442120255f9fd64378405fa96708ff84c)
- [feat: add `dfmt_split_operator_at_line_end`](https://github.com/snprajwal/dfmt/commits/325f093091eae73a51ff3e306ddec0c5e72b01ad)
- [feat: add `dfmt_single_template_constraint_indent`](https://github.com/snprajwal/dfmt/commits/864caada43c4b5379401a1479d12caefa35d5116)
- [fix: boolean logic for line split](https://github.com/snprajwal/dfmt/commits/b845d96ee29e783f6fbc12d3167ae8b81dfff419)

# Weekly forum updates

- [Update #14](https://forum.dlang.org/post/ejfhclgobwmnjkggjvat@forum.dlang.org)
- [Update #15](https://forum.dlang.org/post/bfwowaygbjkmfhqxsprc@forum.dlang.org)
- [Update #16](https://forum.dlang.org/post/qziffwurkxcopdkbntor@forum.dlang.org)
- [Update #17](https://forum.dlang.org/post/usmjoeowgmiexyntaxdp@forum.dlang.org)

# Further work

Support for retaining comments is a work in progress (with a PR up in DMD too), and I'm actively working on identifying and ironing out any other bugs that pop up. Going forward, I'll be working on making dmdfmt production-ready and hopefully have it integrated into the default set of packages for D soon.

---

# The big picture - a summary of SAOC 2023

I won't repeat myself here since the details are all there in the milestone reports themselves. But in brief, here's what I did:

- Studied the DMD library and gained a deep understanding of the language frontend
- Redesigned dfmt to work with the DMD AST
- Implemented a tree-walker that formats D source code using the DMD AST
- Improved parts of DMD itself in the process, and in a way that will benefit any other project that uses the frontend

All in all, I would say we have largely achieved what we set out to do - build a formatter for D that operates on the same AST as the compiler, providing full compatibility with the language syntax and semantics. We just need to polish it a bit before it's good to go for the community to use! :)

# My experience with SAOC

As a compiler nerd, I've always wanted to work on a production compiler, and making that materialise is not as easy as one would think. The large majority of modern compilers are based on LLVM, with a somewhat smaller set using GCC; it is rare to see a language that provides a fully home-cooked compiler from the ground up. Go would be one popular exception, and D is the other. Despite the presence of LDC and GDC, DMD is accepted as the de facto standard compiler for D code. SAOC gave me the chance to work with this, which is simply awesome.

Saying that the D community is *smart* is an understatement. It's only after I began working and regularly visiting the forum that I realised the caliber of the people who work on the language and its ecosystem. While we come across amazing people in all walks of life, the sheer density in the D community initially threw me off - imposter syndrome kicked in pretty hard when I saw the kind of work being done by other folks, and I did wonder about whether I'll be able to carry out my work in a way that fulfills the standards of the community. All of this was put to rest when I began interacting with everyone - they were incredibly welcoming, understanding, and willing to help, despite my unfamiliarity with D in the initial stages. My mentor, Razvan Nitu, took out time from his schedule to regularly have calls with me, some of them extending for hours, where we would discuss the finer details and work out how any roadblocks could be resolved. At no point did I feel helpless or lost, and always got very prompt support with anything I brought up.

It's common knowledge in software development that if you fix a 100 bugs, the 101st one will come around to bite you soon enough. While starting out, I did not expect to hit as many roadblocks as I did, but each one of them taught me a different kind of lesson. Apart from the programming aspect, this journey has also refined my way of approaching and solving problems in general.

Personally, this is a big win for me. While I have gathered considerable experience in general systems across the last few years, a large chunk of it is not directly related to compilers. My SAOC project is compiler-specific work, and has the added benefit of being open-source - if anyone wants to see what kind of work I did, they just need to pull up the PRs from my GitHub profile. This is an excellent launching pad that I can use to convey my domain knowledge and abilities to anyone interested.

A big thank you to everyone involved in SAOC - I had an absolutely fantastic time!
