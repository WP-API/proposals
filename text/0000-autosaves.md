- Start Date: 2018-04-03
- RFC PR: (leave this empty)
- WordPress Trac Ticket: https://core.trac.wordpress.org/ticket/43316

# Summary

This RFC adds the ability to build autosave-capable editors atop the REST API. This RFC proposes adding a "future revision" concept, which integrates with the current revision interface. This provides a way to autosave a post separately to full, atomic saves.


# Motivation

When users are using a modern editor, they expect to never lose their work in the middle of a thought. Editors implement this in many different ways, including client-side persistence and server-side persistence. Typically, client-side persistence is used for second-to-second updates, while server-side persistence is used for minute-to-minute updates.

Currently in the REST API, the only way to update a post is to send a full update to the post (via a PUT request). This creates a revision on every save, which can be confusing to users. Revisions are intended primarily as a user-facing mechanism to restore content to a previous state, and using this same mechanism for autosaves reveals unnecessary detail about how the editor is saving.

The primary motivation for this feature is Gutenberg, which aims to implement autosave purely via the REST API.


# Detailed design

<!-- This is the bulk of the RFC. Explain the design in enough detail for somebody familiar with the API to understand, and for somebody familiar with the implementation to implement. This should get into specifics and corner-cases, and include examples of how the feature is used. Any new terminology should be defined here. -->

The design extends the existing Revision resource to include autosaves. Autosaves are treated as a special type of revision, indicated by their `revision_type` being set to `autosave`.

The general flow for a client is:

* User opens editor and makes first change, client starts a new autosave revision: `POST /wp/v2/posts/{id}/revisions`
* User edits some text, client updates the autosave revision: `PUT /wp/v2/posts/{id}/revisions/{rev_id}`
* User clicks save, client commits the autosave: `POST /wp/v2/posts/{id}/revisions/{rev_id}`
* If user makes another change, repeat from start

The editor will typically wait until the first change of the post to start the autosave revision.

If the editor is abandoned (for example, the client crashes), the client can retrieve the previous autosave for the user by querying `GET /wp/v2/posts/{id}/revisions?author={user}&revision_type=autosave`.


## Alterations to existing Revision resources

The design adds the following properties to Revision resources, denoted with JSON Schema:

```json
{
	"revision_type": {
		"description": "Type of revision."
		"type": "string",
		"readonly": true,
		"enum": [
			"autosave",
			"revision"
		],
		"context": [
			"view",
			"edit"
		]
	}
}
```


## Alterations to existing Revision endpoints

This design adds the following query parameters to the Revision list endpoint (`GET /wp/v2/posts/{id}/revisions`):

* `author` (integer): Filter to just revisions by this author.
* `revision_type` (string or array, default `revision`): Types of revision to include. By default, only includes full revisions.


## New "Create Revision" endpoint

`POST /wp/v2/posts/{id}/revisions`

Create a new revision.

The request body takes the following properties, denoted in JSON Schema format:

```json
{
	"revision_type": {
		"description": "Type of revision.",
		"type": "string",
		"enum": [
			"autosave"
		],
		"default": "autosave",
		"context": [
			"view",
			"edit"
		]
	},
	"title": {
		"description": "The title for the object.",
		"type": "object",
		"context": [
			"view",
			"edit",
			"embed"
		],
		"properties": {
			"raw": {
				"description": "Title for the object, as it exists in the database.",
				"type": "string",
				"context": [
					"edit"
				]
			},
			"rendered": {
				"description": "HTML title for the object, transformed for display.",
				"type": "string",
				"context": [
					"view",
					"edit",
					"embed"
				],
				"readonly": true
			}
		}
	},
	"content": {
		"description": "The content for the object.",
		"type": "object",
		"context": [
			"view", "edit"
		],
		"properties": {
			"raw": {
				"description": "Content for the object, as it exists in the database.",
				"type": "string",
				"context": [
					"edit"
				]
			},
			"rendered": {
				"description": "HTML content for the object, transformed for display.",
				"type": "string",
				"context": [
					"view",
					"edit"
				],
				"readonly": true
			}
		}
	},
	"excerpt": {
		"description": "The excerpt for the object.",
		"type": "object",
		"context": [
			"view",
			"edit",
			"embed"
		],
		"properties": {
			"raw": {
				"description": "Excerpt for the object, as it exists in the database.",
				"type": "string",
				"context": [
					"edit"
				]
			},
			"rendered": {
				"description": "HTML excerpt for the object, transformed for display.",
				"type": "string",
				"context": [
					"view",
					"edit",
					"embed"
				],
				"readonly": true
			}
		}
	}
}
```

