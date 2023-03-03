

3.1.  SCHC Compound ACK Message Format

   Figure 1 shows the regular SCHC ACK format when all fragments have
   been correctly received (C=1), as defined in [RFC8724].

```text
                  |- SCHC ACK Header --|
                  + -------+---+------ + ------------ +
                  | RuleID | W | C=b'1 | b'0-pad(opt) |
                  + ------ + - + ----- + ------------ +

      Figure 1: SCHC Success ACK message format, as defined in RFC8724
```

```text
                  |- SCHC ACK Header --|
                  + -------+---+------ + ~~~~~~~~~~~~~~~~~
                  | RuleID | W | C=b'1 | padding as needed  
                  + ------ + - + ----- + ~~~~~~~~~~~~~~~~~

      Figure 1a: SCHC Success ACK message format, as defined in RFC8724
```

   In case SCHC Fragment losses are found in any of the windows of the
   SCHC Packet, the SCHC Compound ACK MAY be used. The SCHC Compound
   ACK message format is shown in Figure 2.
```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi      |
    +-------------------+ ------ +...+ ------- + ------ +------------+
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi | Bitmap |b'0-pad(opt)|
    +------+------+-----+ ------ +...+ ------- + ------ +------------+

       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure 2: SCHC Compound ACK message format
```
```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi      |
    +-------------------+ ------ +...+ ------- + ------ +------------+
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi | Bitmap |b'0-pad(opt)|
    +------+------+-----+ ------ +...+ ------- + ------ +------------+
                                             next L2 Word boundary ->|
       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure 2a: SCHC Compound ACK message format
```
```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi      |
    +-------------------+ ------ +...+ ------- + ------ + ----- +
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi | Bitmap |  pad. |
    +------+------+-----+ ------ +...+ ------- + ------ + ----- +
                                        next L2 Word boundary ->|
       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure 2b: SCHC Compound ACK message format
```
M 000   100 001 010 110 
```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi      |
    +-------------------+ ------ +...+ ------- + ------ + ~~~~~~~~~~~~~~~~~
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi | Bitmap | padding as needed 
    +------+------+-----+ ------ +...+ ------- + ------ + ~~~~~~~~~~~~~~~~~
                                next L2 Word boundary ->|
       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure 2c: SCHC Compound ACK message format
```
   Padding is optional, and the recommended value is 0.
 ADD-->Note: because it has a C bit reset to 0, the SCHC Compound ACK is
   distinguishable from the Receiver-Abort message [RFC8724], which has a C bit set to 1.<--
 
REMOVE--> Each different SCHC LPWAN technology profile MUST specify how the
   SCHC Compound ACK is different from the Receiver-Abort message as per
   [RFC8724], e.g., the Receiver-Abort message is padded with 1s with an
   extra byte appended at the end, while the SCHC Compound ACK is
   0-padded.<---

```text
           Sender                Receiver
             |-----W=0, FCN=6 ----->|
             |-----W=0, FCN=5 ----->|
             |-----W=0, FCN=4 ----->|
             |-----W=0, FCN=3 ----->|
             |-----W=0, FCN=2 --X-->|
             |-----W=0, FCN=1 ----->|
             |-----W=0, FCN=0 ----->| Bitmap: 1111011
         (no ACK - no DL enable)
             |-----W=1, FCN=6 ----->|
             |-----W=1, FCN=5 ----->|
             |-----W=1, FCN=4 ----->|
             |-----W=1, FCN=3 ----->|
             |-----W=1, FCN=2 ----->|
             |-----W=1, FCN=1 --X-->|
             |-- W=1, FCN=7 + RCS ->| Integrity check: failure
             |<--- Compound ACK ----| [C=0, W=0 - Bitmap:1111011,
             |-----W=0, FCN=2 ----->|        W=1 - Bitmap:1111101]
             |-----W=1, FCN=1 ----->| Integrity check: success
             |<--- ACK, W=1, C=1 ---| C=1
           (End)

            Figure 3: SCHC Compound ACK message sequence example
```


```text
    |-- SCHC ACK Header ---|- W=00 --|----- W=01 -----|
    + -------------------- + ------- + ---- + ------- + ------------- +
    | RuleID | W=00  | C=0 | 1111011 | W=01 | 1111101 | b'0-pad (opt) |
    + ------ + ------ + -- + ------- + ---- + ------- + ------------- +
                                              next L2 Word boundary ->|
       Figure 4a: SCHC Compound ACK message format example: Losses are
                         found in windows 00 and 01

```


REMOVE-->Differentiation between SCHC Receiver-Abort and SCHC Compound ACK
      message, e.g., Receiver-Abort message padded with 1s with an extra
      byte appended at the end, while the SCHC Compound ACK is 0-padded.<---