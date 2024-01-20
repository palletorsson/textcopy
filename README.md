1. **Aktivera Skriptfönstret**: 
   Öppna ett skriptfönster i Blender genom att justera layouten.

2. **Skapa Nytt Skript**: 
   I skriptfönstret, välj att skapa ett nytt dokument.

3. **Spara Filen**: 
   Spara ditt skript med ett lämpligt namn.

4. **Importera Blender Python API**:
   ```python
   import bpy


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
