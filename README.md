Evrything about the all the types of encoding such as Base64 encoding, UTF-8 encoding, UTF-16 encoding, Uncode etc

Topic 1) Base64 encoding:

"Basically Base64 converts a string of bytes (e.g. 01111011 01011000 10001111) into a string of ASCII characters (e.g. "e1iP") so that they can be safely transmitted within HTTP headers. "

A byte consist of 8 bits. When encoding, Base64 will divide the string of bytes into groups of 6 bits and each group will map to one of 64 characters. These 64 characters are the in the Base64 character table. 

Base64 is a data encoding scheme used in safe data transfer such as HTTP and its extensions. Base64 encoding can conver arbitrary group of bytes into a sequence of readable ASCII characters. These converted characters can safely put in a HTTP header without causing any problem while the peers process the HTTP header.

Base64 will divide the bytes into groups of 6 bits. Every 6 bits will map to a character. This character will be one of the 64 characters in the Base64 character table. These 64 characters are common and can be safely put in a HTTP header. These characters include a-z, A-Z, 0-9, +, / and a special purpose character =(The 65th character).

Base64 will divide the bytes into groups of 6 bits. Every 6 bits will map to a character. This character will be one of the 64 characters in the Base64 character table. These 64 characters are common and can be safely put in a HTTP header. These characters include a-z, A-Z, 0-9, +, / and a special purpose character =(The 65th character).

So assume you've this 3 bytes 01111011 01011000 10001111  (bytes may contain any special char, null char, new line char). If we transmit this data as it is over network, there is chance that interpreter at other end might interpret it wrongly. For example, if sequence of bytes contain 00000000, some iterpreter treat it has NULL char i.e. end of string & may stop reading subsequent bytes. Or some interpreter treat 00001010 as EOF. 

To avoid this what we can do is make use of Base64 encoding. How it helps, we'll see.
1) Divide given bytes string into blocks of 6 bits each i.e.  01111011 01011000 10001111 ==> 011110 110101 100010 001111
2) 011110 110101 100010 001111 convert to decimal ==> 30 53 34 15
3) Use Base64 table (i.e 0 to 25 for A to Z .. 26 to 51 for a to z ..52 to 61 for 0 to 9 .. 62 for + .. 63 for /) .. so  given 30 53 34 15 gets converted to   e 1 i P   
4) "e1iP" is Base64 representation for  binary 01111011 01011000 10001111  ({X� is original ASCII representation for the given binary)
5) Convert e1iP to binary before transferring data over network ==> 01100101 00110001 01101001 01010000 ..
6) Now send 01100101 00110001 01101001 01010000 over network.
7) Since binary to ASCII conversion of given binary 01100101 00110001 01101001 01010000 does not contain any special chars (it contains only chars from A to Z or a to z or 0 to 9 or + or / chars), this binary is interpreted correctly as e1iP at receiver.
8) Receiver Uses Base64 table to convert Base64 encoded string i.e. e1iP in this case to get decimals i.e. to get 30 53 34 15
9) Convert 30 53 34 15 to binary representation .. we'll get 00011110 00110101 00100010 00001111 .. ingore first 00 of every byte .. we'll get binary 011110 110101 100010 001111
10) 011110 110101 100010 001111 convert it into blocks of 8 bits we'll get original binary string i.e. 01111011 01011000 10001111.
11) This way we avoid binary data getting mis-interpreted at reciever over network.

Of course with this way for every 3 bytes of original data when we do Base64 encoding we end up transmitting 4 bytes of Base64 encoded data.

