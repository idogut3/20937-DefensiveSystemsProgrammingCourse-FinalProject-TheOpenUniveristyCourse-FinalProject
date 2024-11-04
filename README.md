﻿# A Little about the project
  This is a project I have done as part of my university course.
  It is a client-server application that communicates with each other. The client-side is written in Cpp and the server-side is in Python.
  
Essentially there are 3 major "protocols" that operate with each other in the project, a Registration protocol, a Reconnection protocol and a SendFile protocol.

[Video for showcasing the project](https://drive.google.com/file/d/1rvdHS-tQpMWXrNozavQi8TW145cvzeQ8/view?usp=sharing)

# Overview of Server's Functionality
The server reads his port from the file info.port  (If the file does not exist, it issues a warning and work on the port default: 1256.
It waits for requests from clients in an endless loop, when it receives a request, it deciphers the request and operates based on the request code it extracts from the request's header.

# Overview of the Client's operations:

![client-side-actions](https://github.com/idogut3/20937-DefensiveSystemsProgrammingCourse-FinalProject-TheOpenUniveristyCourse/blob/main/images/client-side-actions.png)

# Registration protocol

If the requested username already exists, the server will return an error, send a general error response to the client. Otherwise, the server will generate a new UUID for the user, save the data in memory (in the database) and return a registration success response with the new uuid of the client's.
After that, the server waits for the client's public key request-message and when it gets it, the server updates it in it's database. In response, the server will generate an AES key, which will be encrypted with the client's public key and sent back to the client.
The client who receives it decrypts the encrypted aes key, and now will use the aes key to encrypt new messages (and files) it will send to the server.

![Registration protocol diagram](https://github.com/idogut3/20937-DefensiveSystemsProgrammingCourse-FinalProject-TheOpenUniveristyCourse/blob/main/images/Reconnection.png)

# Reconnection protocol
If the requested username doesn't exists, the server will try and register the user with the Registration protocol. Otherwise, the server will send the client a reconnection success response with the username of the client. 
Then it will generate an AES key which will be encrypted with the client's public key and sent back to the client (If for some reason the client doesn't have a public key - the server will responed with a general error message to the client).
The client who receives the encrypted aes key decrypts the it, and now will use the aes key to encrypt new messages (and files) it will send to the server.

![Reconnection protocol diagram](https://github.com/idogut3/20937-DefensiveSystemsProgrammingCourse-FinalProject-TheOpenUniveristyCourse/blob/main/images/Registration.png)

# SendFile protocol

When the user wants to send the server a file, it first has to register to the server or reconnect to it (with the correct uuid that exists in the server's database).
After being signed in the user can try to send the file in packets (in a loop until we sent all the packets) (which will be encrypted with the aes given by the server in the Registration protocol or the Reconnection protocol.
Each packet will have a header and a payload, a sendFile packet will look like this - where the request code will be 828 (SendFile_Request_Code):

![RequestHeader](https://github.com/idogut3/20937-DefensiveSystemsProgrammingCourse-FinalProject-TheOpenUniveristyCourse/blob/main/images/RequestHeader.png)
![SendFileRequestPayload](https://github.com/idogut3/20937-DefensiveSystemsProgrammingCourse-FinalProject-TheOpenUniveristyCourse/blob/main/images/SendFilePayload.png)

The server will decrypt the encrypted file using the original AES key it sent to that client, and will calculate the CRC (which is the value obtained from the checksum operation).
It will send a response with the CRC it got and the client will either confirm it is the right checksum value (and send a "send file success" message), Or deny it and will send a request-message saying the CRC is wrong and will try to send the file again 3 more times until it has succeeded or failed for the fourth time.

The checkusm calculation, on the server And in the client, will be executed in the same way as the cksum command in Linux: /https://www.howtoforge.com/linux-cksum-command




