# Forest Melody

Forest Melody is a 2D idle game built with Unity. Birds are placed on a growing tree, hatch, and generate **notes** over time. Notes fund new birds, upgrades, and branches; decorations and boosters modify progression.

This repository is an archived version of a Kaiser Studio project. The studio has closed, and this version was published with permission from its management for personal use.

**Gameplay:** [Videos and project presentation on Behance](https://www.behance.net/gallery/256527607/Birds-Idle) · [Google Play listing](https://play.google.com/store/apps/details?id=com.KaiserStudio.BirdsIdle&pcampaignid=web_share) (the game may no longer be available)

## Start here

| Area | Where to look | Responsibility |
| --- | --- | --- |
| Startup and game loop | [`WorldController.cs`](Assets/Scripts/Controllers/WorldController.cs) | Loads player state, initializes the tree and UI, updates time-based systems, and handles application pause/resume. |
| Player state and persistence | [`PlayerData.cs`](Assets/Scripts/PlayerData.cs), [`Tree.cs`](Assets/Scripts/Tree.cs) | Holds progression, currencies, owned items, placement, and offline-income calculations. |
| Game entities | [`Bird.cs`](Assets/Scripts/Bird.cs), [`Decor.cs`](Assets/Scripts/Decor.cs), [`Branch.cs`](Assets/Scripts/Branch.cs) | Models production, decoration bonuses, and tree expansion. |
| Balance and content | [`SO_Scripts/`](Assets/Scripts/SO_Scripts) | ScriptableObject definitions for birds, trees, decorations, boosters, and shop items. |
| Tree interaction | [`Controllers/`](Assets/Scripts/Controllers) | Builds the tree view and handles placement, unlocking, tapping, and drag-and-drop editing. |
| Interface | [`UI/`](Assets/Scripts/UI) | UI Toolkit panels and widgets; UXML and USS files are in the same directory. |
| Visual effects | [`Vizualization/`](Assets/Scripts/Vizualization), [`EffectVisualManager.cs`](Assets/Scripts/Managers/EffectVisualManager.cs) | Bird stages, animations, feedback, prefab lookup, and pooled effects. |
| Cross-system events | [`GameEvents.cs`](Assets/Scripts/GameEvents.cs), [`Messenger.cs`](Assets/Scripts/Utilities/Messenger.cs) | Event names and the message bus used by UI and gameplay components. |

## Runtime flow

1. `WorldController` initializes Unity services, loads `PlayerData`, prepares visual effects, and initializes the tree and UI.
2. `TreeController` reconstructs branches and placed birds/decorations from the saved model. It also creates available containers for new placements.
3. Each frame, the world updates its clock and active multipliers. Placed, hatched birds generate notes; tapping a bird can generate notes directly.
4. UI widgets read the model, present available actions, and use `GameEvents`/`Messenger` to coordinate actions such as planting, opening panels, and entering edit mode.
5. On pause, the game records activity time and saves. On resume, `Tree.CalculateOfflineIncome()` calculates earnings for the elapsed interval, subject to the configured offline limit and applicable bonuses.

Bird and branch costs, production, incubation, and progression requirements are calculated from parameters stored in ScriptableObjects rather than fixed values in UI widgets. Runtime entities retain data IDs and reconnect to their definitions after deserialization.

## Implementation details

- **Persistence:** `PlayerData` uses Odin binary serialization and writes a local save under `Application.persistentDataPath`. Save calls are made at selected progression changes and on application pause.
- **UI:** `UIManager` binds `UIDocument` roots to panel classes. The tree itself uses Unity UI components, while menus and widgets use UI Toolkit.
- **Interaction:** birds and decorations occupy typed branch containers. Edit mode uses drag-and-drop to move items between compatible containers.
- **Feedback:** bird visuals use Spine animations and DOTween transitions. Note text and tap effects use Unity object pools.
- **Integrations:** the code includes I2 Localization, Google Mobile Ads rewarded/interstitial flows, and Unity IAP for crystal packs.
