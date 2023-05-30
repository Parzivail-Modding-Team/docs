Source: `com.parzivail.pswg.features.blasters.data.BlasterDescriptor`

# `BlasterDescriptor` Builder

`BlasterDescriptor` follows the builder pattern, which allows blaster data to be easily composed to only modify the values required to describe your blaster.

## Constructor

Create a new BlasterDescriptor using the default values.

```java
public BlasterDescriptor(Identifier id, BlasterArchetype type)
```

Parameter | Description
--- | ---
`id` | The unique ID that this blaster will be referenced with throughout PSWG, and as a part of the item name.
`type` | The broad category that best describes the blaster. The value is used internally to determine how many hands are required to hold the blaster, etc.

## Method: `sound`

Specifies the blaster's firing sound.

```java
public BlasterDescriptor sound(Identifier sound)
```

Parameter | Description
--- | ---
`sound` | The ID of the sound that plays each time the blaster is fired.

### Returns

* this


## Method: `usePyrotechnics`

Specifies that the blaster should emit smoke and spark particles from the barrel when fired. Emulates the pyrotechnics used on the Jawa ionization blaster in A New Hope.

```java
public BlasterDescriptor usePyrotechnics()
```


### Returns

* this


## Method: `crosshair`

Specifies the blaster's default HUD crosshair.

```java
public BlasterDescriptor crosshair(int defaultCrosshair)
```

Parameter | Description
--- | ---
`defaultCrosshair` | The zero-based index into the HUD crosshair texture table.

### Returns

* this


## Method: `firingBehavior`

Specifies the blaster's available firing modes and behavior regarding water.

```java
public BlasterDescriptor firingBehavior(List<BlasterFiringMode> firingModes, BlasterWaterBehavior waterBehavior)
```

Parameter | Description
--- | ---
`firingModes` | A list of firing modes that the player can switch this blaster into. The order provided here is the order the player will cycle through them.
`waterBehavior` | Determines how the blaster will behave when fired at or while submerged in water.

### Returns

* this


## Method: `mechanicalProperties`

Specifies properties related to the blaster's physical attributes.

```java
public BlasterDescriptor mechanicalProperties(float weight, float adsSpeedModifier, int quickdrawDelay, int magazineSize)
```

Parameter | Description
--- | ---
`weight` | The weight of the blaster, in units (reserved for future use).
`adsSpeedModifier` | The player speed attribute coefficient used to slow down the player. When ADS, the player's speed is multiplied by this value to determine the new speed of the player with the equation $AdsSpeed_{Player} = adsSpeedModifier \cdot BaseSpeed_{Player}$.
`quickdrawDelay` | The delay, in ticks, it takes for this blaster to become "ready" after scrolling to it in your hotbar.
`magazineSize` | The size of the blaster's internal "magazine" of rounds. This is the maximum number of shots the blaster will attempt to draw from power packs in the inventory when the internal magazine is empty.

### Returns

* this


## Method: `damage`

Specifies the blaster's combat characteristics.

```java
public BlasterDescriptor damage(float damage, float range, Function<Double, Double> damageFalloff)
```

Parameter | Description
--- | ---
`damage` | The maximum amount of half-hearts a single blaster bolt can deal to a target. The actual damage value may be lower than this, as described in `damageFalloff`.
`range` | The maximum range of the fired bolts, in blocks.
`damageFalloff` | A function that takes in a value $x$ as a fraction $0\lt x\lt 1$ that corresponds to the percentage of `range` the target is away (i.e. $\frac{d_{target}}{range}$) and returns the percentage of the maximum damage as a fraction $0\le x \le 1$ that should be dealt to a target at that range.

### Returns

* this


## Method: `bolt`

Specifies the blaster's bolt presentation.

```java
public BlasterDescriptor bolt(int boltColor, float boltLength, float boltRadius)
```

Parameter | Description
--- | ---
`boltColor` | The color of the bolt, as a packed HSV integer (See `com.parzivail.util.math.ColorUtil::packHsv`).
`boltLength` | The length coefficient of the blaster bolt relative to a standard bolt.
`boltRadius` | The radius coefficient of the blaster bolt relative to a standard bolt.

### Returns

* this


## Method: `autoParameters`

Specifies the blaster's parameters when automatic-firing. Only required if "automatic" is a valid firing mode for this blaster.

```java
public BlasterDescriptor autoParameters(int automaticRepeatTime)
```

Parameter | Description
--- | ---
`automaticRepeatTime` | The time, in ticks, between two automatic rounds being fired.

### Returns

* this


## Method: `burstParameters`

Specifies the blaster's parameters when burst-firing. Only required if "burst" is a valid firing mode for this blaster.

```java
public BlasterDescriptor burstParameters(int burstRepeatTime, int burstSize, int burstGap)
```

Parameter | Description
--- | ---
`burstRepeatTime` | The time, in ticks, between two burst rounds being fired.
`burstSize` | The size of a burst, in rounds.
`burstGap` | The minimum time, in ticks, between two successive bursts.

### Returns

* this


## Method: `recoil`

Specifies the blaster's recoiling effects on the player who fired it.

```java
public BlasterDescriptor recoil(BlasterAxialInfo recoil)
```

Parameter | Description
--- | ---
`recoil` | The maximum extent of recoil in the axial directions, in degrees.

