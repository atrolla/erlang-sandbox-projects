#HSLIDE

##Erlang : Reactive Microservices since 1987

Yvan VU @JeSuisSocial

Arnauld LOYER @aloyer

#HSLIDE

## A BRIEF HISTORY

#VSLIDE

#### The beginning

*Stockholm, Sweden*

1985-1986 : Researches in Ericsson's Computer Science Laboratory

*LISP, ADA, ML, SmallTalk, Prolog, Parlog...*

#VSLIDE

#### Constraints

- Handling a very large number of concurrent activities
- High Availability
- Systems distributed over several computers
- Very large software systems
- Continuous operation over several years
- Software maintenance without stopping the system
- Stringent quality and reliability
- Fault tolerance

#VSLIDE

#### Erlang's birth
- 1986 : NO Existing language suits Telecom constraints -> Need to create a new one
- 1987 : Erlang is prototyped by Joe Armstong, Robert Virding and Mike Williams
- 1987 : Internal project based on Erlang -> First users (team of 3) and rapid changes made based on their needs
- 1989 : Project done -> average increase in productivity factor of 8

#VSLIDE

#### The growth
- 1995 : AXE-N (Large Project) collapsed -> new AXD project rewritten in Erlang -> OTP
- ->1997 : Expansion -> Performance, Community, Distributed, Projects, BEAM VM, Mnesia storage, High-Performance, more FP
- 1998 : AXD301 Project announced, Open-source Erlang following Ericsson's ban
- Today : Amazon SimpleDB, Ejabberd, RabbitMQ, WhatsApp

#HSLIDE

## BASIC COMMANDS

#VSLIDE

#### REPL, SHELL & FILE
Module & Module attributes

#VSLIDE

#### TYPES (OR NOT)

- Variables
- Numbers
- List
- Atom
- Map
- Record
- Macro (Constants here)

#VSLIDE

#### PATTERN MATCHING

```erlang
functionName(X) ->
    case X of %% example using case of
        {tuple} -> yo;
        [Head|Tail] -> Tail;
        42 -> "That's the meaning of life.";
        true -> false;
        _ -> 0 %% when nothing matches
    end.
```

#VSLIDE

#### MESSAGE PASSING

```erlang
ProcessPid = spawn(?MODULE, exportedFunction, [Arg1,Arg2]).

exportedFunction(Arg1, Arg2) ->
    receive
        stop -> ok;
        {From, "Hello", Ref} -> From ! {self(),"Hello to you too !",Ref}
    end.
    
flush() -> %% flush all messages from queue
    receive
        _ -> flush() %% message is ignored by loopping
    after
        0 -> true %% when queue is empty, time-out after 0s to get out of the loop
    end.
```

#VSLIDE

#### REBAR3 & EUNIT

```erlang
-include_lib("eunit/include/eunit.hrl").

function_must_ends_with_test() ->
    ?assert(true). % self-explanatory
    ?assertEqual(expected, actual).
```

#HSLIDE

## PANDEMIC GAMEBOARD

#VSLIDE

![Image-Relative](https://github.com/Arnauld/jam201609/raw/master/doc/pandemic-game-board.png)

#VSLIDE

##Simple Rules for start
- a City starts with no infection
- when a city is infected, the infection level for that disease increase by 1
- UNLESS the infection level for that disease is already at 3 : the disease outbreaks

#VSLIDE

![Image-Relative](https://github.com/Arnauld/pandemic-erlang-hands-on/raw/master/doc/webfront.png)

#HSLIDE

## BEFORE CODING

#VSLIDE

#### INSTALL ERLANG & REBAR
*Then launch an erl shell*

#VSLIDE

#### CLONE HANDS-ON REPO
*And build it*

#VSLIDE

#### CONNECT TO THE OTHER NODES
*may need FIREWALLS configuration*

EPMD port : 4369 & arbitrary ports range (ex. 16090-16199)
```shell
> erl -name mynodenade@127.0.0.1 \
-kernel inet_dist_listen_min 16090 \ 
-kernel inet_dist_listen_max 16199 \
-setcookie passw0rd
```
```erlang
net_kernel:connect_node('othernodename@192.168.0.1').
```

#HSLIDE

## TIME TO CODE !

#VSLIDE

## City
City has a color and a list of neighbours, protocol is now : 
- {infected, CityName, NewDiseaseLevel}
- {outbreak, CityName, Neighbours}

City can initiate its Disease Color at a certain level.

#VSLIDE

## City is now a process !
- infect is called on {infect, From, DiseaseColor}
- returns InfectionsLevels map on {infections_level, From} :
    {ok, InfectionsLevels, CityName, CityColor}