Dialogues — Signatures des classes et interaction

But : Décrire la structure des classes frontales liées aux dialogues dans le jeu, exposer les signatures publiques/attendues et expliquer leurs interactions sans fournir de code décompilé.

1. `DialoguePresenter` (responsable de l'enfilement et présentation)
- Rôle : gérer la file de dialogues, instancier la vue UI, coordonner le démarrage et la fin d'un dialogue.
- Signatures principales (exemples de surface) :
  - `public static void EnqueueDialogue(string factionKey, string personKey, string dialogueKey)`
  - `public static void StartDialogue(string factionKey, string personKey, string dialogueKey)`
  - `public static void NotifyDialogue(string factionKey, string personKey, string dialogueKey)`
  - `public event Action<string, string, string> OnDialogueStarted` (faction, person, dialogue)
  - `public event Action<string, string, string> OnDialogueFinished`
  - `public void CancelCurrentDialogue()`
- Interaction : reçoit les appels de haut niveau (ex. `Universe`, système de commandes) ; utilise `DialogueRunner` pour exécuter le contenu et `DialogueView` pour afficher.

2. `DialogueManager` (catalogue & utilitaires)
- Rôle : inventaire des dialogues disponibles, chargement et validation des ressources (textes, assets audio, métadonnées).
- Signatures principales :
  - `public static bool ContainsDialogue(string dialogueKey)`
  - `public static DialogueAsset LoadDialogue(string dialogueKey)`
  - `public static IEnumerable<string> ListAllDialogues()`
  - `public static DialogueAsset CreateTemporaryDialogue(DialogueDescriptor descriptor)`
- Interaction : utilisé par wrappers/présenters pour vérifier et charger les ressources avant exécution.

3. `DialogueRunner` (moteur d'exécution)
- Rôle : interpréter le contenu d'un dialogue (nœuds, branches conditionnelles, options) et produire les événements d'avancement.
- Signatures principales :
  - `public DialogueRunner(DialogueAsset dialogue)` (constructeur)
  - `public void Run()`
  - `public void Stop()`
  - `public event Action<DialogueLine> OnLineReady` (quand une ligne est prête à l'affichage)
  - `public event Action<DialogueOption[]> OnOptionsPresented`
  - `public void ChooseOption(int optionIndex)`
- Interaction : piloté par `DialoguePresenter` ; notifie `DialogueView` pour l'affichage et renvoie des résultats au système de missions si nécessaires.

4. `DialogueView` / `DialogueOptionItem` / `DialogueWaveformView` (UI)
- Rôle : affichage des lignes, options et éléments audio/visuels.
- Signatures principales :
  - `public void ShowLine(DialogueLine line)`
  - `public void ShowOptions(DialogueOption[] options)`
  - `public event Action<int> OnOptionSelected` (index)
  - `public void PlayVoice(string audioKey)`
  - `public void Close()`
- Interaction : abonné aux événements `DialogueRunner.OnLineReady` et `OnOptionsPresented`; envoie les sélections de l'utilisateur vers `DialogueRunner`.

5. `InteractionActionHandler` / Commands (système de commandes inter-systèmes)
- Rôle : fournir des commandes appelables depuis YAML/mission rules (ex. `NotifyMandatoryDialogue`, `StartDialogue`, `SetBlackboardBool`).
- Signatures / contrats attendus :
  - `public static void NotifyMandatoryDialogue(string factionKey, string personKey, string dialogueKey, float daysDelay = 0f)`
  - `public static void StartDialogueCommand(string factionKey, string personKey, string dialogueKey)`
  - Les handlers doivent accepter une liste d'arguments `object[]` et valider/convertir les types.
- Interaction : invoquées à partir des règles YAML ; elles se traduisent en appels vers `DialogueManager`/`DialoguePresenter`.

6. `Universe` / Point d'entrée global
- Rôle : point d'accès global aux systèmes du jeu ; exposer helpers utilitaires (ex. `StartDialogue`) que d'autres systèmes appellent.
- Signatures possibles :
  - `public static void StartDialogue(string factionKey, string personKey, string dialogueKey)`
  - `public static IEnumerable<string> GetAvailableDialogues()`
- Interaction : souvent la première couche appelée par wrappers, systèmes de règles, mods. Peut router vers `InteractionActionHandler` ou `DialoguePresenter`.

7. Objets de données (types de surface)
- `DialogueAsset` (représente l'asset de dialogue)
  - Propriétés attendues : `string Id`, `string Title`, `DialogueNode[] Nodes`, `LocalizationData Localization`
- `DialogueLine`
  - `string SpeakerKey`, `string TextKey`, `string AudioKey`, `string PortraitKey`
- `DialogueOption`
  - `string TextKey`, `string OutcomeKey`, `int NextNodeId`
- `DialogueDescriptor` (utilisé pour création en mémoire / tests)
  - `string Id`, `string Title`, `DialogueLine[] Lines`

Flux d'interaction résumé
1. Une règle YAML déclenche une commande (`NotifyMandatoryDialogue` / `StartDialogue`).
2. Le `InteractionActionHandler`/command router valide les arguments et appelle `Universe.StartDialogue` ou directement `DialoguePresenter.StartDialogue`.
3. `DialoguePresenter` vérifie l'existence via `DialogueManager.ContainsDialogue` puis charge l'asset (`LoadDialogue`).
4. `DialoguePresenter` crée un `DialogueRunner` et démarre `Run()`.
5. `DialogueRunner` publie `OnLineReady` / `OnOptionsPresented` → `DialogueView` affiche, l'utilisateur répond → `DialogueRunner.ChooseOption(...)`.
6. À la fin, `DialogueRunner` déclenche `OnComplete` → `DialoguePresenter` émet `OnDialogueFinished` et le moteur de missions peut exécuter actions ultérieures.

Points d'extension SDK
- Exposer wrappers simples : `Dialogues.Start`, `Dialogues.Notify`, `Dialogues.HasDialogue`, `Dialogues.OnStart/OnFinish`.
- Fournir helpers pour construire `DialogueDescriptor` en code et exporter en YAML/TextAsset via pipeline d'outils si nécessaire.

Emplacement dans le repo
- Ce document a été ajouté à : `Organization-Wiki/technical/Dialogues-Class-Signatures.md` (guide non-décompilé pour les contributeurs et modders).

Fin de la fiche — si tu veux je peux maintenant générer un petit exemple d'utilisation SDK (plugin BepInEx) qui appelle ces méthodes via le wrapper SDK existant.