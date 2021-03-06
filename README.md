ZGrab 2.0
=========

This repo contains the new ZGrab framework, and will eventually replace https://github.com/zmap/zgrab.

## Building

You will need to have a valid `$GOPATH` set up, for more information about `$GOPATH`, see https://golang.org/doc/code.html.

Once you have a working `$GOPATH`, run:

```
$ go get github.com/zmap/zgrab2
```

This will install zgrab under `$GOPATH/src/github.com/zmap/zgrab2`

```
$ cd $GOPATH/src/github.com/zmap/zgrab2
$ make
```

## Single Module Usage 

ZGrab2 supports modules. For example, to run the ssh module use

```
./zgrab2 ssh
```

Module specific options must be included after the module. Application specific options can be specified at any time.

## Multiple Module Usage

To run a scan with multiple modules, a `.ini` file must be used with the `multiple` module. Below is an example `.ini` file with the corresponding zgrab2 command. 

***multiple.ini***
```
[Application Options]
output-file="output.txt"
input-file="input.txt"
[http]
name="http80"
port=80
endpoint="/"
[http]
name="http8080"
port=8080
endpoint="/"
[ssh]
port=22
```
```
./zgrab2 multiple -c multiple.ini
```
`Application Options` must be the initial section name. Other section names should correspond exactly to the relevant zgrab2 module name. The default name for each module is the command name. If the same module is to be used multiple times then `name` must be specified and unique. 

## Adding New Protocols 

Add module to modules/ that satisfies the following interfaces: `Scanner`, `ScanModule`, `ScanFlags`.

The flags struct must embed zgrab2.BaseFlags. In the modules `init()` function the following must be included. 

```
func init() {
    var newModule NewModule
    _, err := zgrab2.AddCommand("module", "short description", "long description of module", portNumber, &newModule)
    if err != nil {
        log.Fatal(err)
    }
}
```

### Output schema

To add a schema for the new module, add a module under schemas, and update [`schemas/__init__.py`](schemas/__init__.py) to ensure that it is loaded.

See [schemas/README.md](schemas/README.md) for details.

### Integration tests
To add integration tests for the new module, run `integration_tests/new.sh [your_new_protocol_name]`.
This will add stub shell scripts in `integration_tests/your_new_protocol_name`; update these as needed.
See [integration_tests/mysql/*](integration_tests/mysql) for an example.
The only hard requirement is that the `test.sh` script drops its output in `$ZGRAB_OUTPUT/[your-module]/*.json`, so that it can be validated against the schema.

#### How to Run Integration Tests

To run integration tests, you must have [Docker](https://www.docker.com/) installed. Then, you can follow the following steps to run integration tests:

```
$ go get github.com/jmespath/jp && go build github.com/jmespath/jp
$ pip install --user zschema
$ make integration-test
```

Running the integration tests will generate quite a bit of debug output. To ensure that tests completed successfully, you can check for a successful exit code after the tests complete:

```
$ echo $?
0
```

## License
ZGrab2.0 is licensed under Apache 2.0 and ISC. For more information, see the LICENSE file.
