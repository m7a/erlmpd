---
section: 32
x-masysma-name: erlmpd
title: ERLMPD - Erlang MPD Client Library
date: 2024/05/31 22:38:44
lang: en-US
author: ["Caolan McMahon (caolan@caolanmcmahon.com)", "Linux-Fan, Ma_Sys.ma (Ma_Sys.ma@web.de)"]
keywords: ["erlang", "library", "mpd", "music"]
x-masysma-version: 1.0.0
x-masysma-website: https://masysma.net/32/erlmpd.xhtml
x-masysma-repository: https://www.github.com/m7a/bo-erlmpd
x-masysma-copyright: |
  Copyright (c) 2009 Caolan McMahon (caolan@caolanmcmahon.com)
  Copyright (c) 2024, 2025 Ma_Sys.ma (info@masysma.net)
---
Foreword
========

This is a fork of the original [erlmpd](https://github.com/caolan/erlmpd)
library by [Caolan](https://github.com/caolan). The original README content
is kept in `README-original.txt` and reads as follows:

> A simple client library for communicating with MPD (<http://mpd.wikia.com>) -
> "a flexible, powerful, server-side application for playing music"
>
> To build just run make, this will create a doc directory which should
> contain a useful reference to the functions exported by the erlmpd module.
>
> This module was developed on Erlang R13B, should you encounter any problems
> I'd like to hear from you. Please get in touch via Github, or find me on
> Twitter at <http://twitter.com/caolan>
>
> Peace ✌

Introduction
============

This repository provides a pure Erlang client library for the
[MPD (Music Player Daemon) Protocol](https://mpd.readthedocs.io/en/latest/protocol.html).

It does not expose the entirety of the protocol's capabilities but is
structured in a way that new API commands can be added easily.

The version provided in this repository has major changes compared to the
original version but still strives to be API compatible for the endpoints that
are exposed in the original version already.

Getting Started
===============

Assume your MPD runs on `127.0.0.1:6600`, you can get started interactively
with the library as follows:

~~~
$ rebar3 shell
===> Verifying dependencies...
===> Analyzing applications...
===> Compiling erlmpd
Erlang/OTP 25 [erts-13.1.5] [source] [64-bit] [smp:36:36] [ds:36:36:10] [async-threads:1] [jit:ns]

Eshell V13.1.5  (abort with ^G)
1> {ok, Conn} = erlmpd:connect("127.0.0.1", 6600), erlmpd:status(Conn).
[{repeat,false},
 {random,false},
 {single,false},
 {consume,false},
 {partition,<<"default">>},
 {playlist,2},
 {playlistlength,514},
 {mixrampdb,<<"0">>},
 {state,pause},
 {song,504},
 {songid,505},
 {time,298.48},
 {elapsed,<<"298.218">>},
 {bitrate,0},
 {duration,<<"480.026">>},
 {audio,<<"44100:16:2">>},
 {nextsong,<<"505">>},
 {nextsongid,<<"506">>}]
~~~

This demonstrates establishing a connection with `erlmpd:connect/2` and
then querying of the status with `erlmpd:status/1`. For interactive usage it
is important to remember that there is a short timeout before the connection
cannot be used anymore, hence in the example, the status is queried from within
the same line.

To make use of the library in a rebar3-enabled project, declare a dependency to
it as follows:

~~~
{deps, [
	{erlmpd, {git, "https://github.com/m7a/bo-erlmpd.git", {branch, "master"}}},
]}.
~~~

...or pin it to a commit ID or branch (e.g. `masysma_changes`) as preferred.

There is currently no release schedule where stable versions could be obtained
from.

Documentation
=============

To generate the API documentation, run

	make -C src ../doc/erlmpd.html

It results in a directory `doc` being created which contains an API
documentation generated from the source code under `src/erlmpd.erl`.

A (potentially outdated) copy of the generated documentation can also be
found on the website: <https://masysma.net/32/erlmpd_att/erlmpd.html>.

Repository Structure
====================

The files in the repository are structured as follows:

~~~
bo-erlmpd/
  |
  +-- erlmpd_att/            -- documentation resources
  |
  +-- src/
  |    |
  |    +-- erlmpd.erl        -- core of the implementation
  |    |
  |    +-- ...               -- OTP auxiliary files
  |
  +-- support/
  |    |
  |    +-- include.mk        -- Makefile snippet/original build instructions
  |
  +-- rebar.config           -- Manifest for rebar3 integration
  |
  +-- README.md              -- Ma_Sys.ma-provided README documentation
  |
  +-- README-original.txt    -- Original README documentation
  |
  +-- debian-changelog.txt   -- Files related to MDPC 2.0 Debian Package
  +-- build.xml                 Build (probably not needed by most developers)
  |
  +-- LICENSE                -- GPL v3 copy
~~~

Branch Management
=================

There are two relevant branches in this repository:

 * `master` -- This is the working state with all Ma_Sys.ma changes including
   those which may primarily be of internal interest (MDPC 2.0 integration,
   website integration, ...)

 * `masysma_changes` -- This is the branch that had originally been intended
   as a pull request to the original repository. As a result, it contains less
   customization and e.g. retains the original README and build instructions
   as-is, mostly only adding to the existent code.

My current development approach is to attempt to implement all features and
bug fixes on branch `masysma_changes` and then merge them into `master`.

The default `master` should be OK for many use cases. It contains more code,
though. If an auditable change history is of importance to you, consider using
the `masysma_changes` branch directly.

License
=======

Caolan agreed to release the original source under GPL-3-or-later making it
possible to create and modify this fork in the first place.

	ERLMPD - Erlang MPD Client Library
	Copyright (c) 2009 Caolan McMahon <caolan@caolanmcmahon.com>
	Copyright (c) 2024, 2025 Ma_Sys.ma <info@masysma.net>
	
	This program is free software: you can redistribute it and/or modify it
	under the terms of the GNU General Public License as published by the
	Free Software Foundation, either version 3 of the License, or
	(at your option) any later version.
	
	This program is distributed in the hope that it will be useful, but
	WITHOUT ANY WARRANTY; without even the implied warranty of
	MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
	GNU General Public License for more details.
	
	You should have received a copy of the GNU General Public License along
	with this program. If not, see <https://www.gnu.org/licenses/>.

Note that the full text of the GPL is stored in file `LICENSE` in the
repository.

API Support Status
==================

Table updated 2024-09-08. Some development notes about the API support status:

 * Unsupported commands may be available through the `command` APIs, but it may
   be better to just add the missing function in erlmpd and send a patch!
 * Filters are supported except for the new _Explicit case-sensitivity_ API.
   If filters are not supported for a given command yet, the existing functions
   for filters in ERLMPD may make adding this functionality easier
 * In many commands, sort and window parameters are not supported yet. Adding
   them could be done in a similar way like with `sticker_find/7`.

MPD Protocol         ERLMPD API                          Support  Comment
-------------------  ----------------------------------  -------  -------------------------------
`clearerror`         `clearerror/1`                      Yes
`currentsong`        `currentsong/1`                     Yes
`idle`               `idle/1`, `idle/2`                  Yes
                     `idle_send/2`, `idle_receive/1`      
`status`             `status/1`                          Yes
`stats`              `stats/1`                           Yes
`consume`            `consume/2`                         Yes
`crossfade`          `crossfade/2`                       Yes
`mixrampdb`                                              No       Probably easy to add
`mixrampdelay`                                           No       Probably easy to add
`random`             `random/2`                          Yes
`repeat`             `repeat/2`                          Yes
`setvol`             `setvol/2`                          Yes
`getvol`                                                 No       Probably easy to add
`single`             `single/2`                          Yes
`replay_gain_mode`                                       No       Probably easy to add
`replay_gain_status`                                     No       Probably easy to add
`volume`                                                 No       Probably easy to add
`next`               `next/1`                            Yes
`pause`              `pause/2`                           Yes
`play`               `play/1`, `play/2`                  Yes
`playid`             `playid/1`, `playid/2`              Yes
`previous`           `previous/1`                        Yes
`seek`               `seek/3`                            Yes
`seekid`             `seekid/3`                          Yes
`seekcur`                                                No       Probably easy to add
`stop`               `stop/1`                            Yes
`add`                `add/2`                             Partial  `POSITION` not supported
`addid`              `addid/3`, `addid_relative/3`       Yes
`clear`              `clear/1`                           Yes
`delete`             `delete/2`                          Partial  `START:END` not supported
`deleteid`           `deleteid/1`, `deleteids/2`         Yes
`move`               `move/3`                            Yes
`moveid`             `moveid/3`                          Yes
`playlist`           `playlist/1`                        Yes
`playlistfind`       `playlistfind/3`                    No       Older syntax with limitations
`playlistid`         `playlistid/1`, `playlistid/2`      Yes
`playlistinfo`       `playlistinfo/1`, `playlistinfo/2`  Yes
`playlistsearch`     `playlistsearch/3`                  No       Older syntax with limitations
`plchanges`          `plchanges/2`                       Partial  `START:END` not supported
`plchangesposid`     `plchangesposid/2`                  Partial  `START:END` not supported
`prio`                                                   No       Probably easy to add
`prioid`                                                 No       Probably easy to add
`rangeid`                                                No       Probably easy to add
`shuffle`            `shuffle/1`, `shuffle/2`            Yes
`swap`               `swap/3`                            Yes
`swapid`             `swapid/3`                          Yes
`addtagid`           `addtagid/3`                        No
`cleartagid`         `cleartagid/3`                      No
`listplaylist`       `listplaylist/2`                    Partial  `START:END` not supported
`listplaylistinfo`   `listplaylistinfo/2`                Partial  `START:END` not supported
`searchplaylist`                                         No       Probably easy to add
`listplaylists`      `listplaylists/1`                   Yes
`load`               `load/2`                            Partial  `START:END` and `POSITION` not supported
`playlistadd`        `playlistadd/3`                     Partial  `POSITION` not supported
`playlistclear`      `playlistclear/2`                   Yes
`playlistdelete`     `playlistdelete/3`                  Yes
`playlistlength`                                         No       Probably easy to add
`playlistmove`       `playlistmove/4`                    Partial  `START:END` not supported
`rename`             `rename/3`                          Yes
`rm`                 `rm/2`                              Yes
`save`               `save/2`                            Partial  `MODE` not supported
`albumart`           `albumart/2`                        Yes
`count`              `count/2`, `count/3`,               Yes
                     `count_group/3`                      
`getfingerprint`                                         No       Probably easy to add
`find`               `find/2`, `find/3`, `find_ex/3`     Yes
`findadd`                                                No
`list`               `list/2`                            No       Only supports `list TYPE` syntax
`listall`            `listall/1`, `listall/2`            Yes
`listallinfo`        `listallinfo/1`, `listallinfo/2`    Yes
`listfiles`                                              No
`lsinfo`             `lsinfo/2`                          Yes
`readcomments`                                           No
`readpicture`        `readpicture/2`                     Yes
`search`             `search/2`, `search/3`              Partial  `sort` and `window` not supported
`searchadd`                                              No
`searchaddpl`                                            No
`searchcount`                                            No
`update`             `update/1`, `update/2`              Yes
`rescan`                                                 No
`mount`                                                  No       _Mounts and neighbors_ API not supported
`unmount`                                                No
`listmounts`                                             No
`listneighbors`                                          No
`sticker get`        `sticker_get/4`                     Yes
`sticker set`        `sticker_set/5`                     Yes
`sticker delete`     `sticker_delete/3`,                 Yes
                     `sticker_delete/4`                   
`sticker list`       `sticker_list/3`                    Yes
`sticker find`       `sticker_find/4`, `sticker_find/7`  Partial  `sort` and `window` only supported for `{NAME} = {VALUE}` syntax
`stickernames`                                           No
`stickertypes`                                           No
`close`              `close/1`                           Yes
`kill`               `kill/1`                            Yes
`password`           `connect/3`, `password/1`           Yes
`ping`               `ping/1`                            Yes
`binarylimit`                                            No
`tagtypes`           `tagtypes/1`                        Yes
`tagtypes disable`                                       No       Probably easy to add
`tagtypes enable`                                        No       Probably easy to add
`tagtypes clear`                                         No       Probably easy to add
`tagtypes all`                                           No       Probably easy to add
`partition`          `partition/2`                       Yes
`listpartitions`     `listpartitions/1`                  Yes
`newpartition`       `newpartition/2`                    Yes
`delpartition`       `delpartition/2`                    Yes
`moveoutput`         `moveoutput/2`                      Yes
`disableoutput`      `disableoutput/2`                   Yes
`enableoutput`       `enableoutput/2`                    Yes
`toggleoutput`       `toggleoutput/2`                    Yes
`outputs`            `outputs/1`                         Yes
`outputset`                                              No
`config`                                                 No       Probably easy to add
`commands`           `commands/1`                        Yes
`notcommands`        `notcommands/1`                     Yes
`urlhandlers`        `urlhandlers/1`                     Yes
`decoders`                                               No       Probably easy to add
`subscribe`                                              No       _Client to client_ API not supported
`unsubscribe`                                            No
`channels`                                               No
`readmessages`                                           No
`sendmessage`                                            No

MDPC 2.0 Integration
====================

If all of the necessary dependencies are installed, you can build a Debian
package for this repository by running the following command:

	ant package

Contact and Future Directions
=============================

The original code worked well even after 15 years of not being changed at all.
Hence I expect this library to not require frequent changes. When client
development requires access to them, new API functions can of course be added.

For questions pertaining to this fork, please write to `info@masysma.net`.

References
==========

 * Erlmpd documentation: <https://masysma.net/32/erlmpd_att/erlmpd.html>
 * Erlmpd fork repository: <https://www.github.com/m7a/bo-erlmpd>
 * Music Player Daemon: <https://musicpd.org/>
 * MPD Protocol: <https://mpd.readthedocs.io/en/latest/protocol.html>
 * Original erlmpd repository: <https://github.com/caolan/erlmpd>
