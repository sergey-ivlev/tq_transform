Erlang opaque types generator
=====

This application provide parse transform for easy generate opaque **Model** from module it defined, with special functions.

Model description
=====

Usage
----

First of all you need to enable parse_transform in module.

```Erlang
-module(my_model).
-compile({parse_transform, tq_record_transform}).
...
```

Next you need to describe fields

Fields
----
```Erlang
-field({FiledName, [{OptionName, OptionValue}]}).
...
```
Field name must be valid atom.

Options:
  - **type**
  - **to_ext** :: Fun | {Mod, Fun} | {Mod, Fun, Args}
  - **from_ext** :: Fun | {Mod, Fun} | {Mod, Fun, Args} | none
  - **mode** :: r | w | rw | sr | sw | srsw | rsw | srw
  - **required** : true | false
  - **default** : any()
  - **default_func** :: Fun | {Mod, Fun} | {Mod, Fun, Args}
  - **get** : true | false | custom
  - **set** : true | false | custom
  - **record** : true | false
  - **validators** : Fun | {Mod, Fun} | {Mod, Fun, Args}

Model
----
```Erlang
-model([{OptionName, OptionValue}]).
...

```

Options:
  - **validators** : Fun | {Mod, Fun} | {Mod, Fun, Args}

Autogenerated functions
=====

new/0
----
Create new model
```Erlang
-spec new() -> Model.
new() -> Model.
```

Getters And Setters
----

```Erlang

%% Getter
-spec FiledName(Model) -> any().
FiledName(Model) -> ... .

%% Setter
-spec set_FiledName(Value :: any(), Model) -> Model.
set_FiledName(Value, Model) -> ... .

```

to_proplist/[1,2]
----

Function convert model to proplist. Only fields which access_mode marked ```r``` are stores in proplist.

```Erlang
-spec to_proplist(Opts, Model) -> [{atom(), any()}, ...] when
  Opts :: [Option],
  Option :: unsafe.

to_proplist(Model) ->
  ... .
to_proplist(Opts, model) ->
  ... .

```
**Opts** :
  - **unsafe** - put variables which access_mode marked as ```sr``` in result proplist too

to_ext_proplist/[1,2]
----

Same as to_proplist, but each argument transforms using to_ext field option function.

```Erlang
-spec to_ext_proplist(Opts, Model) -> [{atom(), any()}, ...] when
  Opts :: [Option],
  Option :: unsafe.

to_ext_proplist(Model) ->
  ... .
to_ext_proplist(Opts, model) ->
  ... .

```
**Opts** :
  - **unsafe** - put variables which access_mode marked as ```sr``` in result proplist too

fields/[2,3]
----
Same as to_proplist, but you need to specify fields, and function verifies field existanse and access mode.

```Erlang
-spec fields(Fields, Opts, Model) -> {ok, Proplist} | {error, Reasons} when
  Fields :: [Field],
  Field :: atom(),
  Proplist :: [{atom(), any()}, ...],
  Opts :: [Option],
  Option :: unsafe | binary_key,
  Reasons :: [{field(), Reason}],
  Reason :: unknown | forbidden.

fields(Fields, Model) ->
  ... .
fields(Fields, Opts, Model) ->
  ... .
```
**Opts**:
  - **unsafe** - put variables which access_mode marked as ```sr``` in result proplist too
  - **binary_key** - Fields are represented as binaries.
  - **ignore_unknown** - ignore unknown options

ext_fields/[2,3]
----
Same as to_ext_proplist, but you need to specify fields, and function verifies field existanse and access mode.

```Erlang
-spec ext_fields(Fields, Opts, Model) -> {ok, Proplist} | {error, Reasons} when
  Fields :: [Field],
  Field :: atom(),
  Proplist :: [{atom(), any()}, ...],
  Opts :: [Option],
  Option :: unsafe | binary_key,
  Reasons :: [{field(), Reason}],
  Reason :: unknown | forbidden.

ext_fields(Fields, Model) ->
  ... .
ext_fields(Fields, Opts, Model) ->
  ... .
```
**Opts**:
  - **unsafe** - put variables which access_mode marked as ```sr``` in result proplist too
  - **binary_key** - Fields are represented as binaries.

