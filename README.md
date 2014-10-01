Dicset
======

A set of dictionaries (mappings)

Written by Vladimir Neverov <sanguini@gmail.com> in 2014

Usage
-----

Constructor takes path to dictionary file as first argument, settings object as second argument.

Dictionary file has an INI-like syntax:

	[dicname]
	from1 	to1 	comment1
	from2	to2		comment2
	"from 3 value"   "to 3 value"    a long and winding comment
	
	[dicname2]
	...

There are two settings recognized by now:

	 - def - a default value that is returned when no dictionary is found
	 - xml2js - boolean, if true - do output in xml2js style (default is old-fashioned non-compatible style :)

Lines starting with # are treated as comments and ignored. Empty lines are ignored.
Columns can be divided with any number of space characters (including tabs, excluding newlines).
'from' and 'to' values can be quoted. In this case they can contain spaces. The following characters
are recognized as quotes: ", ' and `

The constructor reads the file and returns an object with several lookup methods. It can throw an exception if
either a dictionary file couldn't be read or is malformed (not INI-like syntax).

 - .lookup method maps values from the first column to the second one, returns object (syntax depends on xml2js option)
 - .reverse method maps values from the second column to the first one, returns object
 - .comment method maps comments to 'from' and 'to' values (returns object { from: ..., to: ... }), returns object
 - .ilookup, .ireverse and .icomment methods perform case-insensitive lookups, they return objects

 - .to_id method do a reverse lookup (by 'to' field) and return ID only ('from' field)
 - .from_id method do a straight lookup (by 'from' field) and return ID onlu
 - .to_comment and .from_comment methods do lookups by 'to' and 'from' fields respectively and return a text comment

Example:

	var dic = new Dicset('/path/to/file');
	console.log(dic.lookup('metro', 15));

This will lookup a 'metro' dictionary for value 15 and return an object like this (in case when xml2js option is false):

	{
	 	$id: 25,
	 	comment: 'Central station'
	}

With xml2js option set the output will be:

	{
	 	$: {
			id: 25
		},
	 	comment: 'Central station'
	}

If either dictionary is missing or a value is not found, a default value (undefined by default) is returned.

N.B.! If a lookup result (for either 'from' and 'to' lookups) is zero, this is treated as special case and undefined is returned. You can mark records
that do not currently have a mapping with zero.

