# General Notes

## Commits reviewed
### (1) 6b18269ca7d5f38d7bffb267e359e982606a9bf4

There's too many `+ CMESSAGE::HEADERSIZE` type things going on.
 (this is addressed in next commit :) )

 Review Club question: why unique\_ptr? RAII. We want the netwok buffers and
 vRecv data from a node to be deleted immediately when pointer is out of use. no
 ref-count overhead from using shared\_ptr with just one ref used.
 Only one thread should be handling a peer at once anyway.
 

Check later:
  in the p2p test change, why does the "ProcessMessage" bit get removed?

### (2) b830a602b85edb414c5a6add4481b56af19fb628

removes the thing I complained about 

uses "raw" size and "wire" size terminology together to mean the same thing. It
should be simple enough that if the reader doesn't already know they are the
same, this makes it clear. Maybe better if consistent? meh idk. 

### (3) 7370bed970e9455dc686c8088dd3e3afb219bbee

Adds V1TransportDeserializer

hmm last night I was confused at how `m_deserializer->Read` was getting
everything. its in a while loop: while there are bytes remaining

## Review Club homework

1: I will ACK

2. this PR is an essential step to getting p2p message v2. adding v2 will now mean just
   adding a V2TransportDeserializer

3. the first commit splits up deserialization of messages from CNode's message
   container

4. answered above

5. The Adapter pattern AKA a Wrapper acts as a translation layer between 2
   incompatible interfaces. In this case V2 transport will not be directly
   deserializable into CNetMessage.

## October 27 re-review after rebase + added refactoring
Ran all tests and functional tests for the following commmits and did a code
review.

### (1) 6294ecdb8bb4eb7049a18c721ee8cb4a53d80a06
Refactor: split network transport deserializing from message container

pretty much the same as 6b18269ca7d5f38d7bffb267e359e982606a9bf4 with minor cleanup in def of CNetMessage

### (2) 1a5c656c3169ba525f84145d19ce8c64f2cf1efb
Remove transport protocol knowhow from CNetMessage / net processing

Same as b830a602b85edb414c5a6add4481b56af19fb628; looks good again

### (3) efecb74677222f6c70adf7f860c315f430d39ec4
Same as 7370bed970e9455dc686c8088dd3e3afb219bbee; looks good again

### (4) b0e10ff4df3d4c70fb172ea8c3128c82e6e368bb
`Force CNetMessage::m_recv to use std::move`
ryanofsky: "std::move here is misleading, and will result in a copy because
`recv_in` is const."

looks good. makes sense after searching stuff about std::move and const

https://stackoverflow.com/questions/28595117/why-can-we-use-stdmove-on-a-const-object
https://www.artima.com/cppsource/rvalue.html

### (5) 6a91499496d76c2b3e84489e9723b60514fb08db
(sipa)     Remove oversized message detection from log and interface

simple enough change. `m_deser->Read` will return -1 when reading the header to
cause a disconnect

### (6) f342a5e61a73e1edf389b662d265d20cf26a1d51
(sipa)     Make resetting implicit in TransportDeserializer::Read()
quite nice change! simple as.

### (7) ed2dc5e48abed1cde6ab98025dc8212917d47d21
(sipa) add override/final

simple as. Adds clarity to the v1 transport deser definition.
