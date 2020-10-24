* Describes how to use the protocl buffer language to structure your protocol buffer data, including `.proto` file syntax and how to generate code from `.proto` files.
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
* You also cannot use 19000 through 19999 (`FieldDescriptor::kFirstReservedNumber` through `FieldDescriptor::kLastReservedNumber`). These are reserved for Protocol Buffer implementatio.
* You cannot use previously [reserved](https://github.com/pravsemilo/protocol-buffers-notes/blob/master/Guides/README.md#reserved-fields) field numbers.
## Specifying Field Rules
## Adding More Message Types
## Adding Comments
## Reserved Fields
## What's Generated From Your .proto?
# Scalar Value Types
# Optional Fields And Default Values
# Enumerations
## Reserved Values
# References
* https://developers.google.com/protocol-buffers/docs/overview
