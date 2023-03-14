# Hiredis
[[عربي]](readme.ar.md)

This library is a binding of Hiredis to Alusus. Hiredis is a library to work with Redis which is an in-memory key-value storage.

## Adding to the Project

Add the library to the project using the following statements:
```
import "Apm";
Apm.importFile("Alusus/Hiredis");
```

## Example

```
import "Srl/Console";
import "Srl/System";
import "Apm";
Apm.importFile("Alusus/Hiredis");

func main {
    use Srl;

    // The variables we need to connect and communicate with redis
    def c: ptr[Hiredis.Context] = 0;
    def reply: ptr[Hiredis.Reply];

    // connect to redis on the specified port
    c = Hiredis.connect("0.0.0.0", 6379);

    // check if the connection succeed or not
    if c == null {
        Console.print("Connection error: can't allocate redis context\n");
        System.exit(1);
    } else if c~cnt.err != 0 {
        Console.print("Connection error: %s\n", c~cnt.errStr~ptr);
        System.exit(1);
    }

    // execute the command SET which put the value `hellp world` into the key foo
    reply = Hiredis.command(c, "SET %s %s", "foo", "hello world");
    Console.print("SET: %s\n", reply~cnt.str);
    // free the memory that we used to hold the reply
    Hiredis.free(reply);

    // Execute the command GET that fetch the value stored in the key `foo`
    reply = Hiredis.command(c, "GET %s", "foo");
    Console.print("GET foo: %s\n", reply~cnt.str);
    Hiredis.free(reply);

    // Execute the command DEL that delete the key `numbers` from the redis
    // we need this to ensure that no old values are still there.
    reply = Hiredis.command(c, "DEL numbers");
    Hiredis.free(reply);

    // Execute the command RPUSH that puts the value to the right of
    // the numbers list
    def i: int;
    for i = 0, i < 4 , ++i {
        reply = Hiredis.command(c, "RPUSH numbers %d", i);
        Hiredis.free(reply);
    }

    // Execute the commandd LRANGE that fetch the list values in the
    // specified range, -1 means the end of the list
    reply = Hiredis.command(c, "LRANGE numbers 0 -1");
    Console.print("numbers: ");
    for i = 0, i < reply~cnt.elementsCount , ++i {
        def p: ptr[ptr[Hiredis.Reply]];
        // move the pointer to the desired location
        p = reply~cnt.elements + i;
        Console.print(" %s", p~cnt~cnt.str);
    }
    Console.print("\n");
    Hiredis.free(reply);

    Hiredis.free(c);

}

main();
```

## Functions and Types

### Context

```
class Context {
    def err: int; /* Error flags, 0 when there is no error */
    def errStr: array[char, 128]; /* String representation of error when applicable */
};
```

This type is used to connect to redis and execute the commands on it.

`err` represents the error code, it has the value 0 when there is no error.

`errStr` represents the error message if any.

### Reply

```
class Reply {
    def tp:  int; /* REDIS_REPLY_* */
    def integer: int[64]; /* The integer when type is REDIS_REPLY_INTEGER */

    def strLen: int[64]; /* Length of string */
    def str: ptr[char]; /* Used for REDIS_REPLY_ERROR, REDIS_REPLY_STRING
                            REDIS_REPLY_VERB, REDIS_REPLY_DOUBLE (in additional to dval),
                            and REDIS_REPLY_BIGNUM. */

    def elementsCount: int[64]; /* number of elements, for REDIS_REPLY_ARRAY */
    def elements: ptr[ptr[Reply]]; /* elements vector for REDIS_REPLY_ARRAY */
};
```

This type represents the reply that we get after executing a command.

`tp` represents the reply type.

`integer` holds the reply value if it is of integer type.

`str` holds the reply value if it is of textual type.

`elementsCount` represents the number of elements in the list if the reply has vector type.

`elements` holds the list of values if the reply has vector type.

### connect

```
@expname[redisConnect]
func connect(host: ptr[char], port: int): ptr[Context];
```
This function is used to connect to redis.

`host` the host address.

`port` the port that we can access redis through it.

It returns a pointer to the context that we can use to manage this connection if it is succeed,
or a pointer to null if the connection does not succeed.

### command

```
@expname[redisCommand]
func command(c: ptr[Context], format: ptr[char], ... any): ptr[Reply];
```
This function is used to execute a given command on redis, like setting or getting a value.

`c` the context variable that manages the connection to redis.

`format` the format of the command we want to execute.

`any` any arguments that the format needs.

### free

```
@expname[freeReplyObject]
func free(reply: ptr[Reply]);
```
This function is used to free the memory used to hold the reply object.

```
@expname[redisFree]
func free(c: ptr[Context]);
```
This function is used to free the memory used to hold the context object.
