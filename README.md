
# Game Management System Update Guide

This guide provides a comprehensive overview of updating the Game Management System with a new theming style, focusing on the interaction between `GameManager.gd`, `HUD.gd`, and `Player.gd` scripts in a Godot 4 project.

## GameManager.gd

This script manages the game's state, such as player health and experience points (XP), and emits signals when these values change.

```gdscript
extends Node

signal health_changed(new_health)
signal xp_changed(new_xp)

var health: int = 100
var xp: int = 0

func update_health(value: int):
    health = value
    emit_signal("health_changed", health)

func update_xp(value: int):
    xp += value
    emit_signal("xp_changed", xp)
```

## HUD.gd

The HUD script listens for signals from the `GameManager` and updates UI elements like health bars and XP labels accordingly.

```gdscript
extends Control

@onready var health_bar = $HealthBar
@onready var xp_label = $XPLabel

func _ready():
    var game_manager = get_node("/root/GameManager")
    game_manager.connect("health_changed", self, "_on_health_changed")
    game_manager.connect("xp_changed", self, "_on_xp_changed")

    _on_health_changed(game_manager.health)
    _on_xp_changed(game_manager.xp)

func _on_health_changed(new_health):
    health_bar.value = new_health

func _on_xp_changed(new_xp):
    xp_label.text = "XP: %d" % new_xp
```

## Player.gd

The Player script handles movement and actions. It communicates with `GameManager` to update health and XP based on player actions.

```gdscript
extends CharacterBody3D

var current_speed = 5.0
# Other variables and constants...

func _ready():
    # Player initialization...

func _physics_process(delta):
    # Handle player movement and actions...

    # Update health and XP based on conditions
    if some_condition:
        var game_manager = get_node("/root/GameManager")
        game_manager.update_xp(xp_amount)
        game_manager.update_health(new_health_value)
```

## Theme Integration

Ensure the theme is applied consistently across all UI elements in the `HUD.gd` script. Apply the theme at a high level, such as in `GameManager` or the root UI node, for consistent UI theming.

## Summary

- `GameManager.gd` handles the game state and emits signals for health and XP changes.
- `HUD.gd` updates UI elements in response to these signals.
- `Player.gd` controls movement and actions, updating the game state through `GameManager`.
- Apply the theme at a high level to ensure UI consistency.

# Basic Guide to Theming in Godot 4

## Introduction
Theming in Godot 4 allows you to create a consistent and visually appealing user interface (UI) for your game. This tutorial provides a basic guide on setting up UI theming in Godot 4.

## Getting Started
1. **Create a UI Scene**: Begin by creating a new user interface scene in your project. Start with a `Control` node at the root and rename it to `GameplayUI`.
2. **Save the UI Scene**: Save your `GameplayUI` as a scene (`.tscn`) inside a folder named `UI` for better organization.
3. **Add Visual Elements**: Add a `Panel` node to your `GameplayUI`. Panels serve as a base for other UI elements like labels and buttons. Adjust the size of the panel as needed.

## Theming the UI
1. **Create a Theme**: Select the `Panel` node, go to the "Control" section in the Inspector, and create a new theme (`New Theme`).
2. **Save the Theme**: Save the theme as a resource (`.tres`) in the `UI` folder. This allows the theme to be reused across different parts of your game.
3. **Customize the Theme**: Double-click on the theme resource to open the theme editor. Here you can customize various components like buttons, labels, panels, and more.

### Customizing a Panel
1. **Add Panel Type**: In the theme editor, add the `Panel` type to customize.
2. **Override Default Style**: Override the default style box (`StyleBoxFlat`) for the panel. This allows you to change properties like the background color.
3. **Change Background Color**: Click on the overridden `StyleBoxFlat` to open its properties. Change the `Background Color` to your desired color.

### Changing Default Font and Size
1. **Access Theme Settings**: Select the `GameplayUI` node and find the theme settings in the Inspector.
2. **Set Default Font**: Drag and drop a font file (`.ttf` or `.otf`) into the `Default Font` slot to change the font used by UI elements.
3. **Adjust Font Size**: Increase the `Default Font Size` to make the text larger and more visible in your game.

### Using Multiple Themes
1. **Create Child Themes**: You can create separate themes for different parts of your UI to have specific sections look different from the base theme.
2. **Override Properties**: Child themes can override properties set in the base theme. Apply these child themes to specific nodes where you want the customizations to apply.

