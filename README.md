
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
