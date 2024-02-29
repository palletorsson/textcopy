
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
