# hello-future
Sending messages into the future

This is a Python script which reads lines of text from the input, and then
creates another Python script which will write out the same messages to the
terminal.

The twist is that the decoding will take the same amount of time (up to a
constant factor) as it took to encode it. You could use this to write a
message, post it publicly, and be sure that nobody will be able to read it
within a certain period of time.

## Installing

`hello-future` requires the following:

 * Python 3 (tested with version 3.4.2)
 * pycrypto (tested with version 2.6.1)

## Using

To encode a message, do this:

```
$ time python3 hello-future.py msg.py
Write your messages one line at a time. They will be replayed in real time
(times a constant factor) by the Python program.

Press ctrl+C to terminate.

> Hi!
> This is my message.
> ^C
Exiting...

real    0m4.633s
user    0m4.584s
sys     0m0.004s
```

Now your message is encoded into the file `msg.py`, and executing this will
print the message:

```
$ time python3 msg.py 
----------------------------- START OF MESSAGE -----------------------------
Hi!
This is my message.
------------------------------ END OF MESSAGE ------------------------------

real    0m35.098s
user    0m34.484s
sys     0m0.464s
```

Decoding then takes a time proportional to the encoding. In my case, decoding
is roughly ten times slower, but this is implementation-dependent.

## Technical details

Messages are encrypted with AES-256, using keys from a chain of SHA-256
hashes. The following pseudocode describes the encoding process:

```python
key = initial_key
while not end_of_input:
    key = sha256(key)
    if has_new_message():
        encrypted_message = aes256(key, get_message())
        save(encrypted_message)
```

Typically, there will be millions or billions of steps between messages.
Since the hash chain can not be parallelized, it does not matter how much
computing power you have access to.

The current Python implementation can decode at a speed of about 100,000
hashes/second on a desktop system, with specialized hardware this could
probably be pushed up to some tens or hundreds of millions of hashes/second,
but it should be nearly impossible to do it much faster than this in the
foreseeable future.

To make this tool more practical one should try to bridge the gap between the
desktop performance and that of specialized hardware, and processors with
built-in  AES and SHA support should be able to come pretty close.

