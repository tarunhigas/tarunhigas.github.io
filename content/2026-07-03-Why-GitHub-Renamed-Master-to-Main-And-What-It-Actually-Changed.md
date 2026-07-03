Title: Why GitHub Renamed "Master" to "Main" (And What It Actually Changed)
Date: 2026-07-03
Category: programming
Tags:  Github, master, main
Slug:Why-GitHub-Renamed-Master-to-Main-And-What-It-Actually-Changed

If you've opened a new GitHub repository any time in the last few years, you've probably noticed the default branch is called `main`, not `master`. If you've been coding long enough, you might remember when that wasn't the case — `master` was the default for basically the entire history of Git, since 2005.

So what happened? Here's the short story, the longer context, and what it actually means for your day-to-day work.

# **The Trigger: Summer 2020**

The change traces directly back to the summer of 2020, in the middle of the protests and social reckoning following George Floyd's death. Tech terminology that used "master/slave" pairings — a naming convention that had quietly existed in databases, networking hardware, and version control for decades — came under fresh scrutiny.

The Software Freedom Conservancy, which stewards the Git project, put out a statement acknowledging that the term was hurtful to some people. Shortly after, GitHub followed suit, moving away from `master` as the default branch name and encouraging projects to adopt something more neutral.

# **Why "Main" Specifically**

GitHub didn't invent "main" out of nowhere — it emerged as the community favorite among several alternatives (others included `default`, `trunk`, and `primary`). "Main" won out for a few practical reasons:

- **It's short** — same length as `master`, so it doesn't disrupt muscle memory or break scripts that assume short branch names.
- **It's neutral** — no historical baggage.
- **It translates cleanly** — unlike some alternatives, "main" reads clearly across many languages and doesn't carry awkward connotations when translated.

# **How the Rollout Actually Worked**

GitHub didn't force this on every existing repository overnight — that would have broken an enormous number of projects, CI/CD pipelines, and deployment scripts. Instead, the change was staged carefully:

1. **October 1, 2020** — every *newly created* repository on GitHub started defaulting to `main` instead of `master`.
2. **Existing repositories were left untouched.** If your project already had a `master` branch, it stayed that way unless you manually renamed it.
3. **GitHub added tooling** to make renaming easy for maintainers who wanted to switch — including automatic redirects, so old links and `git push` commands pointing to `master` would still resolve correctly for a transition period.
4. **Git itself followed** — starting with Git 2.28, the `init.defaultBranch` config option let anyone set their own default branch name locally, and GitHub Desktop added the same setting.

Other platforms moved in parallel too — GitLab, Bitbucket, and various open-source projects (VS Code among them) made the same switch around the same time, so this wasn't a GitHub-only decision so much as an industry-wide shift.

# **Does It Actually Change Anything Technically?**

Not really — and that's worth emphasizing. There was never anything technically special about a branch named `master` in Git. It was just a convention: the name Git happened to assign to the first branch created in a new repository. You could always rename it, delete it, or use something else entirely. The main practical hiccups came from tooling that had `master` hardcoded — GitHub Pages, for instance, would briefly break on a repo until you told it to serve from `main` instead.

So the change is almost entirely symbolic and cultural, not architectural. That's actually part of the point — it was a low-cost, low-disruption way for the industry to retire language that some found exclusionary.

# **Should You Rename Your Own Old Repos?**

If you've got legacy projects still running on `master`, nothing is forcing you to change them. But if you want to, the process is a handful of Git commands:

    ```bash
    git branch -m master main
    git push -u origin main
    git symbolic-ref refs/remotes/origin/HEAD refs/remotes/origin/main
    ```

Then update the default branch in your repository settings on GitHub, and optionally delete the old `master` branch once you've confirmed everything (CI pipelines, deployment configs, teammates' local clones) points to `main`.

# **The Bigger Picture**

This wasn't an isolated event — it was one small piece of a broader push across the tech industry in 2020 to reconsider language with uncomfortable historical roots (similar conversations happened around terms like "whitelist/blacklist"). Reactions to it were mixed: some developers welcomed the change as an easy, meaningful gesture; others saw it as a symbolic move with limited real-world impact. Both things can be true — it cost the industry very little to make, and for many people, it mattered.

Either way, `main` is now the default everywhere, and for anyone starting a new project today, it's simply the branch name you'll never think twice about.

