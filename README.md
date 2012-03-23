JSONF
=====

Introduction
------------

JSONF (_JavaScript Object Notation Form_) is a metalanguage for describing data structures using a pseudo-JSON syntax.

Some examples of documentation where JSONF can be used:

* APIs (input and output data structures)
* JSON files
* Internal data structures (inside doc-comments)

Although JSONF extends JSON's syntax, it can be used with other languages with similar constructs.

Values
------

All JSON values can be used as they normally are: `null`, `true`, `false`, numbers, strings, arrays, and objects.  All JSON values can be represented identically in JSONF.

Expressions
-----------

Wherever a value can be used in JSON, an expression can be used instead.

JSONF is not a data structure -- it is a description of a data structure.  Given a valid JSON object, we can evaluate a JSONF specification and determine whether or not that object complies with the specification.

An expression can be evaluated to produce a set of allowed JSON values for a particular part of a data structure.

Expressions consists of _atoms_ and _operators_, like any mathematical expression.  Unless otherwise specified, atoms include values, classes, and textual descriptions and the _alternate_ operator is the only permitted operator.  Array and objects allow for more complicated expressions by allowing quantifiers to be used.

Many of these terms are described later in the specification.

### Alternates

The _alternate_ operator is a binary operator that behaves like a logical "or".

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>true / false</code></td>
		<td><code>true</code> or <code>false</code>.</td>
	</tr>
	<tr>
		<td><code>"a" / "b" / "c"</code></td>
		<td><code>"a"</code> or <code>"b"</code> or <code>"c"</code>.</td>
	</tr>
</table>

### Quantifiers

Quantifiers are unary operators used to indicate repetition.  These operators are only available to arrays and objects.

<table>
	<tr>
		<th>Quantifier</th>
		<th>Repetition</th>
	</tr>
	<tr>
		<td><code>*</code></td>
		<td>Zero or more times.</td>
	</tr>
	<tr>
		<td><code>+</code></td>
		<td>One or more times.</td>
	</tr>
	<tr>
		<td><code>?</code></td>
		<td>Optional.  Zero or one time.</td>
	</tr>
	<tr>
		<td><code>{n, m}</code></td>
		<td><var>n</var> to <var>m</var> times.</td>
	</tr>
	<tr>
		<td><code>{n}</code></td>
		<td>Exactly <var>n</var> times.</td>
	</tr>
	<tr>
		<td><code>{-n}</code></td>
		<td>Zero to <var>n</var> times.</td>
	</tr>
	<tr>
		<td><code>{n+}</code></td>
		<td><var>n</var> or more times.</td>
	</tr>
</table>

Textual description
-------------------

If a set of values cannot be represented by JSONF's syntax or is more conveniently expressed another way, a textual description can be provided.  The syntax for this is to surround a human-readable description using backticks.

These descriptions are atoms in JSONF expressions and can be used like any regular JSON value.

JSONF does not define any convention for these descriptions.  It is up to the reader to interpret these descriptions, so care should be taken to not introduce ambiguities.

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>`odd integers`</code></td>
		<td><code>1</code>, <code>3</code>, <code>5</code>, etc.</td>
	</tr>
	<tr>
		<td><code>`current time`</code></td>
		<td>The current time.</td>
	</tr>
</table>

Classes
-------

Classes represent a set of values.  In an expression, they are considered an atom and can be used like any regular JSON value.

The syntax of classes is similar to assigning an expression to a variable.

	<class name> = <JSONF expression>

### Core classes

<table>
	<tr>
		<th>Class</th>
		<th>Description</th>
	</tr>
	<tr>
		<td><code>ANY</code></td>
		<td>All JSON values.</td>
	</tr>
	<tr>
		<td><code>STRING</code></td>
		<td>All string values.</td>
	</tr>
	<tr>
		<td><code>DATE</code></td>
		<td>All ISO 8601 date strings.</td>
	</tr>
	<tr>
		<td><code>TIME</code></td>
		<td>All ISO 8601 time strings.</td>
	</tr>
	<tr>
		<td><code>DATE_TIME</code></td>
		<td>All ISO 8601 date and time strings.</td>
	</tr>
	<tr>
		<td><code>NUMBER</code></td>
		<td>All integers or floating-point numbers.</td>
	</tr>
	<tr>
		<td><code>INTEGER</code></td>
		<td>All integer values.</td>
	</tr>
	<tr>
		<td><code>FLOAT</code></td>
		<td>All floating-point numbers.</td>
	</tr>
	<tr>
		<td><code>OBJECT</code></td>
		<td>All objects.</td>
	</tr>
	<tr>
		<td><code>ARRAY</code></td>
		<td>All arrays.</td>
	</tr>
</table>

_Note:_ `ARRAY` and `OBJECT` may contain any value as elements.  This means they both allow nested arrays and objects.

### Language-specific classes

