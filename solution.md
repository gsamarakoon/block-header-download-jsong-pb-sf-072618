
# Block Headers

## Test Driven Examples


```python
# Exercise 3.1

from io import BytesIO
from network import GetHeadersMessage
from helper import (
    encode_varint,
    int_to_little_endian,
    little_endian_to_int
)

class GetHeadersMessage(GetHeadersMessage):

    def serialize(self):
        '''Serialize this message to send over the network'''
        # protocol version is 4 bytes little-endian
        result = int_to_little_endian(self.version, 4)
        # number of hashes is a varint
        result += encode_varint(self.num_hashes)
        # start block is in little-endian
        result += self.start_block[::-1]
        # end block is also in little-endian
        result += self.end_block[::-1]
        return result
```


```python
# Exercise 3.2

from io import BytesIO
from network import HeadersMessage
from helper import read_varint
from block import Block

class HeadersMessage(HeadersMessage):

    @classmethod
    def parse(cls, stream):
        # number of headers is in a varint
        num_headers = read_varint(stream)
        # initialize the blocks array
        blocks = []
        # loop through number of headers times
        for _ in range(num_headers):
            # add a block to the blocks array by parsing the stream
            blocks.append(Block.parse(stream))
            # read the next varint (num_txs)
            num_txs = read_varint(stream)
            # num_txs should be 0 or raise a RuntimeError
            if num_txs != 0:
                raise RuntimeError('number of txs not 0')
        # return a class instance
        return cls(blocks)
```
