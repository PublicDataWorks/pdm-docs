---
layout: default
title: Contributing
parent: Technical Content
---
# Contributor Guidelines
## Our Expectations
We expect that you'll get as much out of the project as you put in. We hope that contributing to this project will be a rewarding experience for all our contributors. We want help with certain cards, but we also want you to experiment. Notice something that can be improved? Feel free to take a stab at it! Additionally, we expect that you'll help us to maintain a positive, collaborative, and welcoming environment here.

General testing practices and expectations:
   * Try and do your work following the TDD model (red -> green -> refactor)
   * Think about edge cases and failure scenarios.
   * Make sure you write unit tests for your work.
   * Run all of the unit tests (server, worker, client, and end-to-end) to verify everything is working before submitting a pull request.
   * Manually test your work to make sure things look good in the browser (i.e. localhost).

## Getting Started
1. [Fork](https://www.google.com/url?q=https%3A%2F%2Fhelp.github.com%2Farticles%2Ffork-a-repo&sa=D&sntz=1&usg=AFQjCNGcCGmleUBtcikSUfSZjgOGmEvt1A) the [repository](https://github.com/PublicDataWorks/police_data_manager) on GitHub 
2. [Clone](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository) the project
3. Follow [README](https://github.com/PublicDataWorks/police_data_manager/blob/master/README.md) on GitHub to set up local machine
4. Begin Contributing!
   
## Submitting Contributions
1. If everything looks good, first update your fork to make sure it’s up to date with the main repo. 
   * Please use rebase instead of merge to keep your commits on top of the work that has already been committed:
     * `git fetch upstream` (make sure you have already set the upstream to [PDM GitHub Repo](https://github.com/PublicDataWorks/police_data_manager))
     * `git rebase upstream/master`
   * When you have a local commit, push it to your fork. 
3. Submit your pull request to the core repo. [How to Create a Pull Request from a Fork](https://www.google.com/url?q=https%3A%2F%2Fhelp.github.com%2Farticles%2Fcreating-a-pull-request-from-a-fork%2F&sa=D&sntz=1&usg=AFQjCNFMmtXYQy-AnDFc-uE1d1_yAouBJQ)
4. A core team dev pair will then review the pull request. The code review itself will be done via GitHub pull request comments. They will let you know what, if anything, needs to be changed before final submission. 
5. Once your code has gone through the code review, the pull request will be approved and your code will be merged into master. At this point, you’re done and ready to contribute some more!   

## Joining the Community 
*  [PDM Google Group]()
*  Provide Helpful [Feedback](https://docs.google.com/forms/d/e/1FAIpQLSesNcMYgQjse5fBK_CKtRwsikOvpqEzHAbctHWkmbsMCxCE1A/viewform)
  
## Helpful Resources 
* [Development Resources](https://publicdataworks.github.io/pdm-docs/technical-context/development-resources.html)
* [FAQs and Troubleshooting](https://publicdataworks.github.io/pdm-docs/technical-context/faq.html)



