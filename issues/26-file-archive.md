## overview

Decred lacks a place to store photos, videos, presentations, design assets and other large binary files.

Some examples:

* design group assets
* photos and videos from events
* presentations, slides
* release binaries
* database dumps (dcrdata)
* datasets for research group
* future projects
* maybe: archive of code repos (project and dependencies)

Besides just hosting, it would be nice if enthusiastic archivists are allowed to continuously mirror subsets of files. In case the primary archive goes down, it can be later (partially) reconstructed from mirrored parts.

Additionally, it would be nice to have auditable track record of files. It could provide extra integrity protection and help with reconstruction of lost files.

## requirements

Below are requirements organized in 'feature groups', ordered from simplest and immediately tangible to more complex, but also more resilient.

One fundamental requirement is the system can be used with just open source software. Preferably, tried ubiquitous open protocols and software available in popular OS distributions.

## feature group: file server basic

Req:

* serve public files via HTTP or HTTPS
* public files can be downloaded without auth and without javascript
* auth for group of selected uploaders (curators)
  * email is optional
* consider: forbid deletions and overwrites for regular uploaders to prevent content destruction
* smooth UX for uploaders
* forbid spaces and other junk in file names, no ugly `%20`
  * can show a preview+confirmation "your file will be renamed to x, correct the name if necessary and proceed"
* rate limiting and other abuse protection
* roles: public_reader, curator (upload, organize), admin

TODO: Plan:

* who and how will organize content to prevent chaos and enable other features
  * well defined directory structure
* process for handling crap content that slips through

## feature group: file server extras

A bit on top of file server basic, not too hard to achieve.

Req:

* personal and group dirs
* uploader of a file must be known
* user action logging
* resume download
* proper HTTP Last-Modified based of file system attrs (enables wget --timestamping)
* non-public files (access control, need account and perms to access)
* auth for uploaders without browser (e.g. SSH)
* files are organized in a way that can be consumed by photo and video album web apps "on top" of the file layer
* search
* stats (detect unpopular files)

## feature group: file server advanced

Req:

* redirects for moved files (old links must work)
* file metadata with custom fields
  * e.g. geo, event, people, URIs where file was published on the web (tweets, etc)
  * provided by file system or custom impl
* files and meta are organized in a way that can be consumed by photo and video album web apps "on top" of the file layer
* multiple 'variants' of file
  * mainly for videos, audio and photos: different quality levels, audio-only variant of videos

Notes:

* A lot of interesting features can be implemented with metadata. Meta files can be 'primary' entities that 'resolve into' actual files. This allows to resolve into variants with different quality for media content. Meta-first approach allows to escape the limits of rigid directory hierarchy and do tags and hierarchical tags, which is super flexible (is it called [faceted classification](https://en.wikipedia.org/wiki/Faceted_classification)?).

TODO:

* schema and validation for metadata

## feature group: sync

Req:

* easy, fast and robust initial download (similar to git clone) 
* easy, fast and robust incremental sync (similar to git fetch)
* selective clone and sync
  * just the high-res photos, or just mid-res videos, or just podcasts
  * can sync all metadata to know what exists, and then choose certain things to mirror
* workflow to reconstruct an archive from multiple partial mirrors
* extra role added: archivist

Notes:

* things like wget recursive or curl are complex, slow and unreliable

TODO:

* rsync looks like cheap first thing to try
* think of better names for 'archivist' role, e.g. sync_user
* handle case when some files are not available in all variants
  * e.g. when you sync high quality photos but some are only low quality
* think how sync plays together with access control

## feature group: audit, accountability, integrity

Req:

* all changes to content are traceable
  * for every file addition, change or deletion the system records timestamp, user, hashes, and change message -- all similar to Git commits
  * deleted files may be pruned (unlike Git) but there must be a record about what the file was (hash, name and other meta), who and when deleted it
* workflow to merge multiple partial mirrors -- similar to merging Git branches

TODO:

* revise process for handling crap content that slips through

## plan

* chances are software already exists for this, do some research of candidates below
* estimate feature groups
* get lowest hanging fruits first
* ask people to upload their files (events, design, etc)

## candidates:

* protocols: [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol), rsync
* clients: FileZilla, WinSCP
* Git LFS
* IPFS
* Keybase KBFS ([example directory](https://keybase.pub/jz_bz/decred/binaries/v1.4.0/))
* Nextcloud (file hosting, doc hosting and collab, photo gallery, calendar (#116))

## discussions

* [2019-02-11](https://matrix.to/#/!MgQoetFiyjrHAywokv:decred.org/$15498591715889PwtpD:decred.org): Keybase KBFS for file archive, some people already use it
* [2019-02-12](https://matrix.to/#/!OfChXgczrIlpEZSFAv:decred.org/$15499978908114BFEQQ:decred.org): repository of images that Ditto can give to media outlets; sync.com, KBFS, syncthing, piwigo, Nextcloud, shared calendar
