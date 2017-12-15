Field | Description
------|------------
**id** | The item's unique id.
deleted | `true` if the item is deleted.
type | The type of item. One of "job", "story", "comment", "poll", or "pollopt".
by | The username of the item's author.
time | Creation date of the item, in [Unix Time](http://en.wikipedia.org/wiki/Unix_time).
text | The comment, story or poll text. HTML.
dead | `true` if the item is dead.
parent | The comment's parent: either another comment or the relevant story.
poll | The pollopt's associated poll.
kids | The ids of the item's comments, in ranked display order.
url | The URL of the story.
score | The story's score, or the votes for a pollopt.
title | The title of the story, poll or job.
parts | A list of related pollopts, in display order.
descendants | In the case of stories or polls, the total comment count.

Example: 

For example, a story: ```/item/8863```

```json
{
  "by" : "dhouston",
  "descendants" : 71,
  "id" : 8863,
  "kids" : [ 8952, 9224, 8917, 8884, 8887, 8943, 8869, 8958, 9005, 9671, 8940, 9067, 8908, 9055, 8865, 8881, 8872, 8873, 8955, 10403, 8903, 8928, 9125, 8998, 8901, 8902, 8907, 8894, 8878, 8870, 8980, 8934, 8876 ],
  "score" : 111,
  "time" : 1175714200,
  "title" : "My YC app: Dropbox - Throw away your USB drive",
  "type" : "story",
  "url" : "http://www.getdropbox.com/u/2/screencast.html"
}
```
