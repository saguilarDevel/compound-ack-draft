
# Compound ACK

## Overview

This specification includes the SCHC Compound ACK description.
The SCHC Compound ACK is intended to reduce the number of downlink transmissions (i.e., SCHC ACKs) by accumulating bitmaps of several windows in a single SCHC message (i.e., the SCHC Compound ACK). 
The SCHC Compound ACK extends the SCHC ACK message format so that it can contain several bitmaps, each bitmap being identified by its corresponding window number.

The SCHC Compound ACK:
   * provides feedback only for windows with fragment losses,
   * has a variable size that depends on the number of windows with fragment losses being reported in the single Compound SCHC ACK,
   * includes the window number (i.e., W) of each bitmap,
   * has a format coincident with that of a SCHC ACK (RFC 8724) when only one window with losses is reported,
   * might not cover all windows with fragment losses of a SCHC Packet,
   * is distinguishable from the SCHC Receiver-Abort.

The SCHC Compound ACK groups the window number (W) with its corresponding bitmap.
 The window number and its bitmap MUST be ordered from the lowest-numbered to the highest-numbered window.
 
 ## Compound ACK message format
 
 The SCHC Compound ACK message format is shown in Figure 4.
  The window numbered 00, if present in the SCHC Compound ACK, MUST be placed between the Rule ID and the C bit to avoid confusion with padding bits.  
  If padding is needed for the SCHC Compound ACK, padding bits MUST be 0 to make subsequent window numbers and bitmaps distinguishable.

 ```text
    
   |---- SCHC ACK Header ----|-W = x -| ... | --- W = x + i ---|
   + ----------------------- + ------ + ... + ------- + ------ + ------- +
   | RuleID | W=b'x  | C=b'0 | Bitmap | ... | W=b'x+i | Bitmap | b'0-pad |
   + ------ + ------ + ----- + ------ + ... + ------- + ------ + ------- +
   

        Figure : SCHC Compound ACK message format. 
        On top are noted the window number of the corresponding bitmap. 
        Losses are found in windows x,...,x+i.
```
 
 The profiles MUST specify how the Compound ACK is different from the Receiver-Abort message.
 
 The Compound ACK MAY use a Compressed Bitmap, and bitmap fields MAY be of variable size.
 
 
 ## Examples
 Figure shows an example transmission of a SCHC Packet in ACK-on-Error mode with Compound ACK.
 The SCHC Packet is fragmented in 14 tiles, with N=3, WINDOW_SIZE=7, M=2 and two lost SCHC fragments.
Only 1 compound SCHC ACK is generated.

```text
        Sender                      Receiver
          |-----W=0, FCN=6 ----->|
          |-----W=0, FCN=5 ----->|
          |-----W=0, FCN=4 ----->|
          |-----W=0, FCN=3 ----->|
          |-----W=0, FCN=2 --X-->|
          |-----W=0, FCN=1 ----->|
          |-----W=0, FCN=0 ----->| Bitmap: 1111011
      (no ACK)
          |-----W=1, FCN=6 ----->|
          |-----W=1, FCN=5 ----->|
          |-----W=1, FCN=4 ----->|
          |-----W=1, FCN=3 ----->|
          |-----W=1, FCN=2 ----->|
          |-----W=1, FCN=1 --X-->|
          |-- W=1, FCN=7 + RSC ->| Integrity check: failure
          |<---- Compund ACK ----| C=0, W=0 - Bitmap:1111011, W=1 - Bitmap:1111101
          |-----W=0, FCN=2 ----->| 
          |-----W=1, FCN=1 ----->| Integrity check: success
          |<--- ACK, W=1, C=1 ---| C=0
        (End)
```

 
 ```text
    
   |-- SCHC ACK Header ---|- W=00 --|----- W=01 -----|
   + -------------------- + ------- + ---- + ------- + ------- +
   | RuleID | W=00  | C=0 | 1111011 | W=01 | 1111011 | b'0-pad |
   + ------ + ------ + -- + ------- + ---- + ------- + ------- +
   

        Figure : SCHC Compound ACK message format example. 
        On top are noted the window number of the corresponding bitmap. 
        Losses are found in windows 00 and 01.
```



