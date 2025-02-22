# infinispan.playground.encoding
Infinispan Marshalling and Encoding examples
===============================

Author: Wolf-Dieter Fink
Technologies: Infinispan, Hot Rod, Protobuf


What is it?
-----------

This example will show how the HotRod client and REST can access Protobuf objects from an encoded cache.
It uses a UUID class which is not supported out of the box from protostream.

It has been introduced with Infinispan 12 and will replace the MessageMarshaller interface and API because of the performance improvements.

This approach is recommended as it will have probably the best performance as the ProtoStream marshalling is highly optimized.
Also it allows different type of clients to access the remote cache, like HotRod (Java C++ C#) node.js and REST.
The content can be converted for each client, the server will handle this.


Preparation
-------------
Follow the README from teh root project folder to prepare a server instance.
Add the necessary cache by using the template and CLI

        create cache --file=../template/ProtobufLibraryCache.xml LibraryCache


Run the example
-------------------------

   Type this command or an IDE to start the simple example class LibraryClient

        mvn exec:java

     The client will use the generated classes to marshall the UUID class and the @AutoProtoSchemaBuilder annotated LibraryInitializer to have the information and to register the 
     message objects.



   access Data with REST client

      curl [-X GET] http://127.0.0.1:11222/rest/v2/caches/LibraryCache/4

        should return the protobuf Message as json object
        As there is no registered proto schema for the server this will fail
        Until:
         - The domain/target/Encoding-ProtoAdaptorDomain.jar is dropped to the ISPN_HOME/server/lib directory
           This will register automatically the schema, without using the proto-schema-cache so all server instances needs the classes
         - The client register the schema when starting (first), uncomment the registerSchema(...) invocation
           It will add the proto schema to the (persisted) proto-schema-cache clusterwide

      curl -H "Accept: application/x-protostream" --output -  http://127.0.0.1:11222/rest/v2/caches/LibraryCache/1

        returns the protobuf Message as binary protobuf, this could mess up the terminal!

      curl -H "Accept: text/plain" http://127.0.0.1:11222/rest/v2/caches/LibraryCache/1

        returns a failure "No marshaller registered..." as this convertion is not supported
