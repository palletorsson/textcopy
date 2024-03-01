
# Pick-Up Script Documentation

This document explains the functionality and setup of the `PickUp` script attached to an `Area3D` node in Godot, designed for pick-up items in a 3D game environment.

## Script Overview

- **Node Type:** `Area3D`
- **Purpose:** To manage interactions when the player character enters the area of the pick-up item, such as playing a sound and removing the item from the scene.

## Properties

- `@export var pick_up_sound_path : NodePath`: Exports a variable to the Inspector where you can assign the path to an `AudioStreamPlayer3D` node. This node will play the pick-up sound.

## Functions

### `_ready()`

- Connects the `body_entered` signal of the `Area3D` node to the `_on_body_entered` function within the script.
- Checks if the `AudioStreamPlayer3D` node exists at the specified `pick_up_sound_path`. If found, it assigns this node to the `pick_up_sound` variable and plays the sound as a test. If not found, it prints an error message.

### `_on_body_entered(body)`

- Triggered when a body enters the `Area3D`'s space.
- Checks if the body is the player (by comparing the `name` property) and if the `pick_up_sound` is not null.
- If conditions are met, it plays the pick-up sound, scales the item to zero (making it invisible), and starts a timer set to the length of the audio stream. When the timer times out, it calls `queue_free()` to remove the pick-up item from the scene.

## Setup Instructions

1. Attach this script to an `Area3D` node representing the pick-up item in your scene.
2. In the Inspector, set the `pick_up_sound_path` to the path of an `AudioStreamPlayer3D` node that is either a child of the pick-up item or located elsewhere in the scene.
3. Ensure your player node is named "Player" or adjust the script to match your player node's name.
4. Optionally, adjust the script to scale the item differently or use a different method for hiding the item upon pick-up.

This setup allows for an audible indication when the player picks up an item and removes the item from the scene.


# Game Management System

This document outlines the setup and interaction between the GameManager, HUD, and Player components in a Godot project.

## Overview

- **GameManager (Singleton):** Manages global game state variables (health, XP) and notifies the game of changes via signals.
- **HUD:** Displays the game state (health, XP) to the player, updating in response to GameManager signals.
- **Player:** The player character, capable of affecting game state variables, which are managed by the GameManager.

## Setup

### 1. GameManager Setup

- **Script:** Create `GameManager.gd` to hold game state variables and functions for updating these variables. Define signals for variable changes.

```gdscript
extends Node

signal health_changed(new_health)
signal xp_changed(new_xp)

var health: int = 100
var xp: int = 0

func update_health(value: int):
    health += value
    emit_signal("health_changed", health)

func update_xp(value: int):
    xp += value
    emit_signal("xp_changed", xp)
```

- **Singleton Registration:** Register `GameManager.gd` as an AutoLoad (Project > Project Settings > AutoLoad).

### 2. HUD Setup

- **Scene Structure:** In your main scene, add a `CanvasLayer` node with a `Control` node child. Inside `Control`, add UI elements like `ProgressBar` for health and `Label` for XP.
- **Script:** Attach a script to the `Control` node. Connect to `GameManager`'s signals to update UI elements when game state changes.

```gdscript
extends Control

onready var health_bar = $HealthBar
onready var xp_label = $XPLabel

func _ready():
    GameManager.connect("health_changed", self, "_on_health_changed")
    GameManager.connect("xp_changed", self, "_on_xp_changed")

func _on_health_changed(new_health):
    health_bar.value = new_health

func _on_xp_changed(new_xp):
    xp_label.text = "XP: %d" % new_xp
```

### 3. Player Setup

- **Movement and XP Gain:** Implement movement logic in your player script. Increase XP by calling `GameManager.update_xp()` based on movement.

```gdscript
extends KinematicBody3D

var last_position = Vector3()

func _ready():
    last_position = global_transform.origin

func _physics_process(delta):
    var moved_distance = global_transform.origin.distance_to(last_position)
    if moved_distance > 1:
        GameManager.update_xp(1)
        last_position = global_transform.origin
```

## Explanation

- **GameManager:** Central hub for game states, emitting signals on changes.
- **HUD:** Updates UI elements in response to GameManager signals.
- **Player:** Influences game states, with changes managed by the GameManager.

This setup promotes a modular and maintainable architecture.

# Gradient Wall Generator in Godot

