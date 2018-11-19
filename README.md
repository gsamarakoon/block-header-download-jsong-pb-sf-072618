
# Block Header Download

## Try it


```python
# Block Header Download Example

from network import GetHeadersMessage, HeadersMessage, SimpleNode

node = SimpleNode('btc.programmingblockchain.com', testnet=False)
node.handshake()
last_block_hash = GENESIS_BLOCK_HASH
count = 1
for _ in range(20):
    getheaders = GetHeadersMessage(start_block=last_block_hash)
    node.send(getheaders.command, getheaders.serialize())
    headers_envelope = node.wait_for_commands([b'headers'])
    headers_message = HeadersMessage.parse(headers_envelope.stream())
    for b in headers_message.blocks:
        if not b.check_pow():
            raise RuntimeError('bad proof of work at block {}'.format(count))
        if last_block_hash != GENESIS_BLOCK_HASH and b.prev_block != last_block_hash:
            raise RuntimeError('discontinuous block at {}'.format(count))
        count += 1
        last_block_hash = b.hash()
        if count % 2016 == 0:
            print(b.id())
```