### Theme Overrides for Specific Controls
1. **Use Theme Overrides**: For controls that need unique settings, use theme overrides directly on the control node to customize properties like font, color, and more without affecting other controls.




# Object Pick Up and Rotation Using Raycasting in Godot

## Overview
This tutorial demonstrates how to enable a first-person character to pick up, hold, rotate, and release objects using raycasting in Godot. It's perfect for games requiring interaction with objects, such as puzzle games or adventure games.

## Prerequisites
- Basic understanding of Godot Engine and GDScript.
- A Godot project with a 3D scene setup.
- A Camera3D node set up for first-person view.

## Steps

### 1. Prepare the Scene:
   - Ensure your scene has a `Camera3D` node. This script should be attached to that node.
   - Add a `RayCast3D` node as a child of the camera. Name it `RayCast3D`.
   - Create an empty `Spatial` node as a child of the camera for holding objects in front of the player. Name it `HoldPosition`.
   - Ensure your interactable objects are in a group named "Pickable". You can add objects to a group via the Node tab in the Godot editor.

### 2. Setup the Script:
   - Attach the provided script to your `Camera3D` node.
   - Ensure the `@onready var` paths correctly point to your `RayCast3D` and `HoldPosition` nodes.
   - If you have a specific node where you want to release objects (e.g., `World_Node3D`), adjust the path in the `release_object` function.

### 3. Raycasting Configuration:
   - In the Godot editor, select the `RayCast3D` node and configure its properties. Ensure it's enabled and set the `Cast To` property to define the ray direction and length (e.g., `Vector3(0, 0, -1)` for casting forward).

### 4. Collision Layers:
   - Configure collision layers and masks to ensure the raycast only interacts with objects you intend to pick up. This is done in the `perform_raycast` function and the Collision tab for your objects.

### 5. Input Map:
   - Go to "Project" > "Project Settings" > "Input Map" and add an action named "pick_up" with the desired key or button.

### 6. Testing:
   - Run the scene and use the assigned input to pick up, hold, and release objects. Objects should rotate while held.

### 7. Debugging:
   - If the held object does not appear in front of the camera, adjust the `HoldPosition` node's transform in the editor.
   - Use the `debug_line` node to draw a line from the camera to the raycast's target point for visual debugging.

## Script Explanation
- The script uses a raycast to detect interactable objects and allows the player to pick up and hold an object in front of the camera.
- The held object is rotated continuously around its Y-axis while being held.
- Upon release, the object's collision is temporarily disabled to prevent immediate collision with the player, then re-enabled shortly after.
- Objects are returned to the world with a slight offset in front of the player.

## Tips
- Experiment with the `ray_length`, `rotation_speed`, and release position offset to achieve desired gameplay effects.
- To enhance visual feedback, consider adding highlight effects or sounds upon picking up or releasing objects.

This documentation outlines the setup process and functionality of the script. Tailor the instructions and script paths to fit your specific project structure and requirements.


# Intro to Shaders in Godot

This guide is designed to demystify shaders in Godot Engine and provide you with a basic understanding of how to create and apply shaders to achieve various visual effects.

## Understanding Shaders

Shaders are powerful tools that allow you to programmatically alter the appearance of your materials. Unlike standard materials, which you modify using UI sliders and buttons, shaders require you to write code. However, this gives you much more flexibility and control over the final appearance.

### Key Concepts

- **Shader Types**: In Godot, you mainly work with two types of shaders:
  - `Spatial`: Used for 3D shaders.
  - `CanvasItem`: Used for 2D shaders (not covered in this guide).
  
- **Main Functions**:
  - `vertex()`: Handles the vertices of your mesh.
  - `fragment()`: Deals with the pixels on your material.

### Creating a Shader Material

1. Start with the shader declaration: `shader_type spatial;`.
2. Write your shader code within the `vertex()` or `fragment()` functions, depending on whether you want to manipulate vertices or pixels.

### Example: Changing Color

To change the color of a mesh, you would use the `fragment()` function:

```glsl
shader_type spatial;

void fragment() {
    ALBEDO = vec3(0.0, 0.0, 1.0); // This sets the color to blue
}
```

Remember to end each line with a semicolon, as the shader language is statically typed.

### Modifying Properties

You can also modify other properties like `ROUGHNESS` and add effects such as rim lighting within your shader.

### Using UVs for Conditional Coloring

You can divide your mesh's appearance using UV mapping and conditional statements:

