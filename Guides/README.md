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
* **Combinig Messages leads to bloat.**
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
# Scalar Value Types
# Optional Fields And Default Values
# Enumerations
## Reserved Values
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
