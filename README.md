# Seal It!

A server app that produces a seal image when accessed.

When a URL accesses `/seal` , a HTTP/300 message is returned with a URL to a seal picture.

If the request comes with a `Location` header, a hash is registered and a seal URL is referenced against the hash. The same seal image is returned for a same Location.

Seal URLs are pulled from a seals.db SQLite file.

Seal database can thereby be updated independently.

If a seal cannot be returned (missing reference, etc), a generic image is returned.

## Arbitrary items

Also respond to `/<tag>` - `/seal` would be the default configuration

Tag must be a valid C-variable name.

Special tag `/-any` searches all URLs

## Database

Table 1 : Tag

* id : int, PRI, AUTO
* name : char[32]

Table 2 : Seals

* id : int, PRI, AUTO
* url : blob[256]
* tag : int -- reference to tag ID

Table 3 : Locations

* hash : int, PRI
* ref : int, ext key to Seals for the seal to return.

## Tools

* `seal-serve` - to serve the seals. a read-only utility
* `seal-update` - writes to a local SQLite DB. Operations:
    * `load <file>` - load a file of URLs like `<tag> <url>` entries
    * `add <tag> <url>` - add a single URL with a tag
    * `list <tag>` - list URLs under a tag
    * `search [-t <tag>] <term>` - search for text in each URL, optionally limiting to a tag space
    * `del <id>` - delete a URL, by ID
    * `retag <urlid> <newtag>` - reassociate a URL with a different tag. Create the tag if needed
* `seal-check` - check all URLs in the database.
    * remove any URLs that are returning HTTP/400 errors
    * redirect registered locations for deleted URLs


## Hosting

Seal gen should be able to run on free tier hosting.

Therefore, implement either in

* PHP - free tier sites can usually host basic PHP easily
* Python - try <https://www.pythonanywhere.com/>