This Godot script generates a wall consisting of tiles that are distributed according to a Gaussian (normal) distribution across the width of the wall. The tiles transition from black to white, creating a gradient effect. It's designed for 3D projects in Godot and is ideal for backgrounds, decorative elements, or any game component that benefits from a gradient pattern.

## Features

- Generates a grid of 3D tiles with customizable width and height.
- Distributes tile colors according to a Gaussian distribution, creating a gradient effect.
- Configurable tile size, wall dimensions, and distribution parameters.

## Setup

### Prerequisites

- Godot 3.x or newer.
- Two `PackedScene` assets for the black and white cube prefabs.

### Steps

1. **Cube Prefabs**:
    - Create 3D scenes for the black and white tiles with a `MeshInstance` containing a cube mesh and the respective materials.
    - Save these scenes as `PackedScene` resources.

2. **Script Preparation**:
    - Create a new GDScript file (e.g., `gradient_wall_generator.gd`) and insert the provided script code.

3. **Scene Configuration**:
    - In your 3D scene, add a `MeshInstance3D` node at the desired location for the wall.
    - Attach the `gradient_wall_generator.gd` script to this node.

4. **Prefab Assignment**:
    - With the `MeshInstance3D` node selected, assign the black and white cube `PackedScene` resources to the respective script variables in the Inspector.

5. **Parameter Adjustment** (Optional):
    - Modify the `Wall Width`, `Wall Height`, and `Tile Size` script variables in the Inspector to customize the wall's appearance.

6. **Scene Execution**:
    - Run the scene to view the dynamically generated gradient wall.

## Script Overview

```gdscript
extends MeshInstance3D

# Inspector-assigned variables
@export var black_cube_prefab : PackedScene
@export var white_cube_prefab : PackedScene
@export var wall_width: int = 48
@export var wall_height: int = 32
@export var tile_size: float = 1.0

func _ready():
    generate_wall()

func generate_wall():
    # Initializes and generates the gradient wall

func gaussian(value: float, mean: float, stdDev: float) -> float:
    # Gaussian distribution function
```

## Customization

- **Tile Prefabs**: Use different 3D models as prefabs to vary the wall's appearance.
- **Distribution Parameters**: Adjust the Gaussian function's `mean` and `stdDev` parameters to change the gradient's spread and center.
- **Dynamic Updates**: Extend the script to allow real-time adjustments to the gradient, potentially responding to game events or player actions.


# Random Tiled Wall Generator in Godot

This Godot script generates a wall consisting of tiles (cubes) that randomly swap colors between black and white over time. It's designed for 3D projects in Godot and provides a dynamic visual effect suitable for backgrounds, puzzles, or any game element that benefits from a changing pattern.

## Features

- Generates a grid of 3D tiles with customizable width and height.
- Alternates tile colors between black and white randomly at the start.
- Periodically swaps the color of random tiles to create a dynamic effect.
- Configurable tile size and swap frequency.

## Setup

### Prerequisites

- Godot 3.x or newer.
- Two `PackedScene` assets for the black and white cube prefabs.

### Steps

1. **Create Cube Prefabs**:
    - Create 3D cube scenes for the black and white tiles. Ensure each cube has a `MeshInstance` with a cube mesh and appropriate materials.
    - Save these scenes as `PackedScene` resources.

2. **Prepare the Script**:
    - Create a new GDScript (`random_tiled_wall_generator.gd`) and paste the provided script code into this file.

3. **Scene Setup**:
    - In your 3D scene, add a new `MeshInstance3D` node where you want the wall to appear.
    - Attach the `random_tiled_wall_generator.gd` script to this `MeshInstance3D` node.

4. **Assign Prefabs**:
    - Select the `MeshInstance3D` node with the script attached.
    - In the Inspector, assign the black and white cube `PackedScene` resources to the `Black Cube Prefab` and `White Cube Prefab` fields, respectively.

5. **Configure Parameters** (Optional):
    - Adjust the `Wall Width`, `Wall Height`, and `Tile Size` parameters in the Inspector to fit your needs.

6. **Run the Scene**:
    - Play the scene to see the generated wall with the dynamic tile swapping effect in action.

## Script Overview