JSONF defines additional classes for language-specific values or constructs.  All of these classes may not correspond to a JSON-serializable object and are strictly intended for documenting internal data structures.

<table>
	<tr>
		<th>Class</th>
		<th>Description</th>
	</tr>
	<tr>
		<td><code>FUNCTION</code></td>
		<td>All functions.</td>
	</tr>
	<tr>
		<td><code>NONE</code></td>
		<td>Python's <code>None</code> value.</td>
	</tr>
</table>

### Custom classes

Custom classes can be defined.  This allows you to reuse expressions throughout a specification (or multiple specifications), to simplify and improve clarity.

To redefine a class, including any class predefined by JSONF, simply define a class with the same name.

**Convention:** Classes should be named in "UPPER_CASE" format (upper-case
letters, numbers, or underscores).

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>BOOL_INT = BOOLEAN / INTEGER</code></td>
		<td><code>true</code>, <code>false</code>, or any integer.</td>
	</tr>
	<tr>
		<td><code>CHOICE = "a" / 1</code></td>
		<td><code>"a"</code> or <code>1</code>.</td>
	</tr>
	<tr>
		<td><code>RANGE = `integers &gt; 5`</code></td>
		<td>All integers greater than 5.</td>
	</tr>
</table>

### Class inheritance

The concept of extending or merging an object is simple.  Given two objects, <var>X</var> and <var>Y</var>, the result of merging <var>X</var> and <var>Y</var> is an object with:

* Every pair from <var>X</var> and <var>Y</var> that has a key unique to both objects.
* Every pair from <var>Y</var> that has a key also found in <var>X</var>.

Like objects, we can extend or inherit classes that specify a single object.  The syntax for this is:

	<class name> = <base class> + OBJECT

Multiple base classes can be extended, from left to right:

	<class name> = <base class 1> + <base class 2> + ... + <base class n> + OBJECT

**Convention:** If the sole purpose of a class is to be inherited by other classes, its name should be prefixed with `BASE_`.  These classes would be similar to abstract base classes in object-oriented programming.

#### Examples

Suppose we define this class:

	RESPONSE = {
		"success": true,
		"messages": STRING
	}

The following is a class that extends the `RESPONSE` class:

	ERROR = RESPONSE + {
		"success": false,
		"code": STRING,
	}

This class will override the `"success"` value to be `false` and will add a `"code"` value to the object.  `"messages"` is inherited from the `RESPONSE` class.  This is an example of an object that matches the `ERROR` class:

	{
		"success": false,
		"code": "any error code here",
		"message": "response message here"
	}

For comparison, this is an example of an object matched by the `RESPONSE` class:

	{
		"success": true,
		"message": "any response message"
	}

Sometimes it may make sense to define an abstract base class, if future expansions are anticipated to a JSONF specification:

	BASE_RESPONSE = { "success": BOOLEAN, "messages": STRING }
	SUCCESS = BASE_RESPONSE + { "success": true }
	ERROR = BASE_RESPONSE + { "success": false, "code": STRING }

Arrays
------

An element of an array can be any JSONF expression.

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>[ "a", INTEGER ]</code></td>
		<td><code>["a", 0]</code>, <code>["a", 1]</code>, <code>["a", 2]</code>, etc.</td>
	</tr>
</table>

In addition, a new atom is available in these expressions: tuples.

### Tuples

A tuple is an array-specific atom that is used to represent multiple elements.  Their representation is a parenthesized, comma-separated list of JSONF expressions.  Tuples can be nested.  When an array is evaluated, tuples are "flattened" to the nearest, outer array.

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>[ "a" / "b", "c" ]</code></td>
		<td><code>["a", "c"]</code> or <code>["b", "c"]</code>.</td>
	</tr>
	<tr>
		<td><code>[ "a" / ("b", "c") ]</code></td>
		<td><code>["a"]</code> or <code>["b", "c"]</code>.</td>
	</tr>
	<tr>
		<td><code>[ "a", [ 1 / (2, 3) ] ]</code></td>
		<td><code>["a", [1]]</code> or <code>["a", [2, 3]]</code>.</td>
	</tr>
</table>

### Quantifiers

Quantifiers can be applied to any atom within array element expressions.

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>[ INTEGER+ ]</code></td>
		<td>All arrays consisting of only integers and at least one element.</td>
	</tr>
	<tr>
		<td><code>[ true? ]</code></td>
		<td><code>[]</code> or <code>[true]</code>.</td>
	</tr>
	<tr>
		<td><code>[ 1{3}, 2{2}, 3 ]</code></td>
		<td><code>[1, 1, 1, 2, 2, 3]</code> only.</td>
	</tr>
	<tr>
		<td><code>[ INTEGER+, STRING+ ]</code></td>
		<td><code>[1, "a"]</code>, <code>[1, "a", "b"]</code>, <code>[1, 2, "a"]</code>, etc.</td>
	</tr>
	<tr>
		<td><code>[ INTEGER / STRING+ ]</code></td>
		<td><code>[1]</code>, <code>[2]</code>, <code>["a"]</code>, <code>["a", "b"]</code>, etc.</td>
	</tr>
	<tr>
		<td><code>[ (INTEGER / STRING)*, 5 ]</code></td>
		<td><code>[5]</code>, <code>[1, 5]</code>, <code>[1, "a", 5]</code>, <code>["a", "b", 3, 5]</code>, etc.</td>
	</tr>
	<tr>
		<td><code>[ `integers between 1 and 5`+ ]</code></td>
		<td><code>[1]</code>, <code>[1, 5, 3]</code>, <code>[1, 2, 3, 4, 5]</code>, etc.</td>
	</tr>