### Returns

* this


## Method: `spread`

Specifies the blaster's bolt spreading inaccuracy.

```java
public BlasterDescriptor spread(BlasterAxialInfo spread)
```

Parameter | Description
--- | ---
`spread` | The maximum extent of bolt spreading in the axial directions, in degrees.

### Returns

* this


## Method: `heat`

Specifies the blaster's heating characteristics.

```java
public BlasterDescriptor heat(BlasterHeatInfo heat)
```

Parameter | Description
--- | ---
`heat` | An instance of a heating characteristics object.

### Returns

* this


## Method: `cooling`

Specifies the blaster's cooling "minigame" parameters.

```java
public BlasterDescriptor cooling(BlasterCoolingBypassProfile cooling)
```

Parameter | Description
--- | ---
`cooling` | An instance of a cooling characteristics object.

### Returns

* this


## Method: `attachments`

Specifies this blaster's attachments. See below for the API methods available through the builder.

```java
public BlasterDescriptor attachments(Consumer<BlasterAttachmentBuilder> b)
```

Parameter | Description
--- | ---
`b` | A consumer for an attachment builder instance to append attachments to.

### Returns

* this


## Method: `mapWithAttachment`

Given a set of attachments, selects a value from a map that correlates a set of functions to a set of related values.

```java
public Optional<T> mapWithAttachment(int attachmentBitmask, HashMap<BlasterAttachmentFunction, T> map)
```

Parameter | Description
--- | ---
`attachmentBitmask` | The populated attachment values of a blaster.
`map` | The map used to find the appropriate return value from the present attachments.
`<T>` | The generic type, inferred from the map value type, to return.

### Returns

* An optional value, if any, containing the first matching value from the map.


## Method: `mapWithAttachment`

Given an attachment function and related value, determine if the given blaster meets the requirements to provide that value.

```java
public Optional<T> mapWithAttachment(int attachmentBitmask, BlasterAttachmentFunction function, T value)
```

Parameter | Description
--- | ---
`attachmentBitmask` | The populated attachment values of a blaster.
`function` | The attachment function one of the blaster's attachments must have to provide the value.
`value` | The value to provide should the requisite attachment be present.
`<T>` | The generic type, inferred from the value type, to return.

### Returns

* An optional value, if any, containing the matching value.


## Method: `stackWithAttachment`

Combines a set of stacking coefficients based on the present attachments of the given blaster.

```java
public float stackWithAttachment(int attachmentBitmask, HashMap<BlasterAttachmentFunction, Float> map)
```

Parameter | Description
--- | ---
`attachmentBitmask` | The populated attachment values of a blaster.
`map` | The map used to correlate attachment function with coefficient value.

### Returns

* The coefficient generated by the equation: $presentValueCoefficient_{1} \cdot presentValueCoefficient_{2} \cdot \ldots \cdot presentValueCoefficient_{n}$

# Blaster Attachment Builders

The `BlasterAttachmentBuilder` allows a set of possible attachments to be defined and placed on a series of groups, each group allowing one attachment to be attached at a time. Some groups can optionally require one of the allowed attachments to be in place at _all_ times (for example, a variation of weapon stock), while others may allow no attachments (e.g. a set o scopes).

For more information about how blaster attachments are stored and manipulated internally (including how they're stored in itemstacks), see the [blaster attachment bitmap](blaster-attachment-bitmaps.md) page.

## Method: `attachment`

Adds an attachment option without a visual component.

```java
public BlasterAttachmentBuilder attachment(int layer, String id, BlasterAttachmentFunction function, BlasterAttachmentCategory category)
```

Parameter | Description
--- | ---
`layer` | The attachment layer this attachment will belong to.
`id` | The ID this attachment will have.
`function` | The function that this attachment will have.
`category` | The category (and by extension, icon) the attachment falls under.

### Returns

* this


## Method: `attachment`

Adds an attachment option with a visual component.

```java
public BlasterAttachmentBuilder attachment(int layer, String id, BlasterAttachmentFunction function, BlasterAttachmentCategory category, String visualComponent, Identifier texture)
```

Parameter | Description
--- | ---
`layer` | The attachment layer this attachment will belong to.
`id` | The ID this attachment will have.
`function` | The function that this attachment will have.
`category` | The category (and by extension, icon) the attachment falls under.
`visualComponent` | The part name within the model that should be hidden or shown with regard to this attachment's presence.
`texture` | The texture identifier this attachment's model part should render with, or null if it should use the blaster's main texture.

### Returns

* this


## Method: `preset`

Sets a specific attachment to be the "default" attachment of that attachment layer.

```java
public BlasterAttachmentBuilder preset(int layer, String presetId)
```

Parameter | Description
--- | ---
`layer` | The attachment layer to select a preset for.
`presetId` | The ID of the attachment that will be enabled by default on that layer.

### Returns

* this


## Method: `requireLayer`

Sets a specific layer to require an attachment to be present on that layer, and sets the given attachment to be the "default" attachment of that layer.

```java
public BlasterAttachmentBuilder requireLayer(int layer, String presetId)
```

Parameter | Description
--- | ---
`layer` | The attachment layer to require an attachment on and select a preset for.
`presetId` | The ID of the attachment that will be enabled by default on that layer.

### Returns

* this