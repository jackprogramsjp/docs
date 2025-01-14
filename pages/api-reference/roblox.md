# Roblox

Built-in library for manipulating Roblox place & model files

#### Example usage

```lua
local fs = require("@lune/fs")
local roblox = require("@lune/roblox")

-- Reading a place file
local placeFile = fs.readFile("myPlaceFile.rbxl")
local game = roblox.deserializePlace(placeFile)

-- Manipulating and reading instances - just like in Roblox!
local workspace = game:GetService("Workspace")
for _, child in workspace:GetChildren() do
	print("Found child " .. child.Name .. " of class " .. child.ClassName)
end

-- Writing a place file
local newPlaceFile = roblox.serializePlace(game)
fs.writeFile("myPlaceFile.rbxl", newPlaceFile)
```

## Functions

### deserializePlace

Deserializes a place into a DataModel instance.

This function accepts a string of contents, _not_ a file path. If reading a place file from a file
path is desired, `fs.readFile` can be used and the resulting string may be passed to this function.

#### Example usage

```lua
local fs = require("@lune/fs")
local roblox = require("@lune/roblox")

local placeFile = fs.readFile("filePath.rbxl")
local game = roblox.deserializePlace(placeFile)
```

#### Parameters

-   `contents` The contents of the place to read

#### Returns

-   DataModel

---

### deserializeModel

Deserializes a model into an array of instances.

This function accepts a string of contents, _not_ a file path. If reading a model file from a file
path is desired, `fs.readFile` can be used and the resulting string may be passed to this function.

#### Example usage

```lua
local fs = require("@lune/fs")
local roblox = require("@lune/roblox")

local modelFile = fs.readFile("filePath.rbxm")
local instances = roblox.deserializeModel(modelFile)
```

#### Parameters

-   `contents` The contents of the model to read

#### Returns

-   { Instance }

---

### serializePlace

Serializes a place from a DataModel instance.

This string can then be written to a file, or sent over the network.

#### Example usage

```lua
local fs = require("@lune/fs")
local roblox = require("@lune/roblox")

local placeFile = roblox.serializePlace(game)
fs.writeFile("filePath.rbxl", placeFile)
```

#### Parameters

-   `dataModel` The DataModel for the place to serialize

-   `xml` If the place should be serialized as xml or not. Defaults to `false`, meaning the place
    gets serialized using the binary format and not xml.

#### Returns

-   string

---

### serializeModel

Serializes one or more instances as a model.

This string can then be written to a file, or sent over the network.

#### Example usage

```lua
local fs = require("@lune/fs")
local roblox = require("@lune/roblox")

local modelFile = roblox.serializeModel({ instance1, instance2, ... })
fs.writeFile("filePath.rbxm", modelFile)
```

#### Parameters

-   `instances` The array of instances to serialize

-   `xml` If the model should be serialized as xml or not. Defaults to `false`, meaning the model
    gets serialized using the binary format and not xml.

#### Returns

-   string

---

### getAuthCookie

Gets the current auth cookie, for usage with Roblox web APIs.

Note that this auth cookie is formatted for use as a "Cookie" header, and that it contains
restrictions so that it may only be used for official Roblox endpoints. To get the raw cookie value
without any additional formatting, you can pass `true` as the first and only parameter.

#### Example usage

```lua
local roblox = require("@lune/roblox")
local net = require("@lune/net")

local cookie = roblox.getAuthCookie()
assert(cookie ~= nil, "Failed to get roblox auth cookie")

local myPrivatePlaceId = 1234567890

local response = net.request({
	url = "https://assetdelivery.roblox.com/v2/assetId/" .. tostring(myPrivatePlaceId),
	headers = {
		Cookie = cookie,
	},
})

local responseTable = net.jsonDecode(response.body)
local responseLocation = responseTable.locations[1].location
print("Download link to place: " .. responseLocation)
```

#### Parameters

-   `raw` If the cookie should be returned as a pure value or not. Defaults to false

#### Returns

-   string?

---

### getReflectionDatabase

Gets the bundled reflection database.

This database contains information about Roblox enums, classes, and their properties.

#### Example usage

```lua
local roblox = require("@lune/roblox")

local db = roblox.getReflectionDatabase()

print("There are", #db:GetClassNames(), "classes in the reflection database")

print("All base instance properties:")

local class = db:GetClass("Instance")
for name, prop in class.Properties do
	print(string.format(
		"- %s with datatype %s and default value %s",
		prop.Name,
		prop.Datatype,
		tostring(class.DefaultProperties[prop.Name])
	))
end
```

#### Returns

-   Database

---
