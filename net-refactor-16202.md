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