```glsl
void fragment() {
    if (UV.x > 0.5) {
        ALBEDO = vec3(0.0, 0.0, 1.0); // Blue for one half
    } else {
        ALBEDO = vec3(1.0, 0.5, 0.0); // Orange for the other half
    }
}
```

### Manipulating Vertices

The `vertex()` function allows you to move or transform the mesh's vertices. For example, to move the mesh along the Y-axis based on a sine wave:

```glsl
void vertex() {
    VERTEX.y += sin(TIME) * 0.5;
}
```

## Advanced Effects

You can create more complex effects using math functions and time-based alterations. For example, to create a color-shifting effect:

```glsl
void fragment() {
    float r = sin(TIME + VERTEX.x) * 0.5 + 0.5;
    float g = sin(TIME + VERTEX.y) * 0.5 + 0.5;
    float b = sin(TIME + VERTEX.z) * 0.5 + 0.5;
    ALBEDO = vec3(r, g, b);
}
```



# Godot Engine FPS Project

Welcome to the Godot Engine First-Person Shooter (FPS) Project. This project is designed to guide you through building a modern FPS from scratch using Godot Engine. Each episode of this series will cover different aspects of FPS development, from basic movement and collision handling to advanced gameplay features found in games like Halo or Call of Duty.

## Getting Started

### Prerequisites
- Godot Engine 4.0 (alpha, beta, or stable)
- Blender 3.0 or above for custom meshes (optional)

### Test Level Setup
1. Create a scene with a solid floor mesh and several collidable object meshes.
2. Add a `WorldEnvironment` node and configure the environment and sky using an HDRi sky image (included in the repo or use your own).
3. Ensure there's at least one mesh for collision testing.

### Player Controller
1. Create a new scene with `CharacterBody3D` as the parent node.
2. Add a `CollisionShape3D` node with a capsule shape (height: 2 meters, radius: 0.5 meters).
3. Add a `MeshInstance3D` node as a placeholder for the player model.
4. Save the scene and add it to the test level.

### Camera Setup
1. Add a `Camera3D` node to the player controller.
2. Adjust the camera's height to match eye level (approximately 1.5 meters).
3. Run the scene to ensure the camera displays the environment correctly.

### Movement Script
1. Create a new GDScript for the player controller.
2. Implement basic keyboard movement using WASD keys and spacebar for jumping.
3. Add input actions for movement in `Project > Project Settings > Input Map`.
4. Update the script to reference the newly created input actions.

### Mouse Look
1. Set the mouse mode to `MOUSE_MODE_CAPTURED` to hide and center the cursor.
2. Capture mouse movement and apply it to the camera's rotation.
3. Adjust mouse sensitivity for comfortable control.
4. Split mouse input into horizontal (player rotation) and vertical (camera rotation) components.

### Quality of Life Additions
1. Add an exit function to close the game window using the Escape key.
2. Include a `ReflectionProbe` node for enhanced material reflections.

### Final Touches
1. Ensure static meshes in the scene have `StaticBody3D` and `CollisionShape3D` nodes for proper collision.
2. Tweak player speed, jump velocity, and other parameters as needed.

## Contributions and Support

This project is open-source and aims to be a comprehensive resource for Godot FPS development. Contributions are welcome, and if you're interested in supporting this project further, consider becoming a GitHub sponsor. Sponsors receive access to source projects, early video tutorials, and can vote on future FPS mechanics to cover.

Thank you for your interest in this project. Stay tuned for more updates, and keep creating!



# Blender to Godot Integration Guide

This guide explains how to seamlessly integrate Blender models into your Godot 4.0 projects. It ensures compatibility with Blender version 3.0 and above.

## Getting Started

Ensure you have Godot 4.0 and Blender 3.0 (or newer versions) installed on your system.

### Configuring Godot

1. **Enable Advanced Settings:**
   - Open Godot and navigate to `Project > Project Settings`.
   - Make sure "Advanced Settings" is enabled to access the Blender integration options.

2. **Enable Blender File Import:**
   - In the Project Settings, go to `File System > Import > Blender`.
   - Enable the setting to import Blender files by checking the box.

3. **Set Blender Executable Path:**
   - Go to `Editor > Editor Settings` and search for Blender in the file system import settings.
   - Ensure the path to the Blender executable is correctly set. This might be auto-detected, but you can manually set it if necessary.

### Working with Blender

1. **Create and Save Blender Model:**
   - Open Blender and create your model.
   - Save the `.blend` file directly into your Godot project's directory. Godot will automatically import the file.

