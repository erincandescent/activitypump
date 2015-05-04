# User Posts a Note

From https://www.w3.org/wiki/Socialwg/Social_API/User_stories#User_posts_a_note

> 1. Eric writes a short note to be shared with his followers.
> 2. After posting the note, he notices a spelling error. He edits the note and re-posts it.
> 3. Later, Eric decides that the information in the note is incorrect. He deletes the note.

## 1. Posting a note

Eric writes a post in his client and it submits something like this to his outbox (feed)::

```
POST /api/user/eric/feed HTTP/1.1
Host: example.se
Content-Type: application/activitystreams+json
Authorization: Bearer xx-bearer-token-here-xx

{
    "@context": "http://www.w3.org/ns/activitystreams",
    "@type": "Post",
    "object": {
        "@type": "Note",
        "content": "Helo World!"
    }
    "to": [
        {
            "id": "http://example.se/api/user/eric/feed",
            "@type": "Collection"
        }
    ]       
}
```

The server responds by giving you back the full object activity
and object that it just created::

```
{
    "@id": "http://example.se/api/activity/1"
    "@context": "http://www.w3.org/ns/activitystreams",
    "@type": "Post",
    "author": "http://example.se/~erik",
    "published": "2015-05-04T19:01:55Z",
    "object": {
        "@id": "http://example.se/api/note/1"
        "@type": "Note",
        "author": "http://example.se/~erik",
        "content": "Helo World!",
        "published": "2015-05-04T19:01:55Z",
    }
    "to": [
        {
            "id": "http://example.se/api/user/eric/feed",
            "@type": "Collection"
        }
    ]       
}
```

## 2. Updating a note

He notices he made a typo and updates the note with his collection::
```
POST /api/user/eric/feed HTTP/1.1
Host: example.se
Content-Type: application/activitystreams+json
Authorization: Bearer xx-bearer-token-here-xx

{
    "@context": "http://www.w3.org/ns/activitystreams",
    "@type": "Update",
    "object": {
        "@id": "http://example.se/api/note/1"
        "@type": "Note",
        "content": "Hello World!"
    }
    "to": [
        {
            "id": "http://example.se/api/user/eric/feed",
            "@type": "Collection"
        }
    ]       
}
```

This agains just sends you back the activity you just posted

```
{
    "@id": "http://example.se/api/activity/2"
    "@context": "http://www.w3.org/ns/activitystreams",
    "@type": "Update",
    "author": "http://example.se/~erik",
    "published": "2015-05-04T19:02:55Z",
    "object": {
        "@id": "http://example.se/api/note/1"
        "@type": "Note",
        "author": "http://example.se/~erik",
        "content": "Hello World!",
        "published": "2015-05-04T19:01:55Z",
        "updated": "2015-05-04T19:02:55Z"
    }
    "to": [
        {
            "id": "http://example.se/api/user/eric/feed",
            "@type": "Collection"
        }
    ]       
}
```

## 3. Deleting a note

He decides he doesn't want to greet the world and deletes his post::
```
POST /api/user/eric/feed HTTP/1.1
Host: example.se
Content-Type: application/activitystreams+json
Authorization: Bearer xx-bearer-token-here-xx

{
    "@context": "http://www.w3.org/ns/activitystreams",
    "@type": "Delete",
    "object": {
        "@id": "http://example.se/api/note/1"
        "@type": "Note",
    }    
}
```

A shell of the note remains which is referenced in previous activities that
have previously referenced this object. The server will also be required to
return a HTTP 410 GONE status when the ID is dereferenced. The shell looks
looks like::
```
{
    "@id": "http://example.se/api/note/1",
    "@type": "Note",
    "published": "2015-02-10T15:04:55Z",
    "updated": "2015-02-10T15:04:55Z",
    "deleted": "2015-02-10T15:04:55Z"
}
```