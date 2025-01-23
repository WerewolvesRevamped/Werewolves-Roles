# Formalization Guide

- [Introduction](#introduction)
- [Game Elements](#game-elements)
  - [Roles Format](#roles-format)
  - [Teams Format](#teams-format)
  - [Groups Format](#groups-format)
  - [Abilities Format](#abilities-format)

## Introduction
Werewolves Revamped is automated using "formalization" - i.e. all roles and similiar are written in a formal way in a custom language (WWRF) which can be interpreted by the bot.

WWRF is designed with a "Trigger > Condition > Action" model: there are certains events that emit a "trigger" (a phase starting, a player being killed, etc) and their game "actions" which change the game state and my emit triggers themselves (e.g. killing a player, creating a group, manipulating voting values). All elements in WWR are designed by combining triggers and actions (and optionally including conditions that limit the execution of the action to certain cases). Actions are differentiated between prompting actions, which prompt the player for an input and are only executed once such an input is provided and automatic actions which are executed without player interaction (and potentially even without the players knowledge)

For example, the Fortune Teller has a prompting investigation actions which is triggered by the night starting. The Wolf Cub has an automatic poll creating action which is triggered by their death.

## Game Elements

Game Elements are all the components that make up the game. This includes players, roles, groups, polls, teams and more.

We differentiate between active and passive game elements. Active elements can be modified, while passive elements remain static. Passive elements are what is defined in this repository, and active elements are instances of a passive element created during the game. Outside a game only passive elements exist. Some game elements exist in both passive and active form.

For example, the concept of a lynch poll, as defined in this repository is a passive game element, whereas one specific lynch poll within a game is an active game element.
Passive game elements store general information about the element (e.g. a role's description), whereas active elements store game data (e.g. a player's date).

Furthermore we differentiate between acting active elements and non-acting active elements. Acting elements are those that are able to execute (i.e. create) abilities.

For example, a player can create an ability by using their role's abilities. A display, on the other hand, cannot create or execute abilities.

The following types of game elements exist:

Name | Passive  | Active | Acting | Notes
--- | --- | --- | --- | --- 
Roles | ✅ | ⛔ | ⛔ | ∗ 
Players | ⛔ | ✅ | ✅ |  
Teams | ✅ | ✅ | ✅ | ∗∗ 
Groups | ✅ | ✅ | ✅ |  
Polls | ✅ | ✅ | ✅ |  
Attributes (Default) | ⛔ | ✅ | ⛔ |  
Attributes (Custom) | ✅ | ✅ | ✅ |  
Attributes (Role) | ⛔ | ✅ | ✅ | 
Abilities | ⛔ | ✅ | ⛔ |  
Displays | ✅ | ✅ | ⛔ |  
Locations | ✅ | ⛔ | ⛔ |  
Choices | ⛔ | ✅ | ⛔ |  

∗ Roles can be instantiated in two ways: when they are assigned to a player, the player sort of becomes the instantiated version of the role, though of course there is more data on the player. Alternatively if a role is assigned as an extra role mid-game it is instantiated as a role type attribute.
∗∗ While Teams are both active and passive, they are not instantiated as there can only ever be one of each team. Instead the team's active and passive data is stored in the same element.

The various game elements are described in more detail below.

### Roles Format

Roles are the main holder of WWRF information. Their formalization is formatted as follows:

```
**<Role Name>** | <Role Group> <Role Category> <Role Team>
__Basics__
<Basic Description>
__Details__
<Detailed Description>

__Simplified__
<Simplified Description>

__Formalized__
[Unique Role]
[Haunted Role]
<Ability List>

__Card__
<Card Description>
```
- Role Name: May be anything  
- Role Class: May be `Townsfolk`, `Werewolf`, `Solo`, `Unaligned` or `Extra`  
- Role Category: May be `Elected`, `Align`, `Killing`, `Group`, `Investigative`, `Power` or `Miscellaneous`  
- Role Team: Should be kept blank for any role group besides `Solo`. For `Solo` should be set to `- <Team Name>`, where `<Team Name>` is the name of a defined team, e.g. `Pyro Team`.
- Unique Role: Set to `Unique Role` for unique roles, otherwise remove the line entirely
- Haunted Role: Set `Haunted Role` for roles that use ALL their ability while ghostly, otherwise remove the line entirely
- Ability List: A newline separated list of `Abilities`

### Teams Format
```
**<Team Name>**
__Basics__
<Basic Description>

__Formalized__
Win Condition: <Target List>
<Ability List>
```

- Win Condition: A comma separated list of [Player Selectors](#player-selectors). The team wins when all remaining players match one or more of the selectors.
- Ability List: A newline separated list of `Abilities`

### Groups Format
```
**<Group Name>** | <Related Team Name> Group
__Basics__
<Basic Description>
__Members__
<Members Description>

__Formalized__
[Unique Group]
<Ability List>
```

- Unique Group: Set to `Unique Group` for unique group, otherwise remove the line entirely 
- Ability List: A newline separated list of `Abilities`

### Abilities Format
(WIP)
when specifying an ability list, you may use `No Abilities` for an empty ability list
