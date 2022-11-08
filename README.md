# Seal-It !

Add a dedicated Seal of Approval in an online location.

This is a server-side process that will serve up a Seal wherever you embed a URL to "http://<your-ip>/seal-of-approval"

## Intention

### On-request

Server listens for a connection.

Lift the `Location:` header and determine a sha1 hash.

Associate the oURL (origin URL) hash with a random sURL (seal URL) from the active loaded sURLs.

Lookup the sURL using the oURL.

Serve a temporary redirect to the URL (temporary should be with a 30min (configurable) expiry), as well as a header `X-SealIt: <oHash>:<sHash>=<oURL>` for troubleshooting.

We can this way associate any oURL with a sURL, without storing the oURL itself.

### Origin location hash generation

Generic algorithm simply generates a sha1 hash directly from the URL

Using a host glob, and alternative hash generator can be supplied, for example to remove any query sections of a URL (like, page identifiers on a forum)

### Seal URL "database"

The seal URLs can be provided by adding them to a text file, one per line.

On start-up (or on hash-regeneration), SealIt will load these URLs and generate an in-memory hash for each, and adds this to a lookup table.

Any sURLs registered against a non-existent hash should redirect to the generic seal. This is to allow temporary disablement of previously-used seals.

In this way, sURLs have a deterministic hash and do not need to be stored. No stored memory of a prior sURL needs to be tracked.

### Association database

The database can be any implementaiton ; this specification allows simply for a key-value pair text file to serve. Using a SQlite database would improve on the performance once a few thousand oURLs are registered.

The database handler only needs a couple of interactions form the code:

* `lookup(oHash) -> sHash`
* `register(oHash, sHash)`

To support some alternative implementations, two additional methods are specified:

* `load(path)` (likely always implemented)
* `commit(path)` (ikely only for text-based implementations)

## Language

The above was written with Python on mind ; however writing against PHP may actually make more sense. A direct PHP/SQlite may be most lightweight way to put this together, and allow adding to free-tier-hosting solutions.
