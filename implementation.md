ActivityPump
============

Implementation overview
-----------------------

1.  Post to outbox, expose outbox (client to server, "Social API")
2.  Discover inboxes of and post to anyone explicitly targeted (server
    to server, "Federation API")
3.  Have a discoverable inbox that people can post to *(Issue: What's the AP/pump
    approach to spam?)* (server to server, "Federation API")
4.  Side effects: update data on the server in response to certain
    activities (posted to your outbox by you or inbox by others).
5.  Access control: allow authentication (by clients and/or servers?) to
    retrieve certain posts

### Note: Comparison with indieweb

1.  Micropub endpoint + h-feed
2.  Discover webmention endpoint and send webmentions
3.  Have a discoverable webmention endpoint that people can post
    webmentions to
4.  *Side effects: no spec'd indieweb equivalent*
5.  Access control: allow people to sign into your site with RelMeAuth

Implementation breakdown
------------------------

### Outbox

1.  Your outbox endpoint is discoverable from your homepage or profile.
2.  Your outbox endpoint receives authenticated HTTP POST requests
    containing an ActivityStreams activity (or content-object?) with
    Content-Type of `application/activity+json`.
3.  If other objects are mentioned (eg. in `object`, `target` or
    `inReplyTo`) the new activity (or content-object?) is posted to the
    mentioned object(s)'s actor's inbox endpoint (or to the mentioned
    object directly?)
4.  Your outbox endpoint responds to HTTP GET requests with all
    activities as JSON in reverse-chronological order.
5.  GET requests to individual object URIs should return the object
    properties as JSON.

### Discover and post to inboxes

1.  When your outbox receives an activity (or content-object?) with an
    audience specified in `cc` or `bcc` properties, your server
    dereferences these URLs (continuing until URIs for users (Actors)
    are found in the case of groups/collections) and discovers their
    inbox endpoints.
2.  Your server posts the (single) activity (or content-object?) to all
    discovered inbox endpoints, with your authentication, and with
    Content-Type `application/activity+json`.

### Inbox for receiving

1.  Your inbox endpoint is discoverable from your homepage or profile.
2.  Your inbox endpoint receives HTTP POST requests containing an
    ActivityStreams activity (or content-object?).
3.  Your server dereferences the object URI and checks if the object is valid.
4.  Your server checks to see if it has received this object before and
    it hasn't changed, and discards if so.
5.  Your server checks the authentication matches the `actor` of the
    activity, and discards if not.
6.  If a received object mentions one of your objects (eg. in `object`,
    `target` or `inReplyTo`), your server posts the new object to all
    recipients of your original object.
7.  Your inbox endpoint responds to authenticated HTTP GET requests with
    all activities as JSON in reverse-chronological order (usually
    authentication would mean only the inbox owner can see the
    contents).

### Side effects

1.  When your outbox endpoint receives a `Create` activity, an object is
    created.
2.  When your outbox endpoint receives an `Update` activity, an existing
    object is updated.
3.  When your outbox endpoint receives a `Delete` activity, an existing
    object is replaced with a tombstone, and requests to the object URI
    return a 410.
4.  When your outbox endpoint receives a `Follow` activity, your server
    adds the object user to your 'follows' collection.
5.  When your inbox endpoint receives a `Follow` activity with you as
    the object, your server adds the actor to your 'followers'
    collection (as such, they'll receive anything you post to your
    followers).
6.  When your outbox endpoint receives a `Like` activity, your server
    adds the object to a collection of things you've liked.
7.  When your inbox endpoint receives a `Like` activity, your server
    adds the actor to a collection of actors who have liked the object.
8.  When your outbox endpoint receives a `Add` activity, your server
    adds the object to the collection in the `target`.
9.  When your outbox endpoint receives a `Block` activity, your server
    ignores any activities from the object user.
10. When your outbox or inbox endpoint receives an `Undo` activity, your
    server reverses any side-effects caused by the object activity.

### Access control

1.  When an authenticated GET request is made to your outbox, the
    results are filtered according to the authenticated user's
    permissions.

Todo...
-------

-   Binary data / file endpoint
-   Public addressing / special collections

