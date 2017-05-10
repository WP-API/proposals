# WordPress REST API Proposals

**We're currently trialing this process, stay tuned for further updates.**

Welcome to the WordPress REST API proposals repo! This repo tracks proposals for substantial changes to the API.

While most changes can simply be handled through patches, others need discussion and refinement in a consistent design process. This includes substantial changes to existing endpoints (including changes which would necessitate a version change), introduction of new endpoints, reservation of namespaces, or refactoring.

Proposals are drafted here and discussed with the core API team and community, then posted to the [Make/Core blog] for comments once completed.

This allows discussion and iteration on ideas and features early in the development cycle, as well as a consistent design across the entire API.

----

**[Current Proposals](https://github.com/WP-API/proposals/pulls)**

**[Accepted Proposals](https://github.com/WP-API/proposals/tree/master/text)**

----

## When should I write a proposal?

Proposals should be written for substantial changes to the API. Whether a change is substantial is subjective, but may include:

* Adding new endpoints
* Adding or reserving new namespaces
* Introducing new endpoint versions
* Modifying endpoints (including adding properties to existing resources)
* Major refactoring
* Introduction of new best practices or conventions

Most minor changes can use the regular Trac-based patching process. This includes:

* Bug fixes, where they don't affect end users in a backwards-incompatible way
* Minor refactoring
* Additional actions or filters

As a general rule, changes which should appear on the [Make/Core blog] (such as in a release field guide or dev note) should use the proposal process. If in doubt, ask!


### Specific guidelines

Some proposals require a more specific process or detail:

* [New endpoints](new-endpoints.md)


## Before creating a proposal

Proposal documents should be focussed on a single key proposal or new idea.

It's often helpful to get feedback on your concept before diving into the level of API design detail required for an proposal. Feel free to discuss ideas before writing anything up, either on the [issue tracker] or in the #core-restapi room on [WordPress Slack](https://chat.wordpress.org/).

Designing and implementing a consistent user-facing API sometimes requires making hard decisions. Per [the WordPress philosophy](https://wordpress.org/about/philosophy/), we may have to reject desired improvements if they don't fit the overall goals for the API or WordPress generally. The ultimate decision on a proposal rests with the REST API focus leads.


## Proposing an idea

After you've validated your idea, it's time to start writing a proposal document. The proposal process is handled via pull requests to this repo, and merged once the proposal is "accepted".

Here's how proposing a document works:

- **Fork** this repo

- Copy `0000-template.md` to `text/0000-my-feature.md` (where "my-feature" is descriptive, don't assign a number yet).

- **Write** the proposal. Put care into the details, and take care to consider all aspects of the proposal.

- **Submit** a pull request. Be prepared to make changes based on community feedback.

- **Discuss** the proposal. A great time to discuss it is at the weekly API team meeting or office hours. (Offline discussion (e.g. Slack) should be summarized on the pull request comment thread.)

- **Refine** and edit to clarify or change the design. Changes should be made as new commits to the pull request, with comments to explain your changes. **Do not squash or rebase commits** after they are visible on the pull request.

- At some point, a member of the core API team will begin the "final comment period", and **post** the final proposal to the [Make/Core blog].

	- Long discussions should have a summary comment to lay out the discussion and points of disagreement.

	- The comment period lasts a week to allow for final discussion.

- Comments from the community will be considered, and the focus leads will make a final decision to merge or close. If substantial new arguments or ideas are raised, the comment period may be cancelled, and the proposal sent back into development mode.

- If accepted, a core API team member will **accept and merge** the pull request. The team member will additionally create a core Trac ticket to track implementation progress, and update the document with the link.


## Accepted Proposals

Once a proposal is accepted, the implementation can be started with patches submitted to WordPress Trac. Being "accepted" is not a rubber stamp, and doesn't mean the feature will ultimately be merged; it only means the team has agreed to the feature and are amenable to merging it. It also doesn't affect the regular development process or release cycle.

Modifications to "accepted" proposals can be done in follow-up pull requests, but shouldn't substantially change the proposal. Substantial changes should be new proposals, with a note added to the original.

Every accepted proposal has an associated issue tracking its implementation on WordPress Trac; thus that associated ticket can be assigned a priority via the regular WordPress triage and development process.

The proposal can be implemented by anyone, not just the proposal's author. If you're interested in working on the implementation, feel free to ask (e.g. by leaving a comment on the associated ticket).


## Thanks

Thanks to the [Rust RFC process] and [Ember RFC process] for inspiring this process, and providing substantial process documentation. Thanks also to the [Python Enhancement Process (PEP)](https://www.python.org/dev/peps/pep-0001/) for further inspiration. Substantial time and effort has gone into making and refining these processes, and we're grateful to be able to build atop their work.

[Rust RFC process]: https://github.com/rust-lang/rfcs
[Ember RFC process]: https://github.com/emberjs/rfcs
[issue tracker]: https://github.com/WP-API/rfcs/issues
[Make/Core blog]: https://make.wordpress.org/core/
