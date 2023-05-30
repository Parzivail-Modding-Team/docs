# Blaster attachment bitmaps

Internally, the blaster attachments are represented as a 32-bit-wide bitfield, where each attachment is represented by a single bit in the bitmap. Attachment layers are a span of bits which, with the possible attachments on that layer, form a mutex. Each layer, or mutex, has a set of bits which define the _minimum attachment_ for each layer, expressed through the API as an optional "required attachment" on a layer.

## Fundamentals

The attachment system has four fundamental components:

* The blaster stack's attachment bitmap
* The attachment's unique bitfield
* The attachment layer's mutex bitmap
* The blaster's _minimum attachment_

For the following examples, three hypothetical attachments are defined:

```
              Bitfield  Layer Mutex
Attachment A  00000001  00000001
Attachment B  00000010  00000110
Attachment C  00000100  00000110
```

Since both Attachments B and C share the same mutex, and the mutex is comprised of the bitfields for both Attachment B and C, the blaster can have only one of B or C attached at any time. Hence, they are **mut**ually **ex**clusive. *(Note: this example blaster must have exactly one of B and C attached at all times due to the later described **attachment minimum**, see [Attachment Removal](#attachment-removal))*

The examples also consider the hypothetical blaster to have these initial properties:

```
Attachment bitmap    : 00000101
Attachment minumum   : 00000010
```

## Rendering

When a blaster is rendered, only the item stack's attachment bitmap and each attachments' bitfield are considered. The attachments of the example blaster are rendered as follows:

```
Attachment bitmap    : 00000101
                       ||||||||
Attachment A bitfield: 00000001
Attachment B bitfield: 00000010
Attachment C bitfield: 00000100
```

Because the first and third bits of the attachment bitmap are set, the attachments with bitfield `1` (Attachment A) and `100` (Attachment C) are rendered, because their fields have the corresponding bits set.

## Attachment Addition

When an attempt is made to apply a new attachment, the system considers all four fundamental components.

Consider the example blaster, with has Attachment A and Attachment C:

```
Attachment bitmap    : 00000101
```

Suppose we wish to add Attachment B. First, the system uses the mutex of Attachment B to remove any attachments that are mutually exclusive to it by clearing all bits from the attachment bitmap present in the mutex:

```
Attachment bitmap    : 00000101
                 CLEAR ||||||||
Attachment B mutex   : 00000110
                     = ||||||||
Resulting bitmap     : 00000001
```

Then, the attachment bitfield of Attachment B is set in the attachment bitmask:

```
Attachment bitmap    : 00000101
                   SET ||||||||
Attachment B bitfield: 00000010
                     = ||||||||
Resulting bitmap     : 00000011
```

The resulting bitmap is then stored in the stack.

## Attachment Removal

Suppose we wish to now remove Attachment B. First, the system clears the bit corresponding to Attachment B's bitfield:

```
Attachment bitmap    : 00000011
                 CLEAR ||||||||
Attachment B bitfield: 00000010
                     = ||||||||
Resulting bitmap     : 00000001
```

Then, the mutex of Attachment B is combined with the attachment minumum and the attachment bitmap to determine if the
attachment requirements are met:

```
Attachment bitmap    : 00000001
                   AND ||||||||
Attachment minimum   : 00000010
                   AND ||||||||
Attachment B mutex   : 00000110
                     = ||||||||
Resulting bitmap     : 00000000
```

Since the resulting bitmap is zero, no attachments in the set defined by Attachment B's mutex are present. Therefore,
the required attachment from the attachment minimum is selected.

```
Attachment minimum   : 00000010
                   AND ||||||||
Attachment B mutex   : 00000110
                     = ||||||||
Required attachment  : 00000010
```

The attachment defined by the resulting bitmap is then applied.

```
Attachment bitmap    : 00000001
                   SET ||||||||
Required attachment  : 00000010
                     = ||||||||
Resulting bitmap       00000011
```

The resulting bitmap is then stored in the stack. In this example we removed Attachment B only to have it immediately added back, but in practice, any situation that results in an attachment mutually-exclusive set being empty will add the minimum attachment for that set back to the blaster.