The `title`, `content`, and `excerpt` fields mirror the Post resource's fields. Additional fields may be specified by plugins, such as `meta`.

Only `autosave` revisions can be created externally, as regular revisions are maintained internally by WordPress instead.

This endpoint creates a new autosave revision internally, and returns the representation of that revision. All the usual checks are applied to the autosave revision, including the one-autosave-per-user check.

If an existing autosave for the user already exists, a `409 Conflict` status is returned, indicating that the user already has an autosave. This error should include the ID of the existing autosave revision.


## New "Commit Revision" Endpoint

`POST /wp/v2/posts/{id}/revisions/{rev_id}`

Commits a revision.

This endpoint takes the revision identified by the ID, and updates the post to match that revision. This is used for both "committing" autosave revisions (which turns them into real revisions), as well as restoring old revisions.

This endpoint also deletes the autosave revision on commit, as internally autosave revisions are deleted by WordPress when the post is saved.


# How do we communicate this?

<!--
What names and terminology work best for these concepts and why? How is this idea best presented? As a continuation of existing API patterns, or as a wholly new one?

Would the acceptance of this proposal mean the REST API reference must be re-organized or altered? Does it change how the REST API is taught to new users at any level?

How should this feature be introduced and taught to existing REST API users?

How should the outcome of this proposal be communicated in both the WordPress release notes, and the relevant release's developer field guide?
-->

This should be communicated as part of the 5.0 Developer Field Guide, as well as with additions to the REST API Handbook. The communication should focus on how autosaves conceptually fit into the revision concept, as well as the practical aspects of implementing autosave in editors. It should be noted that autosaves are an opt-in feature, and regular post updates can continue to be used for clients which don't need autosaving.

The communication should also note that this adds the ability to restore revisions.

End user-facing communication is not needed or desired, so does not need to be included in the release notes.


## Field Guide content

> # Autosaving in the REST API in 5.0
>
> WordPress 5.0 introduces autosaves to the WordPress REST API. This has been added to power the autosave feature of Gutenberg to ensure users' work is never lost.
>
> Autosaving is an advanced feature of the REST API, and is opt-in for clients. Existing and new clients can continue to use the existing post update functionality in the REST API. Autosave functionality should generally only be used when making frequent, automatic saves without user interaction.
>
> The primary difference between autosaves and regular post saves is that autosaves represent a "future revision" of the post. Editors create an autosave automatically for users and continually update this to persist changes before the post is actually updated. When the user chooses to actually save their changes, this "future revision" can be "committed"; that is, the staged changes can be applied to the post.
>
>
> ## Typical Autosave Flow
>
> *TODO*: Finish field guide content.


## Handbook content

*TODO*: Write handbook content.


# Drawbacks

<!--
Why should we *not* do this?

Consider the impact on end-users, API consumer developers, WordPress core developers, user complexity, and interaction with other proposals.

There are tradeoffs to choosing any path, please attempt to identify them here.
-->

*TODO*: Discuss drawbacks.


# Alternatives

<!--
What other designs have been considered? What is the impact of not doing this?

This section could also include prior art, that is, how other frameworks in the same domain have solved this problem.
-->

*TODO*: Discuss alternative designs from Trac ticket.


# Unresolved questions

<!--
Optional, but suggested for first drafts. What parts of the design are still TBD?
-->

* Continue to discuss and refine the flow.


# Credits

<!-- Leave this section intact. -->

All text, code, and other contributions related to this proposal are licensed under the [GNU General Public License, v2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html) or later.
