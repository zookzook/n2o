N2O: Erlang Web Framework
=========================

[![Build Status](https://travis-ci.org/synrc/n2o.svg?branch=master)](https://travis-ci.org/synrc/n2o)

Features
--------

* Formatters: **BERT**, JSON, Binary or Custom
* Protocols: Nitrogen, RoR, N2O
* Endpoints: **WebSocket**, HTTP, REST
* PubSub: MQS, GPROC
* Templates: DTL
* Sessions: STD and Autogenerated Logins
* DOM Language: Nitrogen DSL, Erlang Shen
* Speed: **15K** **req/s**
* Security: AES CBC 128
* Samples: Skyline (DSL), Games (SPA)

All Features in One snippet
---------------------------

```erlang
-module(index).
-compile(export_all).
-include_lib("n2o/include/wf.hrl").

peer()    -> io_lib:format("~p",[wf:peer(?REQ)]).
message() -> wf:js_escape(wf:html_encode(wf:q(message))).
main()    -> #dtl{file="index",app=n2o_sample,bindings=[{body,body()}]}.
body() ->
    {ok,Pid} = wf:comet(fun() -> chat_loop() end),
    [ #panel{id=history}, #textbox{id=message},
      #button{id=send,body="Chat",postback={chat,Pid},source=[message]} ].

event(init) -> wf:reg(room);
event({chat,Pid}) -> Pid ! {peer(), message()};
event(Event) -> skip.

chat_loop() ->
    receive {Peer, Message} ->
       wf:insert_bottom(history,#panel{body=[Peer,": ",Message,#br{}]}),
       wf:flush(room) end, chat_loop().
```

And try to compare how this functionality would be implemented
with your favourite language/framework.

Performance
-----------

We are using for measurement ab, httperf, wrk and siege, all of them. The most valuable storm
created by wrk and it is not achieved in real apps but could show us the internal throughput
of individual components. The most near to real life apps is siege who also make DNS lookup
for each request. So this data shows internal data throughput by wrk:

| Framework | Enabled Components | Speed | Timeouts |
|-----------|--------------------|-------|----------|
| PHP5 FCGI | Simple script with two <?php print "OK"; ?> | 5K | timeouts |
| ChicagoBoss| No sessions, No DSL, Simple DTL | 500 | no |
| Nitrogen  | No sessions, No DSL, Simple DTL | 1K | no |
| N2O       | All enabled, sessions, Template, heavy DSL | 7K | no |
| N2O       | Sessions enabled, template with two variables, no DSL | 10K | no |
| N2O       | No sessions, No DSL, only template with two vars | 15K | no |

Kickstart Bootstrap
-------------------

To try N2O you just need to clone a N2O repo from Github and build.
We use very small and powerful mad tool designed for our Web Stack.

    $ git clone git://github.com/5HT/n2o
    $ cd n2o/samples
    $ ./mad deps compile plan repl

Now you can try: [http://localhost:8000](http://localhost:8000)

LINUX NOTE: if you want to have online recompilation you should do at first:

    $ sudo apt-get install inotify-tools

Erlang version
--------------

We don't accept any reports of problems with ESL or Ubuntu packaging. We do support only Erlang built from sources, built with kerl or installed on Mac with brew. If you have problems with your favourite Erlang package for your OS, please report issues to package maintainer.

Posting Issues on Github
-------

Thank you for using N2O (you've made a wise choice) and your contributions to help make it better.
Before posting an issue in Github, please contact us via the options listed below in the support section. Doing so will help us determine whether your issue is a suggested feature, refactor of existing code, bug, etc, that needs to be posted to GitHub for the contributing development community of N2O to incorporate. DO NOT post issues to GitHub related to misuses of N2O, all such issues will be closed. 

Support
-------
* [![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/5HT/n2o?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
* IRC Channel #n2o on FreeNode 24/7

Documentation
-------

If you are new or need to determine whether the N2O architecture and philosophy is a fit for your project

* Official N2O Book [PDF](https://synrc.com/apps/n2o/doc/book.pdf)

Windows Users
-------------

For windows you should install http://msys2.github.io and appropriative packages to use Synrc Stack:

* git
* mc
* sh

Credits
-------

* Maxim Sokhatsky -- core, shen, windows
* Dmitry Bushmelev -- endpoints, yaws, cowboy
* Andrii Zadorozhnii -- elements, actions, handlers
* Vladimir Kirillov -- mac, bsd, xen, linux support
* Andrey Martemyanov -- binary protocols
* Oleksandr Nikitin -- security
* Anton Logvinenko -- doc
* Roman Shestakov -- advanced elements, ct
* Jesse Gumm -- nitrogen, help
* Rusty Klophaus -- original author

OM A HUM