2. **Edit and Update Models:**
   - Any changes made in Blender, such as modifying the model or applying different modifiers, will reflect in Godot once the Blender file is saved.

3. **Exclude Objects from Import:**
   - To prevent specific objects (like cameras or lights) from being imported into Godot, append `-noimp` to their names in Blender.

4. **Importing Collisions and Materials:**
   - Objects named with the `-col` suffix in Blender will be imported as collision objects in Godot.
   - Materials created in Blender, including those with textures, will also be imported, retaining their properties.

### Handling Textures

To ensure textures are correctly imported:

1. **Pack and Unpack Resources in Blender:**
   - In Blender, go to `File > External Data > Pack Resources` to pack the resources.
   - Unpack the resources into the desired directory within your Godot project using the `Unpack Item` feature in Blender. This step is crucial for the correct importation of textures into Godot.

2. **Re-import in Godot:**
   - After unpacking the resources in Blender, return to your Godot project. Godot will import the packed textures and update the materials accordingly.

## Conclusion

This workflow facilitates the use of Blender models in Godot projects, allowing for dynamic updates and easy material management. Follow these steps to enhance your game development process with powerful 3D models and animations from Blender.

Thank you for following this guide, and happy creating!


# Getting Started with XR Development in Godot

This guide walks you through the process of setting up a basic XR project in Godot, starting from downloading the Godot Engine, setting up your project, and preparing it for XR development.

## Prerequisites

- Basic knowledge of Godot Engine and XR development concepts.
- A compatible XR device for testing (e.g., Oculus Quest, Pico).

## 1. Download Godot

- Visit the [Godot Engine official website](https://godotengine.org/download) and download the latest stable version of Godot suitable for your operating system. If you're interested in using Godot 4.2 features, check the blog page for the latest release candidates.

## 2. Install Godot

- Open the downloaded file to install Godot on your system. Follow the on-screen instructions to complete the installation.

## 3. Create a New Project

- Launch Godot and click on **New Project**.
- Assign a name to your project and choose or create a new directory for it.
- Click **Create & Edit** to initialize your new project.

## 4. Select Compatibility Renderer

- For XR projects, especially targeting standalone devices, it's recommended to use the **Compatibility Renderer**.
- This option is available during the project setup phase.

## 5. Configure Project Settings

- Navigate to **Project > Project Settings**.
- In the XR section, select **Open XR** and enable it.
- Then, go to the Shader section and enable the necessary shaders for your project.

## 6. Set Up Your Scene

- Create a new 3D scene and rename the root node (e.g., `Main`).
- Add XR-specific nodes like `XR Origin 3D` and `XR Camera 3D`. Position them as needed for visual reference.
- Add `XR Controller 3D` nodes for both hands, assign trackers, and optionally add a MeshInstance 3D with a simple geometry (e.g., a box) to represent the hands.

## 7. Add Environment and Lighting

- Since the editor's environment and lighting are only for visual reference, manually add your desired environment and lighting to the scene.
- Utilize the editor's shortcuts to add default environmental elements and adjust them as necessary.

## 8. Adjust for Godot 4.2 (If Using)

- If you're using Godot 4.2, make sure to account for new features and necessary adjustments related to Open XR and rendering options.

## 9. Write and Add Necessary Code

- Refer to the [Godot documentation](https://docs.godotengine.org/en/stable/index.html) for the required code snippets to make your XR setup functional.
- Attach a new script to your main node and paste the appropriate code from the documentation.

## 10. Testing Your Project

- Test your project by pressing the play button. Ensure it behaves as expected in PCVR mode.

## 11. Exporting for Standalone Headsets

- For exporting to standalone headsets like the Quest or Pico, additional setup steps are required, including configuring Android build tools and creating a debug keystore.
- Follow the detailed instructions in the Godot documentation for exporting to Android.

## 12. Final Testing on Device

- Use the remote debug option to run your project on your XR device and conduct final testing.

## Resources

- [Godot Engine Documentation](https://docs.godotengine.org/en/stable/index.html)
- [XR Development in Godot](https://docs.godotengine.org/en/stable/tutorials/vr/index.html)

## Support

If you encounter any issues or have questions, consider joining the [Godot Community](https://godotengine.org/community) or visiting the [official Godot Discord channel](https://godotengine.org/community).

## Contributing

Feel free to fork this project and submit pull requests with improvements or updates to the guide.

## License

This guide is provided under the MIT License. See the LICENSE file for more information.

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