from_proplist/[1,2,3]
----
Generates model from proplist with atom keys.

```Erlang
-spec from_proplist(Proplist, Opts, Model) -> {ok, [{atom(), any()}, ...]} | {error, Reasons} when
  Proplist :: [{atom(), any()}, ...],
  Opts :: [Option],
  Option :: unsafe | ignore_unknown,
  Reasons :: [{field(), Reason}],
  Reason :: unknown.

from_proplist(Proplist) ->
  ... .
from_proplist(Proplist, Opts) ->
  ... .
from_proplist(Proplist, Model) ->
  ... .
from_proplist(Proplist, Opts, Model) ->
  ... .
```

from_ext_proplist/[1,2,3]
---

```Erlang
-spec from_ext_proplist(Proplist, Opts, Model) -> {ok, [{atom(), any()}, ...]} | {error, Reasons} when
  Proplist :: [{binary(), binary()}, ...],
  Opts :: [Option],
  Option :: unsafe | ignore_unknown,
  Reasons :: [{field(), Reason}],
  Reason :: unknown.

from_ext_proplist(Proplist) ->
  ... .
from_ext_proplist(Proplist, Opts) ->
  ... .
from_ext_proplist(Proplist, Model) ->
  ... .
from_ext_proplist(Proplist, Opts, Model) ->
  ... .
```

[ToDo] Description

field_from_ext/2
---
```Erlang
-spec field_from_ext(FieldName, Val) -> {ok, FieldVal} | {error, Reason}.
```

Convert value to field type using from_ext function and valid result value with field validators.

[ToDo] Description

valid
----

```Erlang
-spec valid(Model) -> ok | {error, Reasons}.
```

Check that model is valid, by performing each field validator and whole model validators.

get_changed_fields/1
---

```Erlang
-spec get_changed_fields(Model) -> [{FieldName, FieldValue}, ...].
```

Return the list that was changed.

is_new/1
---

```Erlang
-spec is_new(Model) -> true | false.
```

Return true if model is new. Uses at most with db model generators.

is_changed/2
---

```Erlang
-spec is_changed(FieldName, Model) -> true | false.

```

Check that field is changed.

get_field_name/2
---

```Erlang
-spec get_field_name(FieldName, Opts) -> {ok, ModelFieldName} | {error, Reason} when
    FieldName :: binary() | atom(),
    Opts :: [Opt],
    Opt :: binary_key | {mode, Mode},
    Mode :: r | w | rw | sr | sw | srw | rsw | rsrw,
    Reason :: {FieldName, unknown} | [{any(), unknown_option}].
```

Check that field exists in model structure and accessable with 'mode' rights.
Field name must be binary if binary_key option is set.

Example
=======

user.erl

```Erlang
-module(user_model).
-compile({parse_transform, tq_record_transform}).

-field({name,
  [required,
   {type, binary},
   {mode, rw}
  ]}).

-field({age,
  [required,
   {type, integer},
   {mode, rw},
   {validators, [{more_then, [18]}]}
  ]}).

-field({password,
  [{type, binary},
   {mode, srw}
  ]}).

more_then(A, Val) when A > Val ->
  {error, {less_then, A}}.
more_then(A, _Val) ->
  ok.

```

Usage:
```Erlang
1> Data = [{<<"name">>, <<"User1">>}, {<<"password">>, <<"123456">>}, {<<"unknown_data">>, <<"qwerty">>}].
2> {ok, User} = user_model:from_ext_proplist(Data, [ignore_unknown]).
3> User:valid().
{error, [{age, required}]}
4> User2 = User:set_age(12).
5> User2:valid().
{error, [{age, {less_then, 18}}]}
6> User3 = User2:set_age(20).
7> User3:valid().
ok
8> User3:to_proplist().
[{age,20},{name,<<"User1">>}]
9> User3:to_proplist([unsafe]).
[{password,<<"123456">>},{age,20},{name,<<"User1">>}]

```
