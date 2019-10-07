# Colorcode 2019

This file contains all the notes for each assignment in ColorCode 2019 workshop.

## Task

We will hack [hackthebox](https://www.hackthebox.eu). Hack The Box is an online platform allowing to test the penetration testing skills.

## But what is penetration testing?

Basically, this is that famed `white hat` or `ethical hacking` thing. We are the good guys trying to break into things in order to find vulnerabilities and problems that anyone outside of the organization might use for breaking our stuff. There are a lot of reasons why to break into things, but the main one is - **IT'S A LOT OF FUN**. Also, you don't need to be a heavy-lifting programmer in order to hack into things - you need to be clever and have the willingness to take an extra mile to
break things. Fun, isn't it?

## Before we even start

What is the most important tool in any IT-person life? [This](https://www.google.com)

## The task itself

As I've mentioned, we will break into Hack The Box. By the end of this challenge, you can go home, break into the website once again and then learn how to break stuff even more. There are a few strategies used by pros, and because we're pros now, we'll use one as well. Our is called `blind testing` - meaning, that the only thing we know about our company is it's name. Everything else we have to figure out ourselves. Let's roll!

First thing we can see, when we scroll through the page is very welcoming `join` button. And this is where the fun begins - the page already invites us in to hack our way in. Any ideas?

### Follow me, I know da way

When we open [the website](https://www.hackthebox.eu/invite) we don't see anything that might give us any hint on how to hack the website. Let's dive deeper. Right click on the browser and click on `inspect element`. 

The card that just have opened shows us the `html` and `css` parts of the website. Meaning - it shows us the skin and the bones of the app. But we need to get into the brains of it, we need to go even deeper! We have `inspector`, `console`, `debugger` and the rest of it - let's go into `debugger`. If you don't know what debugging is - it's a way to see all the problems and errors ine app or website. Roaming around, we see `js` folder and then `inviteapi.min.js`. This file sounds interesting...

First of all - what is an `API` and what this `.js` stands for?

### You get an invitation, everyone gets an invitation!

Upon reading and pokign around in the file, we see `makeInviteCode`. Ok, now what? Let's make the code!

When you don't know what this thing does, what do you do? Well, you use it and then try to figure it out! Click `console` and type `makeInviteCode`. Console returns:

```javascript
function makeInviteCode()
```

Hm, if that's the case, then let's use it as a function! I won't bore you with book-like definition of what function is, let's put it like that

```
It's the thing that does things when we tell it to. And we can also make the thing do the things the way we like the things.
```

Easy, right?

Write `makeInviteCode()`. We get:

```javascript
0: 200
data: Object { data: "SW4gb3JkZXIgdG8gZ2VuZXJhdGUgdGhlIGludml0ZSBjb2RlLCBtYWtlIGEgUE9TVCByZXF1ZXN0IHRvIC9hcGkvaW52aXRlL2dlbmVyYXRl", enctype: "BASE64" }
success: 1
<prototype>: {…
```

or

```javascript
0: 200
data: Object { data: "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb /ncv/vaivgr/trarengr", enctype: "ROT13" }
success: 1
<prototype>: {…
```

Any clues?

### All right, then. Keep your secrets.

Upon typing `enctype` in Google we get:

```
What is Enctype property?
The enctype property sets or returns the value of the enctype attribute in a form. The enctype attribute specifies how form-data should be encoded before sending it to the server. The form-data is encoded to "application/x-www-form-urlencoded" by default.
```

A lot of stuff, yea? There's one thing that's really interesting for us - **encoded**. We can make an assumption, that this thing uses some kind of encryption to hide it's true self. Let's try to decode it then!

> Note to reader - we'll tackle ROT13 approach here, but there's literally no difference on how to deal with that apart from googling BASE64 decoder and ROT13 decoder. Also, in this case ROT13 is a tad more difficult.

How to do that - the easiest way is to `google` the `<algorithm> decoder`. Upon decoding the `ROT13` encrypted thing we get:

```
In order to generate the invite code, make a POST request to /api/invite/generate
```

Ok... another hurdle. What's `POST`? Why it's capitalized? If we'd type `POST` into google we get a lot of stuff about Post Office. That's definitely not what we want. What about `POST request`?

Now we're talking - we're getting all info about the POST request, what does it do and how does it works. But we're pros, we know what it does (it sends the data somewhere and then waits for response. Same as you DM someone on Instagram), so let's make the `POST` request.

> Note to reader - this tutorial assumes usage of any device, even the one without terminal - therefore we going to do it VERY hacky

Let's go to [reqbin.com](https://reqbin.com/) (Also, fun note, you can do **literally everything** in your web browser) and do the `POST` request to `/api/invite/generate`. Note - the way that it's written, assumes that we'd prepend it with the `HOST` name - which is `https://www.hackthebox.eu`.

We get:

```json
{
    "success": 1,
    "data": {
        "code": "RlZEWlYtRlFaREUtTVNHQ1otTk9CRlgtQVJHVUk=",
        "format": "encoded"
    },
    "0": 200
}
```

Again, a lot of signs that doesn't make sense whatsoever. We see that it's encoded - so, let's copy that into our `ROT13` decoder and see if we're able to get into the site!

### You shall not pass

> Note to reader - if you had an example with BASE64 from start, this step will be a lot shorter 

Dang, doesn't work. We're so close - let's try to get into the website for the very last time.

As I don't thing we'd be able to solve this riddle ourselves out-of-the-blue, then let's go back to the first step and try to figure it out from the ground up. What was the first step to get where we are? `makeInviteCode()`

> Note to reader - it might take more than one try to get the `BASE64` to appear

Huh, we've got something else. `BASE64`? Another riddle? As we can see, it's still a property of `enctype` - which tells us, that it might be connected to encryption again. So - let's decode! Find BASE64 decoder in google and paste the response we've got from `/api/invite/generate`.

```
FVDZV-FQZDE-MSGCZ-NOBFX-ARGUI
```

Nice! That looks promising! 

As the last step, let's type that to our `Invite Code` box. Ha, we won!

## Conclusion

That was a ride! Although we've haven't covered a LOT of stuff, this is the best example that IT and tech is not that hard after all. It's all about thinking, tinkering and not giving up when things go sideways. That's our job in a nutshell - we don't know how to do stuff most of the time, we know where to look.

Go home and try to do this again - it'll be WAY easier this time!

