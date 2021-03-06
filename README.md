# twttr

[![Clojars Project](https://img.shields.io/clojars/v/twttr.svg)](https://clojars.org/twttr)

Twitter API client for Clojure, using [`aleph`](https://github.com/ztellman/aleph) and [`clj-oauth`](https://github.com/mattrepl/clj-oauth), supporting [REST](https://dev.twitter.com/rest/reference) and [Streaming](https://dev.twitter.com/streaming/public) endpoints.

`twttr` is a much simplified fork of [`twitter-api`](https://github.com/adamwynne/twitter-api), mostly due to using aleph instead of `http.async.client`. In particular, streaming is now much easier. Also removed: all `^:dynamic` vars, and all but one macro.


## Install

`twttr` is on [Clojars](https://clojars.org/twttr):

    [twttr "2.0.0"]


## Example

```clojure
(ns user
  (:require [twttr.api :as api]
            [twttr.auth :refer [env->UserCredentials]]))

; read credentials from environment variables, namely:
; CONSUMER_KEY, CONSUMER_SECRET, ACCESS_TOKEN, and ACCESS_TOKEN_SECRET
(def creds (env->UserCredentials))

; the "https://api.twitter.com/1.1/users/show.json" Resource URL
; becomes the "users-show" function
(api/users-show creds :params {:screen_name "jack"})
;=> {:id 12, :verified true, :created_at "Tue Mar 21 20:50:14 +0000 2006", ...}

; the value returned is the body of the response,
; with the rest of the response attached as metadata:
(meta *1)
;=> {:request-time 263,
     :headers {"x-rate-limit-reset" "1495550008", "last-modified" "Tue, 23 May 2017 14:29:28 GMT",
               "x-rate-limit-limit" "900", "x-rate-limit-remaining" "898", ...},
     :status 200,
     :connection-time 269}

; update your status
(api/statuses-update creds :params {:status "Well that was quick."})

; get a taste of the sample stream
(def stream (api/statuses-sample creds))
(def first10 (doall (take 10 stream)))
(-> stream meta :body (.close))
(map :text first10)

; track clojure and java tweets 
(def stream (api/statuses-filter creds :params {:track "clojure,java"}))
(mapv #(:text %) stream)
```


## Testing

The tests require that credentials be provided via environment variables with the following names:

```sh
export CONSUMER_KEY=l4VAFAKEFAKEFAKEpy7R7
export CONSUMER_SECRET=dVnTimJtFAKEFAKEFAKEFAKEFAKEFAKEBVYnO91BR1G
export ACCESS_TOKEN=195648015-OIHb87zuFAKEFAKEFAKEFAKEFAKEFAKEb5aLUMYo
export ACCESS_TOKEN_SECRET=jsVg1HFAKEFAKEFAKEFAKEFAKEFAKE4yfOLC5cXA9fcXr
```

Then run `lein test`, which can take a minute since many of the tests involve calling the Twitter API and waiting for an appropriate response. If all tests completed successfully, the test output will end with a message like:

    Ran 27 tests containing 70 assertions.
    0 failures, 0 errors.


## License

Distributed under the Eclipse Public License, same as Clojure.

* Copyright (C) 2017 Christopher Brown
* Copyright (C) 2011 [StreamScience](http://streamscience.co)
