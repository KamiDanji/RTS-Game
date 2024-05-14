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

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption><p>Layers</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Prefab Maken</p></figcaption></figure>

#### GameManager Layer

Sellecteer de GameManager en Onder Object Collection -> Include layers -> alleen de layer sellecteren die we net hebben gemaakt (Walkable Terrain)



#### Walkable Terrain Layout

Maak nu een simpele layout door de cube te sellecteren CTRL + D om dezelfde object op de locatie te plakken en dan CTRL ingedrukt tijdens het slepen met het pijltje om zo een simpele layout te maken.

Nadat je de layout heb gemaakt druk je op GameManager en Dan op Bake.

<figure><img src=".gitbook/assets/image (2) (1).png" alt=""><figcaption><p>NavMesh Setup</p></figcaption></figure>



#### Capsule(Enemy)

Plaats de Capsule(Enemy) Op het blauwe vlakje wat is onstaan na het baken. Maak de Capsule ook wat kleiner door in de transform -> Scale 0.5, 0.5, 0.5

Ga naar de inspecotr en voeg de NavMeshAgent toen aan de Capsule. Dit heb je nodig zodat de capsule kan navigeren op het terrain

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption><p>NavMeshAgent</p></figcaption></figure>



#### Waypoint

Druk op de 1ste plek waar er een bocht is en dan maak je een Empty, Geef die de naam Waypoint

<figure><img src=".gitbook/assets/image (3) (1).png" alt=""><figcaption><p>Waypoint</p></figcaption></figure>



#### Scripts

Maak in de assets tab een nieuwe folder met de naam Scripts

Maak in de Scripts folder een nieuwe C# Script en geef het de naam "EnemyController"

Sleep de script op je Capsule in de hierarchy

<figure><img src=".gitbook/assets/image (4) (1).png" alt=""><figcaption><p>Scripts folder</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (6) (1).png" alt=""><figcaption><p>Script op de enemy zetten</p></figcaption></figure>



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

<figure><img src=".gitbook/assets/image (8) (1).png" alt=""><figcaption><p>Waypoints</p></figcaption></figure>



### Enemy Wave Spawner

Hier gaan we het Enemy Wave systeem maken om enemy's te spawnen in waves.



#### Nieuwe Cube en Spawner

Spawn een nieuwe Cube en zet die aan het begin van je Path(Waar de enemys gaan lopen).

Maak de Cube ook groter door een scale van 3, 3, 3 te geven.

Maak daarna een Empty en geef die de naam Spawner. Zet de Spawner op het begin van het pad op de NavMesh Surface( Blauwe strook op je path).

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption><p>Spawner setup</p></figcaption></figure>



#### Spawner Script

Maak een nieuwe C# Script in de Script folder en geef die de naam "Spawner".

Sleep de Script daarna op de Spawner GameObject.

Als je in de Inspector kijkt van de Spawner GameObject zou de script er moeten staan

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption><p>Spawner Script Op Spawner GameObject</p></figcaption></figure>

Open de Spawner script en plak dit erin

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Spawner : MonoBehaviour
{

    public GameObject enemyPrefab;

    public List<Transform> WayPoints;

    // Start is called before the first frame update
    void Start()
    {
        InvokeRepeating("Spawn", 1f, 0.5f);
    }

    void Spawn()
    {
        GameObject enemy = Instantiate(enemyPrefab, transform.position, Quaternion.identity);
        enemy.GetComponent<EnemyController>().SetDestination(WayPoints);
    }
}

```



Open Dan de EnemyController script en verander het naar de nieuwe script hieronder.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;

public class EnemyController : MonoBehaviour
{

    private List<Transform> WayPoints;
    private int currentWayPointIndex = 0;
    private float agentStoppingDistance = 0.3f;
    private bool WayPointsSet = false;

    NavMeshAgent agent;

    // Start is called before the first frame update
    void Start()
    {
        agent = GetComponent<NavMeshAgent>();
    }

    // Update is called once per frame
    void Update()
    {
        if (!WayPointsSet)
        {
            return;
        }

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


public void SetDestination(List<Transform> wayPoints)
    {
        this.WayPoints = wayPoints;
        WayPointsSet = true;
    }
}

```

#### Capsule/Enemy Prefab

