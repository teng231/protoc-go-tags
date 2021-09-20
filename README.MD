# A helpers for Golang

`protoc-go-tags` can be used to specify additional
go struct tags in the proto definitions. This can be used to specify database
column names or validation options. It is also possible to replace existing tags,
this can be used specifically to overwrite json tags.

Please note that we actually parse the go code and modify the AST (abstract syntax tree)
to add your additional go tags. This should be somewhat more reliable than
regular expressions alone.

Struct tags must be placed on a separate comment line, but can contain multiple
tags.

```
// proto definitions
message StarfleetShip {
	string name = 1;

	// use a different db column name for the departure time
	// `db:"we_are_leaving_at"`
	dkfbasel.protobuf.Timestamp departure_time = 4;
}

// go struct definition
type StarfleetShip struct {
	Name string `protobuf:"bytes,1,opt,name=name" json:"name,omitempty" `

	// use a different db column name for the departure time
	// `db:"we_are_leaving_at"`
	DepartureTime *dkfbasel_protobuf.Timestamp `protobuf:"bytes,4,opt,name=departure_time,json=departureTime" json:"departure_time,omitempty" db:"we_are_leaving_at"`
}
```

To add the additional tags to your structs proceed as following - a complete
example can be found in the directory `example`.

1. Add the tag as comment above the respective property. Unfortunately the tag
can currently not be placed on the same line, as the protoc grpc plugin will
strip out all comments that are on the same line.

2. Run the protoc generator with the grpc plugin.

3. Run the protoc-go-tags utility on the generated files. You need to specify
the directory that the utility should process and it will recursively scan
all files and transform all go files with the ending .pb.go

4. The additional tags should now be included in your .pb.go files


# How to use

1. Installing

```bash
cd
go get github.com/teng231/protoc-go-tags
go install github.com/teng231/protoc-go-tags
```

2. Using with command

```bash
protoc-go-tags --dir=<your-folder-has-code>
```