```gdscript
extends MeshInstance3D

# Assign these in the Inspector
@export var black_cube_prefab : PackedScene
@export var white_cube_prefab : PackedScene
@export var wall_width: int = 48
@export var wall_height: int = 16
@export var tile_size: float = 0.1

var tiles = [] # To keep track of all tiles
var swap_count: int = 0 # Counter for swaps
var swapping_to_black: bool = true # Indicates whether to swap to black or white next
var rng = RandomNumberGenerator.new()

func _ready():
    randomize()
    generate_wall()
    swap_random_tile()

func generate_wall():
    # Initializes and generates the wall of tiles

func swap_random_tile():
    # Periodically swaps the color of a random tile
```

## Customization

- **Tile Prefabs**: Change the cube prefabs to any 3D model to create different effects.
- **Swap Logic**: Modify `swap_random_tile` to implement different swapping patterns or conditions.
- **Performance**: For large walls or performance-sensitive applications, consider optimizing tile management and updates.

# First-Person Controller Scene Setup in Godot

This guide documents the process of setting up a first-person controller scene with new features introduced in Godot 3.x, following the Lucky YouTube tutorial. The setup includes global illumination, fog, depth of field blurring, and a first-person character controller.

## Floor and Archway Setup

- Create a new 3D scene named "World".
- Add a `MeshInstance3D` with a cube mesh as the floor.
- Scale the cube to 1m x 1m x 1m.
- Apply a grid texture to the floor and enable triplanar mapping for uniform texture display.

## Archway with CSG

- Use CSGBox nodes to create an archway structure.
- Combine CSGBox nodes using CSG operations like subtraction to form the arch shape.
- Apply materials and adjust parameters to fix visual glitches and improve resolution.

## Environment Settings

- Create a new environment resource with a white background color.
- Set up ambient light, tone mapping (filmic), and Screen-Space Ambient Occlusion (SSAO).
- Enable SDFGI for global illumination to simulate light bounces and reflections.
- Configure volumetric fog parameters for atmospheric effects.

## Player Setup

- Add a `CharacterBody3D` node as the player.
- Attach a collision shape to the player for physical interactions.
- Attach a camera to the player node to serve as the player's viewpoint.
- Implement movement logic using input mapping for forwards, backward, left, and right.
- Add mouse look functionality with sensitivity settings and clamping for vertical rotation limits.

## Scripting

- Utilize the provided FPS controller template script in Godot, adapting it for changes in 3.x stable.
- Capture the mouse cursor within the game window for immersive first-person control.
- Handle input events for character movement, including walking, sprinting, crouching, and jumping.
- Apply gravity consistently with the project settings to match other physics objects.

## Materials and Textures

- Import grid textures and apply them to mesh instances.
- Create a material resource for the archway and configure its properties.
- Utilize the triplanar mapping for a consistent appearance on complex geometries.

## Debugging Tips

- For visual glitches in CSG operations, adjust the positions and dimensions of the CSG nodes.
- If the first-person controller does not respond as expected, check input mappings and script logic.

## Additional Resources

