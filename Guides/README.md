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
