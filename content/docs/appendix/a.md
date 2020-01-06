---
title: "Appendix A: Character Sets & Collations"
date: 2019-12-16T10:56:00
weight: 10
---

# Character Sets & Collations

## Character Sets

A character set is a mapping of characters to numbers (also called code points).  For example, the letter 'A' may be assigned the code point 41, and the character '爱' may be assigned the code point 29233.

**[ASCII](https://en.wikipedia.org/wiki/ASCII)** is a character set of 128 characters that includes the letters of the English alphabet, some punctuation marks, the numbers 0 to 9, and some mathematics symbols.

**[Latin1](https://en.wikipedia.org/wiki/ISO/IEC_8859-1)** is the default character set in MySQL and includes characters used in many Western European languages.  There are 256 characters in the Latin1 character set.

**[Unicode](https://home.unicode.org/basic-info/overview/)** is a character set that aims to represent characters from all the world's languages plus other commonly used symbols, including emojis.  (Eg. The Unicode code point for the 😂 emoji is 128514.)  There are 137,929 code points defined in Unicode as of 2019, but new additions are made to Unicode frequently, and there is enough room in the Unicode specification for over 1 million code points.

There are many more character sets than the ones mentioned above, but these are the ones you are likely to encounter most often when dealing with English language applications.

{{< hint info >}}
The first 128 code points of Latin1 and Unicode are the same as ASCII
{{< /hint >}}

### Encodings

An encoding is a method of representing in binary the code points of a character set.  For example, both ASCII and Latin1 have very simple encodings in which each code point is represented by its binary value.  (For example, 'A' becomes `00101001`, the binary representation of the number 41.) Since both of ASCII and Latin1 have no more than 256 code points, each character can be represented by a single byte.

However, some encodings are more complicated, and one character set may have many encodings.  Unicode, for example, has several different encodings.  UTF-32 is a Unicode endocding that is similar to the encodings for ASCII and Latin1 in that it simply represent each code point using its binary value.  It uses 4 bytes for each character, though, so that all the code points in Unicode can be represented.

The most commonly used Unicode encoding is UTF-8.  The details of the encoding scheme are beyond the scope of this book, but UTF-8 has the feature that it tends to use less bytes per character than an encoding like UTF-32, while still being able to encode all Unicode code points.

{{< hint warning >}}
**NOTE**: Because of character sets like ASCII and Latin1 where the encodings have the same name as their character sets, the terms 'character set' and 'encoding' are often conflated even though they are technically different concepts.  For example, in MySQL, when you set the `CHARACTER SET` for a database, table, or column, you are really specifying an encoding.
{{< /hint >}}

| Character | ASCII Encoding | Latin1 Encoding | UTF-32 Encoding                       | UTF-8 Encoding |
|-----------|----------------|-----------------|---------------------------------------|----------------|
| A         | `00101001`     | `00101001`      | `00000000`  `00000000`  `00000000`  `00101001` | `00101001`     |
| ©         | N/A            | `10101001`      | `00000000`  `00000000`  `00000000`  `10101001` | `11000010`  `10101001`     |
| 爱        | N/A     | N/A      | `00000000`  `00000000`  `01110010`  `00110001` | `11100111`  `10001000`  `10110001`     |
| 😂        | N/A     | N/A      | `00000000`  `00000001`  `11110110`  `00000010` | `11110000`  `10011111`  `10011000`  `10000010`     |

The table above shows how several different characters are represented in different encodings.  

{{< hint info >}}
**NOTE**
 - Some code points are not representable in some encodings
 - ASCII characters have the same essential encoding in all four example encodings above, with the caveat that UTF-32 pads the representation with 3 bytes of 0s.
 - The UTF-32 and UTF-8 encodings are **both** Unicode encodings, but they represent the same code points in different ways
{{< /hint >}}

{{< hint warning >}}
Be aware that there are other encodings for which it is **not** the case that the encoding is the same as ASCII.
{{< /hint >}}

#### Encoding Problems

Because different encodings represent the same character with different sequences of bits, if an application reads a document (or a database table) assuming a different encoding than the document (or table) was saved in it can result in unexpected characters appearing in the document.

For example, suppose a document is saved in UTF-8 but read as though it were Latin1, and suppose the document contains the 爱 character.

In UTF-8, the 爱 character is encoded using the following sequence of 3 bytes: 

`11100111 10001000 10110001`

However, using Latin1, each byte is interpreted as a separate character: 

- `11100111` is 'ç', 
- `10001000` is a 'control' character that has no visual representation, and
- `10110001` is '±'

Thus, instead of seeing the '爱' character, you would see the pair of characters 'ç±'.

As you can see, it is important to ensure that documents and tables are read using the same encodings in which they were stored.


### Collations