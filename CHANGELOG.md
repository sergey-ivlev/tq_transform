0.3.0 Changelog
=====

Features:
---

 - New ability to handle compile options in create_model plugin callback
 - Now you can set from/to type converter and validators via **tq_converter_rules** compile time option
	(see /priv/converter.map)
 - from/to converters can be set as list of functions

Breaking Changes
---
 - plugin callback ```create_model/1``` moved to ```create_model/2```
 - plugin callback ```normalize_fields/1``` moved to ```normalize_fields/2```
