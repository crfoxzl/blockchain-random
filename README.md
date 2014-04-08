Get entropy from bitcoin transactions!
======================================

Are you trying to use true randomness in your application, but don't want to use `/dev/random` or [random.org](https://random.org) because they are "too mainstream" or "not edgy enough" for your project? Fear not! With `blockrandom.py`, you can get entropy straight from unconfirmed bitcoin transactions! Now you can develop applications in style, safe with the knowledge that you have the hippest source of true randomness in town.

### How to use it ###

You can use `blockrandom.py` just like Python's standard library `random` module.

```python
>>> import blockrandom
>>> blockrandom.randbytes(5)
bytearray(b'\xff\xdcb\x9c\xee')

>>> blockrandom.randbool()
True

>>> blockrandom.randint(0, 100)
43

>>> blockrandom.random()
0.13928496837615967

>>> blockrandom.uniform(1.5, 2.5)
1.1863149404525757

>>> blockrandom.choice(["a", "b", "c"])
"b"

>>> my_list = [1, 2, 3]
>>> blockrandom.shuffle(my_list)  # mutating, in place shuffle
>>> my_list
[2, 1, 3]

>>> blockrandom.shuffled([1, 2, 3])  # non-mutating shuffle
[3, 1, 2]
````

If you want a non-blocking random generator that will recycle previously collected entropy, similar to `/dev/urandom`, you can use the following:

```python
>>> blockrandom.u_randbytes(5)
bytearray(b'\xe5\x84Y\x87a')

>>> blockrandom.u_randbool()
False

>>> blockrandom.u_randint(0, 100)
22

>>> blockrandom.u_random()
0.24625146389007568

>>> blockrandom.u_uniform(1.5, 2.5)
1.380821943283081

>>> blockrandom.u_choice(["a", "b", "c"])
"c"

>>> my_list = [1, 2, 3]
>>> blockrandom.u_shuffle(my_list)  # mutating, in place shuffle
>>> my_list
[3, 2, 1]

>>> blockrandom.u_shuffled([1, 2, 3])  # non-mutating shuffle
[2, 3, 1]
```

More features and better concurrency support are coming soon!

### How it works ###

Unconfirmed transaction hashes are queried from [blockchain.info](https://blockchain.info). If we need to recycle previous entropy, we use the 32-bit MurmurHash3 function.

How well does this work? Well, here's 1MB of truly random bytes from unconfirmed transactions:

![Random bytes from blockchain](../master/experiments/transaction_bytes.png?raw=true)

Here is the same 1MB, after it was run through MurmurHash3 1000 times:

![Random bytes from blockchain](../master/experiments/murmured1000_transaction_bytes.png?raw=true)

And is 1MB generated with MurmurHash3, using only a few KB of entropy from transactions used as a seed:

![Pseudorandom bytes from MurmurHash3, seeded from blockchain](../master/experiments/u_randbytes.png?raw=true)

As you can see, both the MurmurHash3'd entropy and the psuedorandomly generated byes are still well-distributed, which suggests that we can indeed turn MurmurHash3 into a dicent PRNG.

For more details, see the `randomness_testing.py` file and the `experiments` directory, which shows the results of the experiments enumerated in `randomness_testing.py`.
