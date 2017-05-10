# New Endpoint RFCs

Proposals for new endpoints for the REST API require detail about the proposed routes and endpoints.

Each new endpoint represents a new attack surface for malicious actors, signifcant additional considerations for core development, and essentially an infinite support burden, so the need for each endpoint should be considered carefully. Under the motivation section, your proposal should also include detail about why the new endpoints are required, and how the current API is not suitable for the use cases presented.

New endpoints that duplicate existing functionality with different formatting are unlikely to be accepted, even for efficiency reasons.

## Structure

These proposals should follow the following rough structure in the detailed design section of the RFC:

* **Availability**

  Is this a publicly-facing API (for API clients) or an internal API (for the dashboard, customiser, etc)? Which namespace should the resources live under?

  What access controls should apply to the routes? Do external clients need different considerations to internal clients?

* **Resources**

  Each new resource should be detailed, including information on what WordPress core data it models, and the route for it to live at.

  Is the resource a user-facing resource (such as a post or page) or an internal resource (such as a dashboard widget)?

	* **Properties**

	  Each property of the resource should be documented, along with their format and whether the fields are read-only.

	  Consideration should be given to future planning, including how future releases can retrofit new features into properties as needed.

* **Collections**

  Each new collection should be detailed, along with how it relates to the new resources. The collection description should include the route proposed for the collection.

	* **Available queries**

	  Each facet of the collection that can be queried should be documented, as well as how it relates to specific resource fields.

	  Query parameters that match resource properties directly can be summarised rather than detailed.
