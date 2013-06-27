About
=====
`ngx_secure_token` is token-based authorization module.


Status
======
This module is production-ready.


Configuration directives
========================
secure_token
------------
* **syntax**: `secure_token on|garmin|kodak|off`
* **default**: `off`
* **context**: `http`, `server`, `location`

Enable secure token module. `garmin` and `kodak` are short-cuts for
predefined `secure_token_md5` and `secure_token_input` values.


secure_token_key
----------------
* **syntax**: `secure_token_key <key>`
* **default**: `none`
* **context**: `http`, `server`, `location`

Set secret key.


secure_token_md5
----------------
* **syntax**: `secure_token_md5 <md5_formula> [md5_formula2 .. md5_formulaN]`
* **default**: `none`
* **context**: `http`, `server`, `location`

Set MD5 formula(s) used for token verification.

The result of previous formula can be read via `secure_token_md5` variable.


secure_token_input
------------------
* **syntax**: `secure_token_input <input> [separator]`
* **default**: `none`
* **context**: `http`, `server`, `location`

Set input from which user-provided authorization details will be parsed.

If separator isn't set then basic input format is used and details must
be provided in `<expire>_<token>` format.

Otherwise, complex input format is used and each parameter must be named,
that is: `expire=<expire>_token=<token>`, etc.

Supported parameters are: `expire`, `expires`, `token`, `md5`, `access`.


Configuration variables
=======================
$secure_token_key
-----------------
Secret key.


$secure_token_md5
-----------------
Result of last MD5 formula calculation.


$secure_token_input
-------------------
User-provided authorization details.


$secure_token_input_expire
--------------------------
Parsed `expire` value (text format).


$secure_token_input_expire_native
---------------------------------
Parsed `expire` value (binary format, native size).


$secure_token_input_expire_32bit
--------------------------------
Parsed `expire` value (binary format, 32 bits).


$secure_token_input_input_token
-------------------------------
Parsed `token` value.


$secure_token_input_access
--------------------------
Parsed `access` value.


$unparsed_uri
-------------
Unparsed URI (without args).


$lowercase_uri
--------------
Lowercase version of the unparsed URI (without args).


Sample configurations
=====================
Sample configuration #1
-----------------------
Configuration for Garmin (manual).

    http {
        server {
            location / {
                secure_token        on;
                secure_token_key    <key>;
                secure_token_md5    "${secure_token_input_expire_binary}${unparsed_uri}${secure_token_key}"
                                    "${secure_token_key}${secure_token_md5}";
                secure_token_input  "$arg_garmindlm";

                set $backend        origin.garmin.com;
                proxy_pass          http://$backend$lowercase_uri;
            }
        }
    }


Sample configuration #2
-----------------------
Configuration for Garmin (predefined).

    http {
        server {
            location / {
                secure_token        garmin;
                secure_token_key    <key>;

                set $backend        origin.garmin.com;
                proxy_pass          http://$backend$lowercase_uri;
            }
        }
    }


Sample configuration #3
-----------------------
Configuration for Kodak (predefined).

    http {
        server {
            location / {
                secure_token        kodak;
                secure_token_key    <key>;

                proxy_pass          http://origin.kodak.com;
            }
        }
    }


License
=======
    Copyright (c) 2012, FRiCKLE <info@frickle.com>
    Copyright (c) 2012, Piotr Sikora <piotr.sikora@frickle.com>
    All rights reserved.

    Redistribution and use in source and binary forms, with or without
    modification, are permitted provided that the following conditions
    are met:
    1. Redistributions of source code must retain the above copyright
       notice, this list of conditions and the following disclaimer.
    2. Redistributions in binary form must reproduce the above copyright
       notice, this list of conditions and the following disclaimer in the
       documentation and/or other materials provided with the distribution.

    THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
    "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
    LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
    A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
    HOLDERS OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
    SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
    LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
    DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
    THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
    (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
    OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