Base64 encoding is specifically useful when adding data to HTTP headers (specially if data contain lot of special chars such as NULL char or EOF char) or attaching image to email (image's binary may contain lot of 00000000 bytes thereby getting wrongly mis-interpreted as NULL char if we do not use Base64 encoding).

Additional info:
First we’ll explain why it’s needed. Rather than HTTP, Base64 was first suggested as an encoding stream for MIME data in an email.
The authors of the MIME RFC Part 1 Wanted to be able to send pictures and other files via E-Mail. However the SMTP standard only allows transmission of ASCII characters using 7 bit values 0–127.
A binary file of course consists of bytes containing values 0–255, so we first need to convert this so it can be transmitted using SMTP.
Why base64 instead of base128 then?
For a start not all of the 128 ASCII codes can be used. CR/LF (ASCII 13 and 10 respectively), are used to denote the end of a line in SMTP, so if this pairing shows up in the converted text, the data won’t be encoded properly. So rather than using base128 (7bits), We use base64 (6bits) to represent our data.

Base64 is used to transmit data over TEXT based mediums where the original byte values of the data are important. Some systems may alter the original bytes of a string due to differing text encodings. Additionally, text based systems typically can't handle full binary data. To be able to transmit a hash fingerprint for example, a receiver needs to be able to receive the exact same byte values sent by the originator. If the byte values were to change, verification of the digital signature would fail. You'll find Base64 used in SSL certificates, SAML assertions for single sign on systems, embedding data in XML and HTML, as well as many other use cases. 

Base64 vs UTF-8 encoding:
Base64 encoding: arbitrary sequence of bytes to ASCII text (ASCII text to be transimitted over text based medium such as HTTP. Arbitratry sequence of bytes could be a 256 bit Hash as well which is very sensitive, a slight mis-interepretation at receiver would fail the HASH signature validation. hence generally HASH sent in HTTP request header is Base64 encoded.)

UTF-8 encoding: Text to sequence of bytes. (e.g. अ if this char is UTF-8 encoded then it's binary representation is "111 00000"           "10 100100"       "10 000101")
UTF-8: 
Unicode: Unicode is a standard which defines the code rank for all the universal characters used across all the languagues of the world, emojis etc. Unicode specifies code rank for character/symbol.How the character or symbol is stored in memory or represent it in an email message is defined by encoding type used for Unicode .Unicode supports different character encoding such as UTF-8, UTF-16, ASCII. 
UTF-8 encoding can be achieved by using 1 to 4 bytes. 1st byte is reserved for 128 ASCII chars. So ASCII char has same binary representation in UTF-8 and ASCII encoding.
i.e. for code rank 0 to 127 ASCII encoding === UTF-8 encoding

Code rank                         No of Bytes   Most significant bit            Byte1       Byte2       Byte3      Byte4
0 to 127  (U+0000 to U+007F)            1              7                       0xxxxxxx
128 to 2047 (U+0080 to U+07FF)          2              11                      110xxxxx    10xxxxxx
2048 to  65535 (U+0800 to U+FFFF)       3              16                      1110xxxx    10xxxxxx   10xxxxxx                 
65536 to 2097152 (U+10000 to U+10FFFF)  4              21                      11110xxx    10xxxxxx   10xxxxxx    10xxxxxx

e.g. Unicode rank for अ is 2309 (1001 0000 0101‬ is binary representation of 2309 i.e. most significant bit is 12). 
From above table 2309 code rank requires 3 bytes for UTF-8 encoding. (since most significant bit is 12th one) 
As per above table UTF-8 representation of code rank 2309 should fit in 1110xxxx    10xxxxxx   10xxxxxx.
We've total 12 bits in bit representation of 2309 (1001   00 00    0101‬) .. hence "111 00000"           "10 100100"       "10 000101" i.e. 11100000 10100100 10000101 is binary represenation of UTF-8 encoded text अ with code rank as 2309.


UTF-8 is a text encoding - a way of encoding text as binary data.
Base64 is in some ways the opposite - it's a way of encoding arbitrary binary data as ASCII text.
If you need to encode arbitrary binary data as text, Base64 is the way to go - you mustn't try to treat arbitrary binary data as if it's UTF-8 encoded text data.
However, you may well be able to transfer the file to the server just as binary data in the first place - it depends on what transport you're using.
