Source: `com.parzivail.pswg.api.PswgContent`

# Overview

The **PSWG Content API** provides addon authors with a dedicated interface to both add new content to the mod without the typical overhead of content, and to modify content from the base mod (and other addons) during launch.

# Blaster API

This API allows addons to register new blasters, as well as modify existing ones.

## Registration

Method: `PswgContent::registerBlasterPreset(...)`

This method takes one or more `BlasterDescriptor` instances which describe all of the properties of a blaster to be registered.

### Parameters

TODO

## Modification

Event: `PswgContent.BLASTER_REGISTERED`

TODO