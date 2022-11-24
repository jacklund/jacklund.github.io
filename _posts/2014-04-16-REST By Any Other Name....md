---
layout: post
title:  "REST By Any Other Name..."
---

I\'ve done quite a few interfaces which I\'ve referred to as
\"[RESTful](http://en.wikipedia.org/wiki/REST)\", because I\'ve done
what most people call \"REST\" - using HTTP as the transport, modeling
everything as resources, and using the HTTP verbs (GET, POST, PUT, etc)
to access the resources. I mostly wanted to do REST because the
alternative, SOAP, was so onerous - it required translating a WSDL
document to proxies, serializing and deserializing error objects, and,
worst of all, was a nightmare to version. I had read about
[HATEOAS](http://en.wikipedia.org/wiki/HATEOAS), and had kind of
thought, \"Wow, that sounds\...strange\", but nobody seemed to think
much about it, despite the fact that [Roy
Fielding](http://en.wikipedia.org/wiki/Roy_Fielding) himself
[said](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven),
basically, \"If you ain\'t doing HATEOAS, you ain\'t doing REST, so stop
calling it that!\". So, I remained content in the fact that I was
*mostly* doing REST.

However, with my newest project, I wanted a \"truly\" RESTful interface,
so I decided to delve into HATEOAS and see what the deal was, and
whether it was worth doing. Into the rabbit hole I went, and when I came
out, I finally \"got\" HATEOAS, and understood why Dr. Fielding was
making such a big deal about it, and why it\'s actually very, very
important.

However, since everybody and their sister have attempted to explain it
from the point of view of implementing it, instead I\'ll try to explain
it a different way, starting with a web browser.

To access resources on the web, you only need two things: the top-level
URL of the site you\'re trying to access, and something (the browser)
which knows how to handle the various representations of things on the
site. To get to a particular resource (say, a PDF file), you go to the
top-level page in the site, and navigate using the hyperlinks there to
where the file you want is stored. Because you understand what the links
are pointing to, you know how to navigate the site to find what you
need.

And, you don\'t have to worry about \"versioning\" the website - people
may bookmark certain pages or resources, but if one day they get a \"Not
Found\", they know to just go back to the top level and navigate to the
new place where the resource can be found. Simple - which is why it\'s
such a successful protocol.

Now, imagine a similar thing for a network interface. Your client knows
the URL for the site, and when it retrieves the root resource (\"/\"),
it knows how to interpret the data being returned, understands that
there are hyperlinks in there, and that they point to particular
resources on the site, so it can navigate to the resource it\'s trying
to find. It can even \"bookmark\" the resource (cache the location) so
it can get to it directly in the future, but if it gets a \"Not Found\"
(404), it can just return to the top level and navigate its way again.

The interface is suddenly no longer the protocol - the protocol is
completely fixed. The interface is the *representation of the data*,
just like on the web. So what, you ask? What\'s the big deal? All
you\'ve done is pushed the problem down to the representation, right?
Wrong, because, unlike locations (URIs), which are going to be different
from site to site, *representations of data can be standardized* just
like on the web, which means that the clients accessing the data can be
as well.

A concrete example: let\'s say we have a fairly standard use case - an
interface to a company selling something, and I want to get a list of my
orders on the site. However, my client is unaware of the \"API\" for the
server, so the client code would do an HTTP OPTIONS call at the
top-level URL, which would look something like this:

    OPTIONS / HTTP/1.1
    Accept: application/vnd.siren+json

and would get the response:

    200 OK
    Allow: HEAD,GET,PUT,DELETE,OPTIONS
    Content-type: application/vnd.siren+json
    {
      "links": [
        { "rel": [ "self" ], "href": "http://api.foo.com/" },
        { "rel": [ "users" ], "href": "http://api.foo.com/users" },
        { "rel": [ "items" ], "href": "http://api.foo.com/items" },
        { "rel": [ "orders" ], "href": "http://api.foo.com/orders" }
      ]
    }

(*Note*: I\'m using [Siren](https://github.com/kevinswiber/siren) for
the representation example here, but, really, any standard
representation would do the job).

The code would get back a hash of `rel` -\> `href`, in which we would
look up \"orders\". The client would then do an OPTIONS on
`http://api.foo.com/orders`, like so:

    OPTIONS /orders HTTP/1.1
    Accept: application/vnd.siren+json

and would get the response:

    200 OK
    Allow: HEAD,GET,PUT,DELETE,OPTIONS
    Content-type: application/vnd.siren+json
    {
      "actions": [
        { "name": "list", "method": "GET", "href": "http://api.foo.com/orders" },
        { "name": "add", "method": "POST", "href": "http://api.foo.com/orders" },
        { "name": "delete", "method": "DELETE", "href": "http://api.foo.com/orders" },
      ]
    }

Now, the client knows that to get the list of orders, it just needs to
do a GET on `http://api.foo.com/orders`.

Notice that everything the client code needs in order to access the API
are:

1.  The list of resource names served by the server
2.  The list of actions on the resources
3.  A way of parsing at least one of the available representations of
    the data

That\'s it. No method names, parameter types, or \"API\" in any
traditional sense. Even more importantly: the only thing that needs to
be versioned here is, possibly, the representation type. Everything else
can move around at will - even if the client caches (\"bookmarks\") the
location of what it got before, if the resource gets moved, it just
needs to restart the discovery process from the top to find what it
needs. The client and server are *completely* decoupled.

Now, *that\'s* what I call an API.
