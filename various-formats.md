
# Various Compound ACK formats


If more than M padding bits are needed, the first M bits MUST be 0-padded.
The M bits with 0 value, signal the end of the SCHC Compound ACK. 


```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi      | 
           |M bits|1 bit| N bits |   | M bits  | N bits | M bits |    
    + ----------------- + ------ +...+ ------- + ------ + ------ + --- +
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi | Bitmap | 00..00 | pad |
    + -----+------+---- + ------ +...+ ------- + ------ + ------ + --- +
                              next L2 Word boundary ->|<-- L2 Word --->|
       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure A: SCHC Compound ACK message format
```

```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi      | 
           |M bits|1 bit| N bits |   | M bits  | N bits |      
    + ----------------- + ------ +...+ ------- + ------ + ---- +
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi | Bitmap | 0..0 |
    + -----+------+---- + ------ +...+ ------- + ------ + ---- +
                                       next L2 Word boundary ->|
       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure A.1: SCHC Compound ACK message format
```
If the required padding bits are less than M bits, then any padding value MAY be added, as it is clear that there is no other group of window and bitmap, as there are no M bits to indicate any window value.

```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi      | 
    + ----------------- + ------ +...+ ------- + ------ + --- +
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi | Bitmap | pad |
    + -----+------+---- + ------ +...+ ------- + ------ + --- +
           |M bits|1 bit| N bits |    | M bits | N bits | M bits |     
                                                              | <- next L2 Word boundary
       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure B: SCHC Compound ACK message format
```


## Compressed Bitmap

If bitmap compression is supported in the last bitmap of the SCHC Compound ACK.

Example of bitmap compression in the last bitmap with N = 7.
```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi            | 
    + ----------------- + ------ +...+ ------- + ------------ + 
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi |1 1 1 1 1 1 1 | with uncompressed Bitmap
    + -----+------+---- + ------ +...+ ------- + ------------ + 
           |M bits|1 bit| N bits |    | M bits |    N bits    |
                         next L2 Word boundary ->|
                         
                        | W = w1 |...|  W = wi   |
    + ----------------- + ------ +...+ ------- +-+ 
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi |1|              transmitted SCHC Compound ACK
    + -----+------+---- + ------ +...+ ------- +-+ 
                         next L2 Word boundary ->|



       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure C: SCHC Compound ACK message format
```

Example of bitmap compression in the last bitmap with N = 7.
```text

    |--SCHC ACK Header--| W = w1 |...|      W = wi            | 
    + ----------------- + ------ +...+ ------- + ------------ + 
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi |1 1 0 1 1 1 1 | with uncompressed Bitmap
    + -----+------+---- + ------ +...+ ------- + ------------ + 
           |M bits|1 bit| N bits |    | M bits |    N bits    |
                         next L2 Word boundary ->|
                         
                        | W = w1 |...|      W = wi            | M bits |
    + ----------------- + ------ +...+ ------- + ------------ + ------ + --- +
    |RuleID|W=b'w1|C=b'0| Bitmap |...|  W=b'wi |1 1 0 1 1 1 1 | 00..00 | pad |     transmitted SCHC Compound ACK
    + -----+------+---- + ------ +...+ ------- + ------------ + ------ + --- +
                         next L2 Word boundary ->|<-------- L2 Word -------->|



       Losses are found in windows W = w1,...,wi; where w1<w2<...<wi

                 Figure D: SCHC Compound ACK message format
```