</table>

Objects
-------

Keys and values of an object can be any JSONF expression.

Be aware that some languages restrict what types of values can be used as keys, but JSONF does not enforce a restriction.

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>{ "a": INTEGER }</code></td>
		<td><code>{"a": 1}</code>, <code>{"a", 20}</code>, etc.</td>
	</tr>
	<tr>
		<td><code>{ STRING: true }</code></td>
		<td><code>{"a": true}</code>, <code>{"b": true}</code>, etc.</td>
	</tr>
</table>

However, this only limits us to expressing fixed-sized objects.  With additional syntax, we can express optional key/value pairs or an object whose keys must follow some pattern.

### Groups

It may be helpful to take a step back and analyze the object syntax.  Objects are very similar to arrays: they are comma-separated lists where each element is a pair consisting of a key and a value.

	{ "a": 1, "b": 2, "c": 3 }
	  ^^^^^^  ^^^^^^  ^^^^^^
	   pair    pair    pair

For objects, the only atoms permitted in expressions are key/value pairs and groups.  In addition, operators are only permitted to be used with groups.

A group is an object-specific atom that is used to represent multiple key/value pairs.  Their representation is similar to tuples: a parenthesized, comma-separated list of JSONF expressions.  They can be nested.  Groups are "flattened" to the nearest, outer object.

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>{ ("a": 1) / ("b": 2, "c": 3) }</code></td>
		<td><code>{"a": 1}</code> or <code>{"b": 2, "c": 3}</code>.</td>
	</tr>
	<tr>
		<td><code>{ "a": 1, "b": { ("c": 3) / ("d": 4) } }</code></td>
		<td><code>{"a": 1, "b": {"c": 3}}</code> or <code>{"a": 1, "b": {"d": 4}}</code>.</td>
	</tr>
</table>

### Operator restriction

The reasoning behind only allowing operators with groups is to improve clarity, since values are also expressions.  Consider the following:

<table>
	<tr>
		<th>Without restriction:</th>
		<td><code>{ "a": 1 / 2 / "b": 2 }</code></td>
	</tr>
	<tr>
		<th>With restriction:</th>
		<td><code>{ ("a": 1 / 2) / ("b": 2) }</code></td>
	</tr>
</table>

In the second example, it is much easier to distinguish the alternate values from the alternate key/value pairs.

### Ordering

JSONF does not require key/value pairs to be in any particular order.

### Quantifiers

Quantifiers can only be applied to groups within object-based expressions.  Again, this restriction is to be explicit that the quantifier applies to key/value pairs and not a value.

<table>
	<tr>
		<th>Example</th>
		<th>Matched values</th>
	</tr>
	<tr>
		<td><code>{ (STRING: INTEGER)+ }</code></td>
		<td><code>{"a": 1}</code>, <code>{"b": 2, "c": 3}</code>, etc.</td>
	</tr>
	<tr>
		<td><code>{ "a": 1, ("b": 2, "c": 3)?, "d": 4 }</code></td>
		<td><code>{"a": 1, "d": 4}</code> or <code>{"a": 1, "b": 2, "c": 3, "d": 4}</code>.</td>
	</tr>
</table>

Documentation
-------------

A documentation block can be inserted to document a particular expression.  These blocks are placed on a new line directly below the value and are indented at least one level inward.

	[ INTEGER+ ]
		Array of user IDs.

Documentation blocks can also be optionally prefixed with `//`.  This is usually useful for presenting JSONF text with JavaScript or JSON syntax highlighting.

```javascript
{
	"code": INTEGER
		// Error code value.
}
```

### Ambiguity

Since a documentation block documents the line (or lines) directly above it, it is possible for a block to be referring to multiple values or expressions.

For clarity, it is recommended to either:

1. Be explicit about what you are documenting in the preceding lines, if you are not documenting the whole line.
2. Break up the expression into multiple lines.

### Key/value pairs

If a key/value pair is being documented, the first line of the documentation may begin with:

	<key : value>

`key` is a short description of the key and `value` is a short description of the value.  This is especially useful when used in combination with classes to further describe the data stored in the object.  For example:

	{
		(STRING: INTEGER)*
			<username : unique ID>
			User and document assignments.
	}
