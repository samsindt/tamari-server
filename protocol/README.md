# Tamari Protocol

The tamari protocol defines a text encoding that is used by clients to make requests to a server and for a server to respond to clients. A server also uses the protocol when writing to the write-ahead log.

## Specification

A tamari protocol statement always starts with a single byte *operation prefix*. An operation prefix describes the purpose of the statement, including the purpose of a request or the purpose of a response. The available operation prefixes are:  

Symbol | Purpose | Request | Response
--- | --- | --- | ---
\+ | Write value at key | Yes |  
= | Read value at key | Yes | 
\- | Delete value at key | Yes |  
$ | Denote success of request | | Yes  
! | Denote failure of request | | Yes
<br>  

An operation prefix is followed by zero or more *arguments*, depending on the specific operation prefix. Each argument begins with a number denoting the nunber of bytes that the argument body contains. This makes arguments *binary safe*, meaning that string contents do not need to be escaped. In order to delimit the number from the following argument body, the number is followed by the tab escape sequence `\t`. After the tab delimiter is the argument body which is a sequence of bytes. 

After the arguments is the newline escape sequence `\n` to denote the conclusion of the statement.

## Examples
The following examples will assume that arguments are ASCII strings with no null terminator, but could be any byte sequence.
1. A request to write the value *foo* to key *bar*:
    ```
    +3\tbar3\tfoo\n
    ```
2. A request to read the value at key *bar*:
    ```
    =3\tbar\n
3. A request to delete the value at key *bar*:
    ```
    -3\tbar\n
    ```
4. A response from a successful write request:
    ```
    $\n
    ```
5. A response from a succcessful read or delete request to key *bar* (which possesses value *foo*):
    ```
    $3\tfoo\n
6. A response from an unsuccessful read request to key *bar* which does not exist:
    ```
    !3\t10020\tNo value at key: bar\n
    ```