Sleep de Capsule vanuit de Hieracrhy naar de assets tab om de capsule een prefab te maken dat hebben we nodig voor de spawner.

verwijder daarna de capsule uit de hierarchy.

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption><p>Capsule/Enemy Prefab maken</p></figcaption></figure>



Klik op de spawner en druk op het slotje dat bij de inspector staat.

Sleep daarna de Capsule Prefab van de Assets window naar 'Enemy Prefab'.

Sellecteer daarna je waypoints door CTRL ingedrukt te houden en je waypoints te klikken en sleep ze dan naar het woord 'Way Points' in de inspector.

druk nadat weer op het slotje bij de inspector om het paneel weer te unlocken.

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption><p>Spawner setup</p></figcaption></figure>



#### Play test

Als je alle stappen tot nu toe goed hebt uitgevoerd en je drukt op play in de editor zou: Enemy's spawnen -> gaan naar de waypoint -> Bij de laatste Waypoint gaan de enemys kapot.

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption><p>project in werking</p></figcaption></figure>



### Level Manager

Open de Spawner script en verander het naar dit

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Spawner : MonoBehaviour
{

    public GameObject enemyPrefab;
    public float spawnRate = 0.5f;
    public int maxCount = 10;
    private int count = 0;
    public List<Transform> WayPoints;


    // ReSharper disable Unity.PerformanceAnalysis
    void Spawn()
    {
        GameObject enemy = Instantiate(enemyPrefab, transform.position, Quaternion.identity);
        enemy.GetComponent<EnemyController>().SetDestination(WayPoints);

        count++;

        if (count >= maxCount)
        {
            CancelInvoke("Spawn");
        }
    }

    public void StartNextWave()
    {
        count = 0;
        InvokeRepeating("Spawn", 1, spawnRate);
    }

    public void StopSpawning()
    {
        CancelInvoke("Spawn");
    }
}

```



Spawn een Empty object in en geef die de naam "LevelManager"

maak een nieuwe script en geef die ook de naam "LevelManager"

sleep de script naar de empty gameobject.

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption><p>LevelManager</p></figcaption></figure>

Open de LevelManager Script en plak dit erin

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class LevelManager : MonoBehaviour
{

    public int maxWave = 5;
    private int currentWave = 0;
    private bool isSpawning = false;
    private int enemiesRemaining = 0;
    private float timer = 0f;
    public float waveSpawnInterval = 45f;

    public Spawner spawner;

    // Start is called before the first frame update
    void Start()
    {
        StartNextWave();
    }

    // Update is called once per frame
    void Update()
    {
        if (isSpawning)
        {
            // While the wave is spawning, keep the timer to 0
            timer = 0f;
        }
        else
        {
            //If we are not spawning, start the timer
            timer -= Time.deltaTime;
            if (timer <= 0)
            {
                if (currentWave >= maxWave)
                {
                    // reached the max wave, stop spawning
                    StopSpawning();
                }
                else
                {
                    StartNextWave();
                }
            }
        }
    }

    public void EnemyDestroyed()
    {
        enemiesRemaining--;
        if (enemiesRemaining == 0)
        {
            isSpawning = false;
            // All enemies have been destroyed, reset timer to waveSpawnInterval
            timer = waveSpawnInterval;
        }
    }

    void StartNextWave()
    {
        currentWave++;
        spawner.StartNextWave();
        enemiesRemaining = spawner.maxCount;
        isSpawning = true;
    }

    void StopSpawning()
    {
        spawner.StopSpawning();
        isSpawning = false;
    }
}

```

####

#### EnemyController

Open de EnemyController Script en verander dat naar dit

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;

public class EnemyController : MonoBehaviour
{

    public LevelManager levelManager; // Reference to the level manager
    private List<Transform> WayPoints;
    private int currentWayPointIndex = 0;
    private float agentStoppingDistance = 0.3f;
    private bool WayPointsSet = false;

    NavMeshAgent agent;

    // Start is called before the first frame update
    void Start()
    {
        agent = GetComponent<NavMeshAgent>();
        levelManager = FindObjectOfType<LevelManager>();
    }

