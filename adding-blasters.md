# Adding a new blaster

Blasters require three things to be loaded by PSWG and made available in-game:

* Registration during `PswgAddon::onPswgReady` using the PSWG Content API
* A model, in P3D format
* A texture

In this guide, we'll walk you through adding the Westar Carbine blaster.

## Terminology

When speaking about blasters, it's relevant to incorporate both common Minecraft terms and some terms unique to blasters.

| Term | Definition |
| --- | --- |
| Tick | The interval at which the game updates. Ideally 1/20th of a second. |
| Cooling | The period during which the blaster is losing heat instead of accumulating it |
| Venting | The period during which the blaster is cooling down and cannot be fired |
| Overheating | The condition when a blaster is fired without manually venting heat for long enough that the heat accumulated is greater than the heat capacity, and the player is forced to wait for the blaster to completely cool before firing again |
| Overcharge | A grace period during which the blaster can be fired without accumulating heat |
| Cooling bypass | A way to end an overheat vent early by completing a timing-based minigame on the blaster cooldown bar |
| Primary bypass | The first, typically wider, cooling bypass target |
| Secondary bypass | The second, typically more difficult, cooling bypass target which, if triggered, provides an overcharge |

## Preparing the assets

### Location

The mod searches for blaster models in the `assets/<modid>/models/item/blaster` directory of a your addon. The name of the model should be the same as the ID used when registering your blaster. Textures for each model should share the same filename in the directory `assets/<modid>/textures/item/model/blaster` (unless specified otherwise, see the attachment section of the [Blaster Descriptor API](blaster-descriptor.md) page for more details).

### Format

Blasters use a custom model format called P3D. For more information on P3D models and how to make them, see the [P3D Models](p3d-models.md) page. In general, models can be created in Blockbench or any popular model editor of your choice.

### Model conventions

In addition to the conventions listed on the [P3D Models](p3d-models.md) page, blasters use some additional rules of thumb:

* Blasters should be scaled such that the trigger is roughly three pixels (30cm) tall. This is a good reference because, across weapons, the trigger, and therefore trigger guard, does not scale with the caliber or firepower of the weapon. You may find it helpful to compare your model against a blaster model in PSWG's [model resource folder](https://github.com/Parzivail-Modding-Team/GalaxiesParzisStarWarsMod/tree/master/resources/models).
* Blasters may **optionally** define any of the following sockets:
  * `main_hand`: The position that will be gripped by the player's main hand. If undefined, the model origin will be used.
  * `off_hand`: The position *and orientation* of the player's offhand. If undefined, no offhand will be rendered.
  * `muzzle_flash`: The position and orientation of the default (see `muzzle_flash.[x]`) muzzle flash animation.
  * `muzzle_flash.[x]`: The position and orientation of the muzzle flash when an attachment named `x` is attached. For example: A hypothetical blaster has an attachment (and Object) named `long_barrel`. If a socket named `muzzle_flash.long_barrel` exists, the muzzle flash will render at that socket instead of the default `muzzle_flash` socket when that attachment is attached.
* All attachments should be visible when exported and in the location they should appear in-game. "Overlapping" attachments are normal and expected if multiple attachments are mutually exclusive (e.g. two variations of stock, or two different scopes). See the attachment section, specifically layers, of the [Blaster Descriptor API](blaster-descriptor.md) page for more details.

TODO: screenshots, example Westar assets, walkthrough

## Blaster registration

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