- Free grid textures by Kenney: [Kenney Assets](https://www.kenney.nl/assets)
- FPS controller script by Garbaj: [YouTube Tutorial](https://www.youtube.com/channel/UCb7A3-CWHgy5EejbfA9wbmw)

Remember to save your scene frequently and test the player's movement and environmental effects to ensure everything is working as intended.

---

Tutorial Reference: [Lucky YouTube Channel](https://www.youtube.com/channel/UCb7A3-CWHgy5EejbfA9wbmw)

# First-Person Controller Script Documentation

This script extends `CharacterBody3D` to implement a first-person character controller in Godot.

## Properties

- `current_speed`: The current movement speed of the character.
- `walking_speed`: The walking speed of the character.
- `sprinting_speed`: The sprinting speed of the character.
- `crouching_speed`: The speed of the character while crouching.
- `crouching_depth`: The amount by which the character "sinks" while crouching.
- `jump_velocity`: The initial velocity of the character when jumping.
- `mouse_sensitivity`: The sensitivity of the mouse movement.
- `lerp_speed`: The speed at which the character's head position interpolates while crouching/standing.

## Nodes

- `head`: A reference to the `Head_Node3D` node, which represents the character's head and camera.

## Gravity

- `gravity`: The gravity value is fetched from the project settings to ensure consistency with `RigidBody` nodes.

## Methods

### `_ready()`
Captures the mouse cursor when the game starts.

### `_input(event)`
Handles mouse movement for looking around. It rotates the character and the head node based on the mouse's relative movement.

### `_physics_process(delta)`
Handles the character's movement logic, including walking, sprinting, crouching, and jumping. It uses input actions to determine the desired movement and applies gravity when the character is not on the floor.

## Input Handling

- **Sprinting**: Holding the sprint key increases the character's speed.
- **Crouching**: Holding the crouch key decreases the character's height and speed.
- **Jumping**: Pressing the jump key applies an upward velocity if the character is on the floor.

## Movement

The script computes the direction of movement based on player input and uses linear interpolation (lerp) to smoothly transition movement changes. The character's velocity is updated accordingly, and the `move_and_slide` method is used for the actual movement and collision handling.

---

This script is part of a tutorial series by Lucky on YouTube. For more details and to see the script in action, visit the [Lucky YouTube Channel](#).


# Project Documentation

## GDScript Notes

### Random Number Generator (RNG)
- Each `MeshInstance3D` has its own RNG for generating random positions.
- The RNG is seeded using a combination of system time and a random integer to ensure uniqueness (`generate_seed_for_entity` function).
- Use `OS.get_unix_time()` instead of `Time.get_unix_time_from_system()` if you're using Godot 3.2 or later.

### Movement Logic
- The agent moves in a random direction every `time_to_next_step` seconds.
- Movement is restricted within an area defined by `area_size`.

### Shader Parameters
- The script updates the shader parameters `agent_position1` through `agent_position8` to reflect the agent's last eight positions.
- Positions are normalized to the range [0, 1] before being passed to the shader (`get_agent_position` function).

### Shader Material
- The script directly accesses the `ShaderMaterial` from the node it is attached to (`monitor = self as MeshInstance3D`).
- The shader parameters are set using the `set_shader_parameter` method.

### Making Materials Unique
- To ensure that each instance has its own random sequence, each `MeshInstance3D` node must have a unique material.
- Use the "Make Unique" option in the editor to duplicate the material for the node, or use `material_override.duplicate()` in the script.

## Shader Notes

### Uniform Variables
- Eight uniform variables (`agent_position1` through `agent_position8`) represent the last eight positions of the agent.

### Fragment Shader Logic
- A color effect is applied based on the distance from each agent position to the fragment's UV coordinates.
- The `smoothstep` function is used to create a soft threshold for the color blending effect.
- The `mix` function combines the effect color with the base color based on the calculated intensity.

### Color and Radius
- The base color is set to a magenta color, and a green color is added around each agent position.
- The radius for the effect is controlled by the `radius` variable.

## Best Practices and Tips

### Unique Instances
When duplicating nodes, make sure to make materials and scripts unique if you want different behavior for each instance.

### Shader Parameters
Be consistent with naming conventions when setting shader parameters from GDScript.

### Debugging
Use print statements or the Godot debugger to verify that variables and shader parameters are being set as expected.

### Seeding RNGs
Consider the implications of RNG seeding, especially in multiplayer games, to ensure that sequences are both random and synchronized across different clients when necessary.

### Version Control
Keep in mind the version of Godot you're using, as certain functions and methods might differ between versions.

---

Maintain this documentation alongside your project files or in your version control system to aid in future development and debugging.
1. **Aktivera Skriptfönstret**: 
   Öppna ett skriptfönster i Blender genom att justera layouten.

2. **Skapa Nytt Skript**: 
   I skriptfönstret, välj att skapa ett nytt dokument.

3. **Spara Filen**: 
   Spara ditt skript med ett lämpligt namn.

4. **Importera Blender Python API**:
   ```python
   import bpy

- Öppna **Blender** och skapa en kub genom att gå till menyn **Add > Mesh > Cube**.
- Byt layout till **Scripting** för att öppna skriptfönstret.
- I **Python-konsolen** nedtill, hitta raden som börjar med `bpy.ops.mesh.primitive_cube_add(...)`.
- Högerklicka på koden och välj **Copy to Clipboard**.
- Gå till texteditorn ovanför och klicka på **New** för att skapa ett nytt skript.
- Klistra in den kopierade koden i det nya skriptet.
- Nu kan du köra skriptet för att skapa en kub i scenen när du vill.

# Organizing Research Inspiration and Final Codebase

## Research Inspiration Organization

### 1. Dedicated Repository or Directory
- Separate repository or directory for research and inspiration.

### 2. Documentation
- Document research findings, ideas, and resources in markdown files.

### 3. Issue Tracking for Ideas
- Track research ideas with GitHub Issues.
- Use labels like "research", "idea", "inspiration".

### 4. Branches for Experimental Work
- Create branches for experimental ideas.
- Merge successful experiments into the main project.

### 5. Wiki for Comprehensive Documentation
- Use GitHub Wiki for detailed research documentation.

## Final Codebase Organization

### 1. Clean and Structured Repository
- Maintain a clean, well-structured repository for the final code.

### 2. Clear Directory Structure
- Organize code into directories based on functionality or modules.

### 3. Documentation
- Thorough documentation of setup, usage, and inline comments.

### 4. Version Control Best Practices
- Meaningful commit messages and a coherent commit history.
- Implement a branching strategy for managing the code.

### 5. Code Reviews and Pull Requests
- Use pull requests for new features or changes.
- Conduct code reviews for quality and consistency.

## Integration and Reference

- **Cross-Reference**: Link back to the research repository in the codebase documentation.
- **README Overview**: Brief overview of the research in the main `README.md` of the codebase.

## Tools and Automation

- **GitHub Actions**: Automate tasks like testing, linting, or deployment.
- **Research Tools Integration**: Integrate with research tools if possible.

## Periodic Review and Update

- **Regular Updates**: Keep both research and code repositories updated.
- **Reflect Back**: Review the research for new ideas to integrate into the codebase.

**Note**: Clear separation between research and coding phases ensures focus and clarity in the project development process.




## Spelmotorspecifika Skriptexempel

I denna README presenteras exempel på hur man skapar en enkel rotation och förstör ett objekt eller en nod baserat på dess avstånd från startpositionen. Exemplen är skrivna för tre olika spelmotorer: Unity (C#), Godot (GDScript) och Unreal Engine (C++).

### Unity C# Exempel:

```csharp
// MyRotation.cs
using UnityEngine;

public class MyRotation : MonoBehaviour
{
    public float rotationSpeed = 10f;
    private Vector3 originalPosition;

    void Start()
    {
        originalPosition = transform.position;
    }

    void Update()
    {
        transform.Rotate(Vector3.up * rotationSpeed * Time.deltaTime);

        if (Vector3.Distance(transform.position, originalPosition) > 10f)
        {
            Destroy(gameObject);
        }
    }
}
```

I Unity C#-exemplet skapar vi en klass som ärver från `MonoBehaviour`. Den roterar ett GameObject och förstör det om det rör sig för långt från sin ursprungliga position.

### Godot GDScript Exempel:

```gdscript
# LerpSkybox.gd
extends Spatial

export var rotation_speed = 10.0
var original_position

func _ready():
    original_position = global_transform.origin

func _process(delta):
    var rotation_amount = Vector3(0, rotation_speed * delta, 0)
    rotate_y(rotation_amount.y)

    if global_transform.origin.distance_to(original_position) > 10.0:
        queue_free()
```

I Godot GDScript-exemplet använder vi `Spatial` som bas för vår klass. Skriptet roterar en nod och tar bort den från scenen om den rör sig för långt från sin startposition.

### Unreal Engine C++ Exempel:

```cpp
// MyRotation.h
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "MyRotation.generated.h"

UCLASS()
class YOURPROJECTNAME_API AMyRotation : public AActor
{
    GENERATED_BODY()

public:    
    AMyRotation();

protected:
    virtual void BeginPlay() override;

public:    
    virtual void Tick(float DeltaTime) override;

    UPROPERTY(EditAnywhere, Category="Rotation")
    float RotationSpeed;

private:
    FVector OriginalPosition;
};

// MyRotation.cpp
#include "MyRotation.h"

AMyRotation::AMyRotation()
{
    PrimaryActorTick.bCanEverTick = true;

    RotationSpeed = 10.0f;
}

void AMyRotation::BeginPlay()
{
    Super::BeginPlay();
    
    OriginalPosition = GetActorLocation();
}

void AMyRotation::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);

    FRotator NewRotation = GetActorRotation();
    NewRotation.Yaw += RotationSpeed * DeltaTime;
    SetActorRotation(NewRotation);

    if (FVector::Dist(GetActorLocation(), OriginalPosition) > 10.0f)
    {
        Destroy();
    }
}
```

I Unreal Engine C++-exemplet använder vi en `AActor`-klass för att rotera en aktör i spelmiljön. Om aktören flyttar sig mer än 10 enheter från sin startposition, förstörs den.

---
Varje exempel visar en liknande funktionalitet anpassad till respektive spelmotor och programmeringsspråk.