    // Update is called once per frame
    void Update()
    {
        if (!WayPointsSet)
        {
            return;
        }

        if (!agent.pathPending && agent.remainingDistance <= agentStoppingDistance)
        {
            if (currentWayPointIndex == WayPoints.Count - 1)
            {
                levelManager.EnemyDestroyed();
                Destroy(this.gameObject, 0.1f);
            }
            else
            {
                currentWayPointIndex++;
                agent.SetDestination(WayPoints[currentWayPointIndex].position);
            }
        }
    }


public void SetDestination(List<Transform> wayPoints)
    {
        this.WayPoints = wayPoints;
        WayPointsSet = true;
    }
}

```



#### Spawner naar LevelManager

Sleep de GameObject Spawner naar het tabje Spawner in de LevelManager Script op de LevelManager GameObject

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption><p>LevelManager</p></figcaption></figure>

LevelManager Script:&#x20;

Max Wave: Hoevel golven van vijander er zijn.

Wave Spawn Interval: Hoelang het duurt voordat de volgende golf start.



### Turrets



#### Tags

Druk op de Cube -> in de inspector op Tags -> Add Tag. -> druk op het '+' om een tag te maken. Noem de tag "Platform". Maak er nog een en noem die "Occupied"

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption><p>Tags</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption><p>Tags</p></figcaption></figure>



#### Camera Movement

maak een C# en geef die de naam CameraController en plak dit erin.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraController : MonoBehaviour
{
    public float speed = 10.0f; // snelheid van de camera
    public float sensitivity = 0.1f; // gevoeligheid van de muis
    private float pitch = 0f; // verticale rotatie van de camera

    void Start()
    {
        // Vergrendel en verberg de cursor wanneer het spel start
        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
    }

    void Update()
    {
        // Wissel cursor zichtbaarheid en vergrendelingsstatus wanneer de linker Alt-toets wordt ingedrukt
        if (Input.GetKeyDown(KeyCode.LeftAlt))
        {
            if (Cursor.lockState == CursorLockMode.Locked)
            {
                Cursor.lockState = CursorLockMode.None;
                Cursor.visible = true;
            }
            else
            {
                Cursor.lockState = CursorLockMode.Locked;
                Cursor.visible = false;
            }
        }

        // Als de cursor is vergrendeld, sta camerabeweging toe
        if (Cursor.lockState == CursorLockMode.Locked)
        {
            // Haal de nieuwe positie van de muis op ten opzichte van de vorige positie
            Vector3 delta = new Vector3(Input.GetAxis("Mouse X"), Input.GetAxis("Mouse Y"), 0);

            // Roteer de camera op basis van de muisbeweging
            pitch -= delta.y * sensitivity;
            pitch = Mathf.Clamp(pitch, -90f, 90f);

            Quaternion targetRotation = Quaternion.Euler(pitch, transform.localEulerAngles.y + delta.x * sensitivity, 0f);
            transform.localRotation = targetRotation;

            Vector3 dir = new Vector3();
            if (Input.GetKey(KeyCode.W)) dir += transform.forward; // Vooruit bewegen
            if (Input.GetKey(KeyCode.S)) dir -= transform.forward; // Achteruit bewegen
            if (Input.GetKey(KeyCode.A)) dir -= transform.right; // Naar links bewegen
            if (Input.GetKey(KeyCode.D)) dir += transform.right; // Naar rechts bewegen

            // Controleer of de spatiebalk wordt ingedrukt
            if (Input.GetKey(KeyCode.Space))
            {
                dir += Vector3.up; // Omhoog bewegen
            }

            // Controleer of de Ctrl-toets wordt ingedrukt
            if (Input.GetKey(KeyCode.LeftControl))
            {
                dir -= Vector3.up; // Omlaag bewegen
            }

            dir.Normalize(); // Normaliseer de richtingsvector
            dir *= speed * Time.deltaTime; // Schaal de richtingsvector met snelheid en tijd

            // Beweeg de camera
            transform.Translate(dir, Space.World);
        }
    }
}
```

[^1]: #### Unity Scenes Uitgelegd

    Unity Scenes zijn  individuele levels of stages in een game, elk met hun eigen objecten en instellingen. Ze worden gebruikt om verschillende delen van een game, zoals levels of de mainmenu, te organiseren. Hiermee kun je efficiënt tussen verschillende onderdelen van je game wisselen en het ontwikkelproces overzichtelijk houden.
