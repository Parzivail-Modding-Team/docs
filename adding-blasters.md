# Adding a new blaster

Blasters require three things to be loaded by PSWG and made available in-game:

* Registration during `PswgAddon::onPswgReady` using the PSWG Content API
* A model, in P3D format
* A texture

In this guide, you will be walked through adding the Westar Carbine blaster.

## Preparing the assets

TODO

## Blaster Registration

Finally, the blaster must be registered using the Content API during the addon initialization phase:

```java
public class MyAddon implements PswgAddon
{
	@Override
	public void onPswgReady()
	{
		PswgContent.registerBlasterPreset(
			new BlasterDescriptor(new Identifier("myaddon", "westar_carbine"), BlasterArchetype.RIFLE)
				// Allow automatic fire, semi-automatic fire, and stun bolts. No wet usage is permitted.
				.firingBehavior(List.of(BlasterFiringMode.AUTOMATIC, BlasterFiringMode.SEMI_AUTOMATIC, BlasterFiringMode.STUN), BlasterWaterBehavior.NONE)
				// This blaster weights 6.7 units, reduces speed by 0.5 when ADS, takes 10 ticks
				// to arm when pulling out, and can hold 300 bolts before requiring a reload
				.mechanicalProperties(6.7f, -0.5f, 10, 300)
				// It does 3 half-hearts of damage up to 188 blocks away with a 2nd-order cliff
				// damage falloff modifier
				.damage(3f, 188f, Falloff.cliff(2))
				// It has yellow bolts with standard sizing
				.bolt(ColorUtil.packHsv(0.1667f, 1, 1), 1, 1)
				// It can fire a new bolt every 7 ticks in fully automatic mode
				.autoParameters(7)
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
						// A basic scope attachment, with a model component with the object name "scope"
						.attachment(1, "scope", BlasterAttachmentFunction.DEFAULT_SCOPE, BlasterAttachmentCategory.SCOPE, "scope", null)
						// An optional stock, with a model component with the object name "scock"
						.attachment(2, "stock", BlasterAttachmentFunction.REDUCE_RECOIL, BlasterAttachmentCategory.STOCK, "stock", null)
						// An optional underbarrel, with a model component with the object name "underbarrel"
						.attachment(3, "underbarrel", BlasterAttachmentFunction.REDUCE_SPREAD, BlasterAttachmentCategory.BARREL, "underbarrel", null)
				)
		);
	}
}
```