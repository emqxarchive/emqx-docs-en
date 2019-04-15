
==================
Erlang/OTP Platform Introduction
==================

*EMQ*  message server is completely developed based on the Erlang/OTP language platform. Erlang/OTP is a very good Soft-Realtime Low-Latency language platform, which is very suitable for the development of message servers:

1. Fine-grained garbage collection (Garbage Collection)

2. Preemptive fair process scheduling based on Reduction count

3. Fault tolerant recovery and distribution support
--------------
Erlang/OTP History
--------------

Ericsson, Joe Armstrong, nearly 30 years from 1988

The Erlang language was originally designed to develop telecommunications equipment and systems.

Open source in 1998, multi-core CPU support, Internet, instant messaging, cloud computing applications in 2006

completely different design ideas with object-oriented series of C++, Java, C#

best server platform for Message-based mobile Internet or Internet of Things 

------------------
Erlang/OTP Platform features
------------------

Concurrency, Muti Core, Threads, Massive Processes

Low-Latency

Soft real-time

Fault-tolerant, monitor, link, supervisor tree

Distributed nodes, mnesia

Scalable

Hot Upgrade

------------
Erlang Virtual machine
------------

Similar operating system: CPU Cores, Schedulers, Threads, Massive Processes

Cross-platform(Linux, FreeBSD, AIX, HP-UX, raspberry pie…Windows)

Excellent memory management：process heap, binary, ets…

Fine-grained Garbage Collected

Lightweight process, fair scheduling

-----------------
Erlang Programming language(1)
------------------

Functional Programming

Pattern Match

Lightweight Processes

Message Passing

Recursion, Tail Recursion

Actor-Oriented, Object-Oriented?

Atom, Pid, Tuple, List, Binary, Port…

List, Binary Comprehension::

    List, Binary Comprehension
    << <<(serialise_utf(Topic))/binary, ?RESERVED:6, Qos:2>> || {Topic, Qos} <- Topics >>;

    routes(Topics, Pid) ->
        lists:unzip([{{Topic, Pid}, {Pid, Topic}} || Topic <- Topics]).

Binary Match analytic network protocol

Closure) and HigherOrder Functions

Parameterized Module::

    new(Sock, SockFun, Opts) ->
       {?MODULE, [Sock, SockFun, parse_opt(Opts)]}.

--------------
Erlang/OTP Platform
--------------

OTP(Open Telecom Platform)
Behaviours::

    gen_server(Client server)
    gen_fsm(Finite state automaton)
    gen_event(Event notification)

Supervisor::
     Supervisor Restart Strategies
     one_for_all
     one_for_one
     rest_for_one
     simple_one_for_one

Applications::

    TREE IMAGE

Releases::

    ERTS + Boot脚本 + Applications  => Binary Package

