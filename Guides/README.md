* Describes how to use the protocol buffer language to structure your protocol buffer data, including `.proto` file syntax and how to generate code from `.proto` files.
* Covers **proto2** version.
# Defining A Message Type
* Each field has a name and type.
```protobuf
message SearchRequest {
	required string query = 1;
	optional int32 page_number = 2;
	optional int32 result_per_page = 3;
}
```
## Specifying Field Types
* You can specify [scalar types](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/README.md#scalar-value-types), composites types, including [enumerations](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/README.md#enumerations) and other message types.
## Assigning Field Numbers
* Each field in message definition has a **unique number**.
* These numbers are used to identify fields in the [message binary format](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/Encoding.md).
* Numbering shouldn't be changed once your message type is in use.
* Field numbers in range 1 through 15 take one byte to encode field number and its type.
	* Reserve field numbers 1 through 15 for frequently occuring message elements.
	* Also leave some room for frequently occuring element that might be added in future.
* Field numbers in range 16 through 2047 take two bytes.
* The smallest field number you can specify is 1, and the largest is 2^29 - 1, or 536,870,911.
* You also cannot use 19000 through 19999 (`FieldDescriptor::kFirstReservedNumber` through `FieldDescriptor::kLastReservedNumber`). These are reserved for Protocol Buffer implementation.
* You cannot use previously [reserved](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/README.md#reserved-fields) field numbers.
## Specifying Field Rules
* Message fields are one of following : 
	* `required` : Exactly 1.
	* `optional`: 0 or 1.
	* `repeated` : 0 or more.
* `repeated` fields of scalar numeric types aren't encoded efficiently. Instead use the special option `[packed=true]`.
```protobuf
repeated int32 samples = 4 [packed=true];
```
* **Required is forever.**
	* Be very careful about marking fields as `required`.
	* Instead consider writing application specific custom validation routines.
## Adding More Message Types
* Multiple message types can be defined in a single `.proto` file.
```protobuf
message SearchRequest {
	required string query = 1;
	optional int32 page_number = 2;
	optional int32 result_per_page = 3;
}

message SearchResponse {
	...
}
```
* **Combining Messages leads to bloat.**
	* While multiple message types (message, enum and service) can be defined in a single `.proto` file, it can lead to dependency bloat when large number of messages with varying dependencies are defined in a single file.
	* It is recommended to include as few message types per `.proto` file.
## Adding Comments
```protobuf
/* SearchRequest represents a search query, with pagination options to
 * indicate which results to include in the response. */

message SearchRequest {
	required string query = 1;
	optional int32 page_number = 2;  // Which page number do we want?
	optional int32 result_per_page = 3;  // Number of results to return per page.
}
```
## Reserved Fields
* If you [update](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/README.md#updating-a-message-type) by removing or commenting, future users can reuse the field number.
* This can cause compatibility issues when dealing with old versions of `.proto` files.
* To avoid this, mark your deleted fields as `reserved`.
* Field numbers and names can be marked as `reserved`.
* Field names and numbers cannot be mixed in the same `reserved` statement.
```protobuf
message Foo {
	reserved 2, 15, 9 to 11;
	reserved "foo", "bar";
}
```
## What's Generated From Your **.proto**?
* On running the [protocol buffer compiler](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/README.md#generating-your-classes) on a `.proto` file :
	* The compiler generates code in your chose language.
		* Getting and setting field values.
		* Serializing messages to an output stream.
		* Parsing messages from an input stream.
# Scalar Value Types
* `double`
* `float`
* `int32`
	* Uses variable-length encoding.
	* Inefficient for encoding negative numbers. Use sint32 instead.
* `int64`
	* Uses variable-length encoding.
	* Inefficient for encoding negative numbers. Use sint64 instead.
* `uint32`
	* Uses variable-length encoding.
* `uint64`
	* Uses variable-length encoding.
* `sint32`
	* Uses variable-length encoding.
* `sint64`
	* Uses variable-length encoding.
* `fixed32`
	* Always four bytes.
	* More efficient than uint32 if values are greater than 2^28.
* `fixed64`
	* Always eight bytes.
	* More efficient than uint64 if values are greater than  2^56.
* `sfixed32`
	* Always four bytes.
* `sfixed64`
	* Always eight bytes.
* `bool`
* `string`
* `bytes`
# Optional Fields And Default Values
* A well-formed message may or may not contain an optional element.
* When a message is parsed, if it doesn't contain an optional element, the corresponding field in the parsed object is set to the default value for that field.
* Default value can be provided as part of message description.
```protobuf
optional int32 result_per_page =3 [default = 10];
```
* If the default value is not specified for an optional element, a type-specific default value is used instead.
	* `string` : Empty string
	* `bytes` : Empty byte string
	* `bool` : false
	* Numeric Types : Zero
	* Enums : First values listed in enum's type definition.
# Enumerations
```protobuf
message SearchRequest {
	required string query = 1;
	optional int32 page_number = 2;
	optional int32 result_per_page = 3 [default = 10];
	enum Corpus {
		UNIVERSAL = 0;
		WEB = 1;
		IMAGES = 2;
		LOCAL = 3;
		NEWS = 4;
		PRODUCTS = 5;
		VIDEO = 6;
	}
	optional Corpus corpus = 4 [default = UNIVERSAL];
}
```
* Aliases allow to assign same value to different enum constants.
	* To do this set the `allow_alias` option to `true`.
	* Otherwise compiler will generate an error message.
```protobuf
enum EnumAllowingAlias {
	option allow_alias = true;
	UNKNOWN = 0;
	STARTED = 1;
	RUNNING = 1;
}
```
* Enumerator constants must be in the range of a 32-bit integer.
* Since `enum` values use [varint encoding](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/Encoding.md) on the wire, negative values are inefficient and not recommended.
* You can define `enum`s within a message definition or outside. These `enum`s can be reused in any message definition in your `.proto` file.
* You can also use an `enum` type declared in one messae as the type of a field in a different mesage using the syntax `_MessageType_._EnumType_`.
## Reserved Values
```protobuf
enum Foo {
	reserved 2, 15, 9 to 11, 40 to max;
	resrved "FOO", "BAR";
}
```
# Using Other Message Types
## Importing Definitions
## Using proto3 Message Types
# Nested Types
## Groups
# Updating A Message Type
# Extensions
## Nested Extensions
## Choosing Extension Numbers
# Oneof
## Using Oneof
## Oneof Features
## Backwards-compatibility Issues
# Maps
## Maps Features
## Backwards compatibility
# Packages
## Packages and Name Resolution
# Definiing Services
# Options
## Custom Options
# Generating Your Classes
# References
* https://developers.google.com/protocol-buffers/docs/overview
