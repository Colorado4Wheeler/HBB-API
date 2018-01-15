# Homebridge Buddy 3rd Party Developer API

This is an easy way to integrate your plugin with Homebridge Buddy and HomeKit.  Most of the heavy lifting is done by Homebridge Buddy so the changes to your plugin are minor.  

Once implemented you can directly integrate your plugin device with Homebridge Buddy without the need for the user to go into the server configuration and link to it.  You have full control over how to treat this device, so if you feel your device is best represented in HomeKit as, say, a switch rather than a dimmer then you can default to that and remove the users ability to change it.

## Homebridge Buddy

Once you integrate this API, Homebridge Buddy will do the rest.  As soon as you enable the link to Homebridge Buddy then as soon as that device has been created it will be added to the server.  The same goes for updating a device or deleting a device from Indigo.  All of these functions will prompt Homebridge Buddy to take action on it's own configuration.  There is nothing complex for you to do with your plugin to make these things happen.

The primary reason for making sure Homebridge Buddy does the majority of the work is so that as this API matures there is little need for you to change your plugin code to be compatible.  The changes can be implemented in Homebridge Buddy's code instead.

**API support is available in Homebridge Buddy 1.0.7 or newer.**

## Integrating The HBB API Into Your Plugin

There is one file to copy, a few lines to add to your Devices.xml for the device(s) to integrate and a few lines to add to your plugin, that's it.

### The Library File

Download the `hbb.py` file to your plugins file structure.  You can either leave it in the root folder with `plugin.py` or you can create a subfolder, it's up to you.  Just remember that if you create a subfolder you will need to create a file in that subfolder to accompany the `hbb.py` called `__init__.py` so that Python can import the API.

Once you have copied the file to your plugin, you'll need to import it into Python using the following code:

	from hbb import HomebridgeBuddy
	hbb = HomebridgeBuddy()

If you opted to add this a subfolder (in our case one called "lib") then you would import it in this manner:

	from lib.hbb import HomebridgeBuddy
	hbb = HomebridgeBuddy()

That's all there is to it.

### Devices.xml Modification

This is a minor modification needed for `Devices.xml` so that the ability to link to Homebridge Buddy is available to your users.  Feel free to move these fields around how you see fit just so long as ALL the fields remain in your device, hidden or visible, and that the ID of these fields do not change.

	<!-- Start Homebridge Buddy Integration Block -->		
	<Field type="checkbox" id="hbbIntegrated" defaultValue="false" hidden="false">
		<Label>Enable for HomeKit</Label>
		<Description>Integrate this device into Homebridge Buddy</Description>
		<CallbackMethod>hbbIntegrationFieldChange</CallbackMethod>
	</Field>

	<Field type="menu" id="hbbServer" defaultValue="1953136859" visibleBindingId="hbbIntegrated" visibleBindingValue="true" alwaysUseInDialogHeightCalc="true">
		<Label>Homebridge server:</Label>
		<List class="self" filter="" method="hbbIntegrationServerList" dynamicReload="true"/>
		<CallbackMethod>hbbIntegrationFieldChange</CallbackMethod>
	</Field>

	<Field type="menu" id="hbbTreatAs" defaultValue="none" visibleBindingId="hbbIntegrated" visibleBindingValue="true" alwaysUseInDialogHeightCalc="true">
		<Label>Homebridge device type:</Label>
		<List class="self" filter="" method="hbbIntegrationTreatAsList" dynamicReload="true"/>
		<CallbackMethod>hbbIntegrationFieldChange</CallbackMethod>
	</Field>

	<!-- not yet implemented in API but here for future proofing -->	
	<Field type="textfield" id="hbbAlias" defaultValue="" visibleBindingId="hbbIntegrated" visibleBindingValue="true" alwaysUseInDialogHeightCalc="true" hidden="true">
		<Label>Alias Name:</Label>
	</Field>

	<Field type="separator" id="sep_hbbConnector" visibleBindingId="hbbIntegrated" visibleBindingValue="true" alwaysUseInDialogHeightCalc="true"/>
	<!-- End Homebridge Buddy Integration Block -->

Once integrated your device will appear similar to the following sample device, note that if the box is unchecked then you will not see the fields nor the separator.

![](https://github.com/Colorado4Wheeler/HBB-API/blob/master/images/example1.png)

### Functions To Add To Your `plugin.py`

The following functions are called by the device fields to verify and then connect to the Homebridge Buddy plugin.  They will verify that it is installed and of an appropriate version for this version of API.

	def hbbCheckForPlugin (self): return hbb.checkForPlugin ()
	def hbbIntegrationFieldChange (self, valuesDict, typeId, devId): return hbb.integrationFieldChange (valuesDict, typeId, devId)
	def hbbIntegrationServerList (self, filter="", valuesDict=None, typeId="", targetId=0): return hbb.integrationServerList (filter, valuesDict, typeId, targetId)
	def hbbIntegrationTreatAsList (self, filter="", valuesDict=None, typeId="", targetId=0): return hbb.integrationTreatAsList (filter, valuesDict, typeId, targetId)
