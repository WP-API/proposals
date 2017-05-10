- Start Date: (fill me in with today's date, YYYY-MM-DD)
- RFC PR: (leave this empty)
- WordPress Trac Ticket: (leave this empty)

# Summary

Introduce a `dashboard` namespace prefix for endpoints specifically for the WordPress Dashboard.


# Motivation

WordPress core currently contains two namespace prefixes: `wp` and `oembed`. These represent two separate APIs offered by WordPress: the general REST API, and the OEmbed implementation.

The `wp` general API represents the primary external API offered by WordPress to API clients.

The general design of WordPress is as two distinct parts: the core, and the Dashboard "application" built on top of it. Traditionally, these two have not been formally separated, however the design and clean slate of the REST API allows us to consider these separately as we evolve the Dashboard.

With the push towards removing admin-ajax handlers in favour of new REST API endpoints, there are naturally API endpoints which are not useful outside of the Dashboard. Endpoints for Dashboard Widgets, pointers, and other features are specific to the Dashboard application, and are not broadly applicable to WordPress.

Introducing a new `dashboard` namespace prefix allows separating Dashboard-specific endpoints from the public WordPress REST API. This can also allow for future changes, deprecations, and new versions of the Dashboard API without requiring a version bump for the public REST API.


# Detailed design

New endpoints for the Dashboard will live under `dashboard/`, initially with the namespace `dashboard/v1` for the first version of endpoints.

Future iterations of the Dashboard that need to break API backwards compatibility can increment the version under the same namespace prefix as needed (e.g. `dashboard/v2`, `dashboard/v3`). These new versions do not need to affect the public WordPress REST API.

This requires no code changes, as it simply designates the namespace for future use. The first endpoints to be added under this new namespace are expected to be the endpoints for the Nearby Events feature in WordPress 4.8.


# How do we communicate this?

The new endpoints under the `dashboard` namespace prefix will collectively be called the Dashboard REST API to distinguish them from the public WordPress REST API.

There are no user-facing changes resulting from this proposal. Likewise, there is no effect on plugin, theme, or API client developers.

The difference between `wp` and `dashboard` needs to be communicated to core developers working on the Dashboard and related features. The announcement of this RFC on the Make/Core blog will serve as an initial announcement, but evergreen documentation should be added to the Core Handbook.

A new page will be added to the Core Handbook under the "Best Practices" section, outlining design guidelines for new endpoints added to WordPress Core. The proposed text for this page is:

> # REST API
>
> Core usage of the REST API should follow some simple rules to ensure WordPress provides a consistent public API.
>
> ## Creating New Endpoints
>
> Where possible, core features should use existing REST API endpoints rather than adding new ones. For example, a tag search feature should use the existing tag collection endpoint in the REST API rather than building one specifically for the feature.
>
> When a new endpoint is required, care should be put into the design of the external API. In particular, the shape and schema of the resource, the namespace, the route (URL), and the available actions should match existing precedent in core.
>
> REST API endpoints intended for public use and which represent "core" features of WordPress should be under the public REST API namespace (`wp/v2`). Endpoints which are only applicable for the Dashboard (e.g. Dashboard Widgets, pointers, or other UI settings) should be under the Dashboard REST API namespace (`dashboard/v1`). This ensures a clear distinction between APIs for general consumption and APIs built for specific UI features.
>
> ## Review
>
> The design and implementation of the endpoint should be checked by the REST API team before committing to ensure it's consistent with the rest of the API. This helps us provide a better experience for API users.
>
> To request design feedback, you create a [new proposal](https://github.com/WP-API/proposals). This allows the team to give structured feedback and discussion, and ensure consistency across the API. This is for design feedback only; implementation feedback should still take place on Trac as with any other code.
>


# Drawbacks

This requires reserving a new namespace which might currently be in use by plugins or other code. This represents a minor backwards compatibility break, and investigation needs to be done as to whether we're breaking any major plugins with this change.

This also fragments core's API, as it introduces an entirely new namespace. Drawing a line of which namespace to use is tough, and this might lead to features only existing as part of the Dashboard API, without a public API for use by external clients.

In addition, it creates the question of whether more namespaces are needed. For example, should there be a Customizer namespace? Do other features need their own namespaces as well?


# Alternatives

There are two main alternatives to creating a `dashboard` namespace prefix.

The first is to use the current `wp/v2` namespace. The main reason to avoid doing this is that it couples Dashboard-specific features to the public REST API. These features are rarely applicable or usable outside of the Dashboard application, especially in third-party/external clients. In addition, this would also tie Dashboard API versioning to the public REST API, which could lead to the public REST API version being needlessly bumped for Dashboard-only changes.

The other alternative is to use a sub-namespace prefix of the existing `wp` prefix, such as `wp/dashboard`. This avoids needing to break compatibility, as `wp` is already reserved for core. However, this increases the mental complexity of the API, and breaks the current precedent of `<prefix>/v<version>`. Given that it is unlikely that the `dashboard` prefix is currently being used, the simpler option is the better one. This also discourages a proliferation of sub-namespaces under the `wp` prefix.


# Unresolved questions

* Should we use `dashboard/v1` or `wp/dashboard/v1`? The former could potentially clash with plugins, and is much more generic. We also need to pave the way for further namespaces that may be added to core in the future, where clashes may be more common.


# Credits

<!-- Leave this section intact. -->

All text, code, and other contributions related to this proposal are licensed under the [GNU General Public License, v2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html) or later.
