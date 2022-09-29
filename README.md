## Steps to get setup
* Install Unity 2020.3.35f (https://unity3d.com/unity/whats-new/2020.3.35)
* Copy (only those with waiting '.meta' files listed) game dlls from 'Shipbreaker_Data\Managed' into the Dll folder
* Update "shipbreaker_settings.json" with the correct game path (note that you need to use double backslash in the path, eg: "D:\\\\SteamGames\\\\steamapps\\\\common\\\\Hardspace Shipbreaker")
* Open unity, and run `Shipbreaker/Update game catalog`
* Restart unity
* Run `Shipbreaker/Update known assets`
* Delete the folder "FirstShip.Piepieonline" in "BepInEx\plugins\ModdedShipLoader\Ships"

## Steps to build
* Discard all changes to the SampleScene
* Run `Shipbreaker/Build`

## First steps
* Open SampleScene
* Navigate to "_CustomShips/ExampleBox"
* Drag "ExampleBox.prefab" into the scene hierarchy
 * If this doesn't look right, when the Editor has finished loading, `Shipbreaker/Force View Refresh`
* Move and rotate the atmospheric regulator into the box (Hint: Hold ctrl to snap to the grid)
* Click the ExampleBox in the hierarchy, and apply overrides
![Applying overrides](Docs/ApplyingOverrides.png?raw=true)
* Discard changes to the scene
* Build ("Shipbreaker/Build")
* Run the game, open the freeplay menu, and find "Example Box" at the end of the list
* Close the game
* In the hierarchy, disable "East", "CutPointER" and "CutPointEB"
* Move the airlock such that the inner wall sits inline with the open space, but not touching any walls
![Airlock position](Docs/AirlockPlacement.jpg?raw=true)
* Clone "CutPointEB", enable it, and move it so that it just touches the floor and the airlock inner wall (It'll need to be rotated 180 degrees)
* Build
* Open the ship in-game
* Congratulations, you've created your first ship!

## Creating custom ships
* 

## Custom ship notes
* The game doesn't work with negative scales

## Joints
* Joints are how the game knows to connect multiple separate assets
* Mandatory Joint Containers: These work by connecting all child objects. Useful for attaching trim to a wall, for example (can be cut in game, but won't come apart otherwise)
* StructurePartAsset - Joint setup asset: These work by attaching at runtime, whatever is connected to them - used by things like cutpoints?

## Rooms
* RoomContainerDefinition: Define how the room works. At the moment, I am reusing existing definitions only.
* RoomSubVolumeDefinition: (green transparent boxes) should fully encompass that piece's internal area - multiple can potentially be used for complex shapes? Must be a child of a RoomContainerDefinition
* RoomOpeningDefinition: (red transparent boxes), define how the volumes connect - including blocking (walls), allowing expansion (overlap) and doors (portals)
 * Flow axis (red arrows) defines which direction should the air flow upon breaching
* Room overlaps need to be carefully managed, as extra/misplaced overlaps will cause instant breaching when loading

## Texturing custom models
We can add custom models to the game, and set them so that they get the triplaner texture applied
An example of the process can be found in "Assets/_CustomShips/FirstShip/Components/Shell/ShellConnector.prefab"
1. Create a new material, with the "Fake/_Lynx/Surface/HDRP/Lit" shader (Found in "Assets/_CustomShips/_Common/Shaders/FakeLynxHDSurface.shader")
2. Apply the "Assets/_CustomShips/FirstShip/Materials/FirstShipWalls.png" texture to the BaseColorMap

The top half of this texture will get the triplaner texture applied (as the outside), and the rest will appear as normal

## Inspecting game content
* Find the '.prefab' in known_assets.json, and copy it's GUID
  * For example, the Mackerel airlock:
  * Find the line: "fd038d23f35b59747a22dec2f214b11f": "Assets/Content/Prefabs/Ship Kit/Nodes/Mackerel/Core Segments/PRF_Mackerel_Airlock.prefab",
  * Copy fd038d23f35b59747a22dec2f214b11f (no quotation marks)
* Paste it into 'Asset GUID' of the 'GameInspectorWindow' (If this is missing, open with `Shipbreaker/Show Game Inspector`)
* Click 'Load GameObject'

## Other important gotchas
* If something doesn't load correctly, make sure that everything that needs to be marked as addressable, is!
* I am caching all (game based) addressables at the moment. This means that the view of an addressable prefab won't update unless you remove it's prefab from "Assets/EditorCache"
* When using asset references, it must be the assets GUID, not the addressable name/path
* Spawnpoint hardpoint is not working

## Other interesting information
The BBI devs did us a massive favour, and released a bunch of documentation on their tools: https://drive.google.com/file/d/12gYTLHTgoeJLBlpVMsv4WfJC9cMHxcd5/view
While extremely interesting, it's direct application is limited.
Read it to get a better understanding of how the systems fit together (especially pressurisation), but don't expect processes detailed by them to work for us.

## Expected Unity Exceptions
There are a bunch of exceptions that I haven't managed to clean up yet, but will not impact the process, as follows:
* "Broken text PPtr. GUID 000..." - Appears when something is referring directly to in-game content that needs to be cached.
* "Could not extract GUID in text file" - As above