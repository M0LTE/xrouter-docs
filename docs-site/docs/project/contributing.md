# Contributing

This site is community-maintained, and corrections and improvements are welcome. It is a
small MkDocs Material project, so contributing is mostly a matter of editing Markdown and
opening a pull request. This page covers the practical steps and the one policy that matters
most.

## How to make a change

1. **Find the page.** Every page is a Markdown file under `docs/`, laid out to match the
   site navigation (for example this page is `docs/project/contributing.md`). Edit the
   Markdown directly.
2. **Build it locally with strict mode** so broken links and bad references fail the build
   rather than slipping through:

    ```bash
    pip install -r requirements.txt   # first time only
    mkdocs build --strict
    ```

    `--strict` turns warnings such as a dead cross-link into errors. To preview as you write,
    use `mkdocs serve` and open the local URL it prints.

3. **Open a pull request** against the documentation repository with a clear description of
   what you changed and, where it matters, the source you checked it against.

If you would rather not use Git,
[open an issue](https://github.com/m0lte/xrouter-docs/issues) describing the correction and
someone can fold it in. Issues on this repository are the only place to report documentation
problems. Please do not take them to the XRouter support group, which is for the program
itself.

## The correction policy

There is one rule that overrides the rest:

!!! warning "The author wins"
    Where this site disagrees with **Paula G8PZT** or with the **in-program manual**, the
    author and the manual are correct. The right fix is to **change the doc to match them**,
    not to argue the point in the text. This site is a re-presentation; it has no authority
    of its own. If you are unsure which is right, check the `MAN` command in a current build,
    or ask the author and other sysops on the support group.

When you correct a fact, it helps to cite the source you used, such as the in-program manual,
a groups.io message, or one of the references in
[Sources and attribution](sources-and-attribution.md), so the next person can see why the
page says what it says.

## House style

Keep to the existing style so the site stays consistent:

- **Original prose.** Do not paste text from the manual or any mirror. Write it in your own
  words, faithful to how XRouter behaves.
- **Be accurate, not impressive.** Never invent keyword names, default values, port numbers
  or version strings. If a value varies by build or you cannot confirm it, say so or leave it
  out. A gap is better than a confident error.
- **Lead with what the page is for**, write in calm second-person prose, and prefer short
  paragraphs with tables for reference data and fenced blocks for config and commands.
  British spelling is fine.
- **Cross-link** generously to related pages with relative links, and add a **Sources** line
  where a page makes specific factual claims.
- **Mind the scope.** This site documents XRouter generically. It does not describe any
  particular club or individual node deployment, and it does not publish security findings.
  General hardening guidance is welcome.

## Discuss it first if it is large

For anything bigger than a fix, such as restructuring a section, adding new pages, or
changing the navigation, it is worth
[opening an issue](https://github.com/m0lte/xrouter-docs/issues) to float the idea before you
do the work, so the approach can be agreed and effort is not wasted.

---

**Sources:** [About these docs](about-these-docs.md) ·
[Sources and attribution](sources-and-attribution.md)
