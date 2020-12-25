## This chapter is about the RPC protocol. 
Remote Procedure Call is a classical server-client model.
Produce:
1.The client calls the client stub. The call is a local procedure call, with parameters pushed on to the stack in the normal way.\
2.The client stub packs the parameters into a message and makes a system call to send the message. **Packing the parameters is called marshalling.**\
3.The client's local operating system sends the message from the client machine to the server machine.\
4.The local operating system on the server machine passes the incoming packets to the server stub.\
5.The server stub unpacks the parameters from the message. **Unpacking the parameters is called unmarshalling.**\
6.Finally, the server stub calls the server procedure. The reply traces the same steps in the reverse direction.\

Following is an example about how to use Protobuf binary format.
