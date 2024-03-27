**// This file is for PDL internal developers**

# amr-umbrella

Download, build, and install amr and its dependencies in a single highly-automated step.

All pdlfs repositories are mirrored at both github.com and one pdlfs-internal git server running at dev.pdl.cmu.edu.

**The purpose of this guide to show fellow pdlfs internal developers the steps of
properly setting up the repository and syncing the mirrors at both github.com and dev.pdl.cmu.edu.**

## Local repository setup guide

We will use dev.pdl.cmu.edu as our primary git repository, and github.com as our secondary repository.  Once setup, you can push to both repositories using the "git push all" command.

```bash
# First, git-clone from dev.pdl.cmu.edu
git clone git@dev.pdl.cmu.edu:pdlfs/amr-umbrella.git

cd amr-umbrella

# add a push alias "all" that pushes to both dev.pdl and github
git remote add all git@github.com:pdlfs/amr-umbrella.git
git remote set-url --add --push all git@dev.pdl.cmu.edu:pdlfs/amr-umbrella.git
git remote set-url --add --push all git@github.com:pdlfs/amr-umbrella.git

# Verify settings
git remote -v
```
