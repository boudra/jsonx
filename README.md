
JSONX is an Erlang library for efficient decode and encode JSON, written in C.
Works with binaries as strings, arrays as lists and it only knows how to decode UTF-8 (and ASCII).

### Decode (json -> erlang)

 - null   -> atom null
 - true   -> atom true
 - false  -> atom false
 - string -> binary
 - number -> number
 - array  -> list
 - object -> {struct, PropList}, optional eep18 or proplist.

### Encode (erlang -> json)

 - atom null 	      -> null
 - atom true 	      -> true
 - atom true 	      -> false
 - any other atom     -> string
 - binary             -> string
 - number             -> number
 - {struct, PropList} -> object
 - {PropList}         -> object
 - PropList           -> object
 - {json, IOList}     -> include IOList with no validation

### INSTALL and DOC

* cd jsonx
* make
* make doc
* firefox doc/index.html&

### Examples encode json

~~~~~
1>  jsonx:encode([1, 2.3, true, false, null, atom, <<"string">>, []]).
<<"[1,2.3,true,false,null,\"atom\",\"string\",[]]">>

%% Object as proplist
2>  jsonx:encode( [{name, <<"Ivan">>}, {age, 33}, {phones, [3332211, 4443322]}] ).
<<"{\"name\":\"Ivan\",\"age\":33,\"phones\":[3332211,4443322]}">>

%% Object as struct
3>  jsonx:encode( {struct, [{name, <<"Ivan">>}, {age, 33}, {phones, [3332211, 4443322]}]} ).
<<"{\"name\":\"Ivan\",\"age\":33,\"phones\":[3332211,4443322]}">>

%% Object as eep18 propsal
4>  jsonx:encode( {[{name, <<"Ivan">>}, {age, 33}, {phones, [3332211, 4443322]}]} ).
<<"{\"name\":\"Ivan\",\"age\":33,\"phones\":[3332211,4443322]}">>
~~~~~

### Examples encode record to json

~~~~~
-module(x).
-export([encode_records_test/0]).
-record(none, {}).
-record(person, {name :: binary(), age :: number()}).
-record(person2, {name, age, phone}).

encode_records_test() ->
    jsonx:encoder([{none,     record_info(fields, none)},
                   {person,   record_info(fields, person)},
                   {person2,  record_info(fields, person2)} ]).
~~~~~

~~~~~
10> c(x).
 {ok,x}
                                                              
11> F = x:encode_records_test().
 #Fun<jsonx.0.126959313>
                                              
12> F([ {none}, {person,<<"IvanDurak">>,16}, {person2, <<"BabaYaga">>, 116, 6666666} ]).
 <<"[{},{\"name\": \"IvanDurak\",\"age\": 16},{\"name\": \"BabaYaga\",\"age\": 116,\"phone\": 6666666}]">>
~~~~~