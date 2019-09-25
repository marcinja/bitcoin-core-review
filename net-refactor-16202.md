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
