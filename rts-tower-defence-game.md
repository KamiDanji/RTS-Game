---
description: Alle instructies om een RTS Tower Defence game te maken.
---

# RTS Tower Defence Game

{% file src=".gitbook/assets/OntwerpDocument.docx" %}
Ontwerp document van de game
{% endfile %}

{% hint style="info" %}
Gebruik de screenshots voor meer duidelijkheid&#x20;
{% endhint %}

### Project creatie

Open Unity en druk op new project rechts-boven, selecteer de 3D Built in render pipeline en geef je projetc een naam (B.v. : TowerDefense)

### Beginnen aan de game!



#### Scene

Links onderen zie je de [Scene ](#user-content-fn-1)[^1]SampleScene, verander de naam naar Level1.

<figure><img src=".gitbook/assets/image_2024-05-06_235622611.png" alt=""><figcaption><p>Naam veranderen naar Level1</p></figcaption></figure>



#### Packages, AI Navmesh, Gridbox Materials

Druk daarna van boven op Window -> Package Manager, Selecteer Unity Registry packages -> Typ AI Nav -> Selecteer en installeer de Ai navigation Package

Ga naar [deze link](https://assetstore.unity.com/packages/2d/textures-materials/gridbox-prototype-materials-129127) om de gridbox materials te downloaden voor unity, dit gaan we gebruiken om de objecten textures te geven zodat het niet allemaal wit is. Druk op openen in Unity en dan in Unity op download -> Import -> Import

Save je project en sluit het af start het daarna weer op. Dit is om de project te updaten met de nieuwe import van de AI navmesh package.

<figure><img src=".gitbook/assets/image_2024-05-07_000708325.png" alt=""><figcaption><p>AI NavMesh Installeren</p></figcaption></figure>



#### Level Prototyping

Spawn nu de 3D object Plane in de level door in je Hierarchy Rechtermuis te klikken -> 3D object -> Plane. Spawn ook een Cube en een Capsule in

Na het in spawnen van de objecten moet je de transform resetten. Dat doe je door het object te selecteren/klikken -> 3 stippen bij transform in de inspector -> Reset

Door op reset te drukken gaan de objecten naar de world origin(0/midden punt)

<figure><img src=".gitbook/assets/NVIDIA_Overlay_q14cV3u2gn.png" alt=""><figcaption><p>Objecten in spawnen</p></figcaption></figure>

<figure><img src=".gitbook/assets/NVIDIA_Overlay_GlT2vNXIqe.png" alt=""><figcaption><p>Transform Resetten</p></figcaption></figure>



#### Materials

in dezelfde foto zie je de locatie van de materialen. Om die op je objecten te gebruiken moet je de gewenste material slepen naar het gewenste object.



#### Object Scaling

Maak de Plane groter door bij transform -> Scale en zet alle drie de axis(X,Y,Z) op 5

Voor de Cube doen we Scale 2, 1, 2.

Plaats de ingespawnde objecten in de level, ongeveer zoals in foto.

Maak daarna een empty object door weer rechter muis klik en dan 'Create Empty' boven 3d object, Noem de Empty "GameManager"

<figure><img src=".gitbook/assets/NVIDIA_Overlay_Y5uwukQA77.png" alt=""><figcaption><p>Layout en GameManager</p></figcaption></figure>



#### GameManager

Sellecteer GameManager dan in de inspector add component -> typ NavMeshSurface -> Klik erop

<figure><img src=".gitbook/assets/NVIDIA_Overlay_MhOsTQGFuf.png" alt=""><figcaption><p>NavMeshSurface</p></figcaption></figure>



#### Layer

Druk op de cube -> in de inspector Layer -> Add Layer -> Layer 6 -> Typ Walkable Terrain

Druk nog een keer op de cube en dan in de inspector op layers en selecteer de layer die je net hebt toegevoegd.

Nadat je dit heb gedaan sleep je de cube van je hieracrcy naar je assets

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption><p>Layers</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption><p>Prefab Maken</p></figcaption></figure>

#### GameManager Layer

Sellecteer de GameManager en Onder Object Collection -> Include layers -> alleen de layer sellecteren die we net hebben gemaakt (Walkable Terrain)



#### Walkable Terrain Layout

Maak nu een simpele layout door de cube te sellecteren CTRL + D om dezelfde object op de locatie te plakken en dan CTRL ingedrukt tijdens het slepen met het pijltje om zo een simpele layout te maken.

Nadat je de layout heb gemaakt druk je op GameManager en Dan op Bake.

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption><p>NavMesh Setup</p></figcaption></figure>



#### Capsule(Enemy)

Plaats de Capsule(Enemy) Op het blauwe vlakje wat is onstaan na het baken. Maak de Capsule ook wat kleiner door in de transform -> Scale 0.5, 0.5, 0.5



#### Waypoint

Druk op de 1ste plek waar er een bocht is en dan maak je een Empty, Geef die de naam Waypoint

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption><p>Waypoint</p></figcaption></figure>



#### Scripts

Maak in de assets tab een nieuwe folder met de naam Scripts

Maak in de Scripts folder een nieuwe C# Script en geef het de naam "EnemyController"

Sleep de script op je Capsule in de hierarchy

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption><p>Scripts folder</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption><p>Script op de enemy zetten</p></figcaption></figure>



#### EnemyController Script

Open de EnemyController script en plak dit erin

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;

public class EnemyController : MonoBehaviour
{

    public List<Transform> WayPoints;
    private int currentWayPointIndex = 0;
    private float agentStoppingDistance = 0.3f;
    
    NavMeshAgent agent;

    // Start is called before the first frame update
    void Start()
    {
        if (WayPoints.Count == 0)
        {
            Debug.Log("There are no waypoints");
            return;
        }

        agent = GetComponent<NavMeshAgent>();
        agent.SetDestination(WayPoints[currentWayPointIndex].position);
    }

    // Update is called once per frame
    void Update()
    {
        if (!agent.pathPending && agent.remainingDistance <= agentStoppingDistance)
        {
            if (currentWayPointIndex == WayPoints.Count - 1)
            {
                Destroy(this.gameObject, 0.1f);
            }
            else
            {
                currentWayPointIndex++;
                agent.SetDestination(WayPoints[currentWayPointIndex].position);
            }
        }
    }
}

```

Druk op CTRL + S en ga terug naar unity

Druk op de Capsule scroll naar je script -> Druk het plusje en sleep de Waypoint of Waypoints object(en) van je hierarchy naar de waypoint in je script. Doe dit op de volgorde can je waypoints.

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption><p>Waypoints</p></figcaption></figure>



### Enemy Wave Spawner

Hier gaan we het Enemy Wave systeem maken om enemy's te spawnen in waves.

[^1]: #### Unity Scenes Uitgelegd

    Unity Scenes zijn  individuele levels of stages in een game, elk met hun eigen objecten en instellingen. Ze worden gebruikt om verschillende delen van een game, zoals levels of de mainmenu, te organiseren. Hiermee kun je efficiënt tussen verschillende onderdelen van je game wisselen en het ontwikkelproces overzichtelijk houden.
