# Reading a user's recent posts

From https://www.w3.org/wiki/Socialwg/Social_API/User_stories#Reading_a_user.27s_recent_posts

> 1. Iris finds a comment by Sam on one of her photos funny. She'd like to read more posts by Sam.
> 2. Iris reads the latest notes by Sam. She also reviews his latest photos.

## 1. Finding more content from an author

Iris finds a comment by Sam on one of her photos funny.

```
GET /api/user/iris/inbox
Host: example.se
Content-Type: application/activitystreams+json
Authorization: Bearer xx-bearer-token-here-xx

{
  "@context": [
    "http://www.w3.org/ns/activitystreams",
    "http://www.w3.org/ns/activitypump"
  ],
  "@id": "https://example.se/~iris/my-stream",
  "@type": "Collection",
  "itemsType": ["activity"]
  "items": [
    {
      "@context": "http://www.w3.org/ns/activitystreams",
      "@id": "https://samsplace.example/samsthoughts/note/41",
      "@type": "Note",
      "actor": "http://samsplace.example/sam/",
      "content": "That dog is as tired as a dog!",
      "inReplyTo": {
        "@type": "Image",
        "@id": "https://example.se/~iris/photos/long-run-with-dog/",
        "url" [
          "href": "http://example.se/~iris/media/tired_dog.png",
          "mediaType": "image/png"
        ]
      }
    }
  ]
}
```

## 2. Reading recent content from an author

Iris would like more such comments on her pets by Sam.  If Iris were
using a web interface, she might merely visit Sam's website at
http://samsplace.example/sam/, linked from the `actor` field, where she
could read Sam's latest jokes and japes in her browser in plain HTML.

However, right now she's using a mobile client.  She clicks the "View
Sam's latest post" button.  Behind the scenes, her client does a GET
request to http://samsplace.example/sam/ and sees
[embedded json-ld](http://www.w3.org/TR/json-ld/#embedding-json-ld-in-html-documents)
describing an ActivityStreams Actor type.  (Note: this method still
needs to be agreed upon, see
[issue 24](https://github.com/w3c-social/activitypump/issues/24)
for details)


```
GET /sam/
Host: samsplace.example
Content-Type: text/html

<!DOCTYPE html>
<html>
  <head>
    <script type="application/ld+json">
      {
        "@context": [
          "http://www.w3.org/ns/activitystreams",
          "http://www.w3.org/ns/activitypump"
        ],
        "@type": "Person",
        "@id": "https://samsplace.example/sam/",
        "following": "https://samsplace.example/following.json",
        "followers": "https://samsplace.example/followers.json",
        "inbox": "https://samsplace.example/inbox.json",
        "outbox": "https://samsplace.example/feed.json",
        "preferredUsername": "sam",
        "displayName": "Sam",
        "summary": "Sam's here to make you laugh.",
        "icon": [
          "https://samsplace.example/pics/sam_smirk.png"
        ]
      }
    </script>
  </head>
  <body>
    <h1>Sam's Jokes and Japes</h1>
    <!-- Content goes here ;) -->
  </body>
</html>
```

Iris's client pulls up Sam's outbox

```
GET https://samsplace.example/feed.json
Host: samsplace.example
Content-Type: application/activitystreams+json
Authorization: Bearer xx-bearer-token-here-xx

{
  "@context": [
    "http://www.w3.org/ns/activitystreams",
    "http://www.w3.org/ns/activitypump"
  ],
  "@id": "https://samsplace.example/~sam/my-stream",
  "@type": "Collection",
  "itemsType": ["activity"]
  "items": [
    {
      "@context": "http://www.w3.org/ns/activitystreams",
      "@id": "https://samsplace.example/samsthoughts/note/41",
      "@type": "Note",
      "actor": "http://samsplace.example/sam/",
      "content": "That dog is as tired as a dog!",
      "inReplyTo": {
        "@type": "Image",
        "@id": "https://example.se/~iris/photos/long-run-with-dog/",
        "url" [
          "href": "http://example.se/~iris/media/tired_dog.png",
          "mediaType": "image/png"
        ]
      }
    },
    {
      "@context": "http://www.w3.org/ns/activitystreams",
      "@id": "https://samsplace.example/samsthoughts/note/40",
      "@type": "Note",
      "actor": "http://samsplace.example/sam/",
      "content": "Why'd the chicken cross the road?  To get away from the wolf!",
    }
  ]
}
```

Iris's client displays Sam's recent posts in a new tab in her mobile
client.  That chicken joke sure was a good one!
