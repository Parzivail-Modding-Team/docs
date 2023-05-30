Source: `com.parzivail.pswg.api.PswgContent`

# Overview

The **PSWG Content API** provides addon authors with a dedicated interface to both add new content to the mod without the typical overhead of content, and to modify content from the base mod (and other addons) during launch.

# Blaster API

This API allows addons to register new blasters, as well as modify existing ones.

## Registration

* Method: `PswgContent::registerBlasterPreset(...)`
* Registration phase: `PswgAddon::onPswgReady`

This method takes one or more `BlasterDescriptor` instances which describe all of the properties of a blaster to be registered.

For a detailed explanation of the methods available in the builder, see the [BlasterDescriptor API](blaster-descriptor).

Registering a blaster will fire an event that allows other addons to modify the registered blaster, if required. See below for details about the blaster registration event.

## Complete Example

```java
PswgContent.registerBlasterPreset(
	new BlasterDescriptor(Resources.id("t21"), BlasterArchetype.HEAVY)
		// Allow automatic fire, burst fire, and stun bolts, and prevent wet usage
	    .firingBehavior(List.of(BlasterFiringMode.AUTOMATIC, BlasterFiringMode.BURST, BlasterFiringMode.STUN), BlasterWaterBehavior.NONE)
	    // This blaster weights 6.7 units, reduces speed by 0.5 when ADS, takes 10 ticks
	    // to arm when pulling out, and can hold 300 bolts before requiring a reload
	    .mechanicalProperties(6.7f, -0.5f, 10, 300)
		// It does 3 half-hearts of damage up to 188 blocks away with a 2nd-order cliff
		// damage falloff modifier
	    .damage(3f, 188f, Falloff.cliff(2))
		// It has red bolts with standard sizing
	    .bolt(ColorUtil.packHsv(0.98f, 1, 1), 1, 1)
		// It can fire a new bolt every 7 ticks in fully automatic mode
	    .autoParameters(7)
		// In burst fire, it can fire every two ticks up to three times, then it must
		// wait for (gap = 2) * (burstRepeatTime = 2) = 4 ticks before firing another
	    .burstParameters(2, 3, 2)
		// Firing accumulates up to 1.5 degrees of horizontal and 3 vertical recoil
	    .recoil(new BlasterAxialInfo(1.5f, 3))
		// Each bolt has a maximum deviation of half a degree from straight
	    .spread(new BlasterAxialInfo(0.5f, 0.5f))
		// It can hold 1008 units of heat, and it accumulates 95 units per shot. When
		// cooling manually, it drains 12 units per tick. If you overheat, you're
		// penalized an extra 20 units that must cool before the bar will change. The
		// remainder of the overheat will drain at 14 units per tick. If you stop firing,
		// heat will begin to passively drain after 100 ticks. If you hit gold in the
		// overheat minigame, you have a grace period of 80 ticks before heat will begin
		// to accumulate each round.
	    .heat(new BlasterHeatInfo(1008, 95, 12, 20, 14, 100, 80))
		// In the overheat minigame, the "primary" (blue) sector is halfway across
		// the bar and is 10% of the bar's width wide. The "secondary" (gold) is 30% of the way across and is 6% of the bar's width wide.
	    .cooling(new BlasterCoolingBypassProfile(0.5f, 0.05f, 0.3f, 0.03f))
		// This blaster supports the following attachments:
	    .attachments(
	        b -> b
				// A long barrel attachment that reduces spread, on layer 1. On the model, the visual part is called long_barrel, and it uses the base texture.
	            .attachment(1, "long_barrel", BlasterAttachmentFunction.REDUCE_SPREAD, BlasterAttachmentCategory.BARREL, "long_barrel", null)
				// A short barrel that increases damage, also on layer 1. On the model, the visual part is called short_barrel, and it uses the base texture.
	            .attachment(1, "short_barrel", BlasterAttachmentFunction.INCREASE_DAMAGE, BlasterAttachmentCategory.BARREL, "short_barrel", null)
				// A basic scope on layer 2. On the model, the visual part is called scope, and it uses the base texture.
	            .attachment(2, "scope", BlasterAttachmentFunction.DEFAULT_SCOPE, BlasterAttachmentCategory.SCOPE, "scope", null)
				// And a repeater on layer 3. It does not have a visual model component.
	            .attachment(3, "repeater", BlasterAttachmentFunction.ALLOW_AUTO, BlasterAttachmentCategory.INTERNAL_ORDNANCE_CONFIG)
				// We'll layer 1 require at least one attachment to be selected, 
				// and by default the blaster will choose the long barrel
	            .requireLayer(1, "long_barrel")
	    )
);
```

## Blaster Registration Event

* Event: `PswgContent.BLASTER_REGISTERED`
* Registration Phase: `PswgAddon::onPswgStarting`

TODO

