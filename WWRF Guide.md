# Formalization Guide

- [Introduction](#introduction)
- [Game Elements](#game-elements)
- [Types](#types)
  - [Player Type](#player-type)
  - [Role Type](#role-type)
- [Game Element Formats](#game-element-formats)
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
Teams | ✅ | ✅ | ✅ | ⁑ 
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
⁑ While Teams are both active and passive, they are not instantiated as there can only ever be one of each team. Instead the team's active and passive data is stored in the same element.

The format of various game elements are described in more detail below.

Active game elements generally always support two values that can be modified and accessed through various methods:
- Target: A value that can store a variety of types and can be modified through targetting.
- Counter: A numeric value that can be modified through counting.

## Types

In many cases roles or other game elements need to deal with values, for example player inputs or constants used for comparisons. Each value has a certain type which must be defined or inferred.

For example, a role investigation takes one input value: a player that is to be investigated. A disguise takes two inputs: a player that should be disguised and a role they should be disguised as. These inputs may be player submissions, but may also be builtin to the role (for example for a Tanner both the role and the player are user submitted, but for a Disguised Fox the player is always set to themselves, whereas the role is user submitted).

Each value needs to be annotated with its respective type, however most of the time the WWRF parser can do this - in the investigation example the target must always be a player, so the value is always annotated as a player type. For some other abilities, this is less clear, however. In these cases the type can sometimes be inferred from the contents of the value itself (sometimes at parse time, sometimes at runtime) and other times the type has to be manually annotated. Check for each ability what types it expects and when annotation may be necessary. Annotation is done be appending the type surrounded by square brackets, for example: ``​`Citizen`[role]``.

Values are split into two categories: constant values (e.g. `Citizen`, referring to the citizen role) or selectors (e.g. `@Self`, referring to the current game element). Furthermore, there is a differentiation between basic selectors (e.g. `@Self` or `@Target`) which refer to a specifc value based on context and advanced selectors (e.g. `@(Role:Citizen)`, returing all players with the citizen role) which select values according to a specified query.

This is a list of values and example constant values and selectors:

Value Type | Constant Example  | Basic Selector Example | Advanced Selector Example | List Type
--- | --- | --- | --- | ---
Player | ⛔ ∗ | @Self | @(Role:Citizen) | ✅
Role | `Citizen` | @Target | ^(Team:Townsfolk) | ✅
Active Extra Role | `Mayor` | @ThisAttr | ⛔ | ✅
Group | #Bakers | @Self | ⛔ | ⛔
Alignment | Townsfolk | &Self | &(Align:!Townsfolk) | ✅
Location | ⁑ | ⁑ | ⁑ | ⛔
Base Location | #tavern | ⛔ | ⛔ | ⛔
Poll | `Wolfpack` | @Self | ⛔ | ⛔
Success | Success | ⛔ | ⛔ | ⛔
Result ⁂ | ⛔ | @Result | ⛔ | ⛔
Info | `Attacked @Target` ⁑⁑ | @ActionFeedback | ⛔ | ⛔
Ability Type | `Killing` | @VisitType | ⛔ | ⛔
Ability Subype ⁑⁑∗ | `Attack Killing` | @VisitSubtype | ⛔ | ⛔
Ability Category | `All` | ⛔ | ⛔ | ⛔
Number | 1 | @Selection | ⛔ | ⛔
Boolean | True | ⛔ | ⛔ | ⛔
Attribute | `Wolfish` | @VisitParameter | ⛔ | ✅
Active Attribute | `Marker` | @ThisAttr | `Marker:Self` | ✅
Display | `Potions` | ⛔ | ⛔ | ⛔
Display Value | `Counter` | ⛔ | ⛔ | ⛔
Category | `Killing` | ⛔ | ⛔ | ✅
Killing Type | `Attack` | @DeathType | ⛔ | ⛔
Class | `Townsfolk` | @Result | ⛔ | ✅
Source | `Group:Wolfpack` | @AttackSource | ⛔ | ✅
Option | `Join Pack` | @Option | ⛔ | ✅
String | `Text` | ⛔ | ⛔ | ✅
Null | ⛔ | ⛔ | ⛔ | ✅

∗ Players can not be referenced using a constant value, as that would require this player to be present in every game.  
⁑ Locations are a special type which unifies several other types into a single type. The following types can be interpreted as a location: group, base location, active extra role, player, attribute.  
⁂ Results are a special type which is returned after executing an ability, depending on context different types can be extracted from it. It always stores at least a success type (for if the ability succeeded) and an info type (for the ability feedback) and depending on the ability potentially other abilities.  
⁑⁑ Info is a special type which is a text containing several selectors. All selectors contained within the info text must either be annotated or support run-time annotation. The info type is used when a text output is generated. As part of the text output process all selectors contained within the info type are evaluated and converted to text.  
⁑⁑∗ An Ability Subtype contains the info of the ability type _and_ the subtype, not just the latter.  

Some types additionally support property accesses using the `<Selector>-><Property>`, e.g. `@Target->Role` retrieves a targets role.

Many types default to not just being a single value, but actually a list, though this will often be a list of length one, this is shown in the table above. Types that are list types will return several results if a selector matches several results (though some abilities may choose to only use the first element of the list), while types that are not list types can only return a single value. When using a property access on a list type, the property access is executed on each element in the list and a new list is returned.

### Player Type

Player type is one of the most common types and refers to a player of the game (active). This type is also known under `player_attr` (referring to a player when acting through an extra role) or `player_group` (referring to a player when acting through a group).

Selector | Meaning
--- | ---
@Self | The current player.
@All | All living players.
@Others | @All without @Self.
@Dead | All dead players.
@DeadAlive | All players.
@Target | The current player's target (must be alive).
@TargetDead | The current player's target (even if the target is dead).
@Members | The current group's or team's members.
@Attacker | A player responsible for a killing in `On Death`, `On Killed`, `On Banishment`, `On Banished` and variants.
@This | Refers to the player for which a complex trigger featuring a target (e.g. `On <Target> Death`) triggered for.
@Winner | Refers to the winner of a poll in `On Poll Closed`.
@ActionTarget | Refers to the target of an action in `On Action` and variants.
@Executor | Refers to the executor of an action in `On Poll Closed` for polls created by a group.
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
@RoleChanger | Refers to the responsible player in `On Role Change`.
@Chooser | Refers to the player that made the choice in `Choice Chosen`.
@Visitor | Refers to the visiting player in `On Visited`, `On Redirect` and variants.
@Joiner | Refers to the joining player in `On Join`.
@VisitParameter | Refers to a parameter in a visit in `On Visited`, though this will usually __NOT__ be a player.
@Ind | Refers to the current element in a `For Each` ability.
@Voters | Refers to all players that voted for the winning option in `On Poll Closed` and `On Poll Win`.
@OtherVoters | @Voters without the poll winner.
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a player if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@ID:<ID> | Refers to a specific player based on discord id. Useful for testing.
%Player[N]% | Refers to a player stored as host information.

The advanced player selector has the format @(Property:Value) and searches for players where a certain property matches a certain value. For example, `@(Role:Citizen)` will return all players who's role is `Citizen`. All properties may be inverted using an `!` at the start of the value, e.g. `@(Role:!Citizen)` will return all players who's role is __not__ `Citizen`.

Advanced Selector Value | Meaning
--- | ---
Role | The player's current role.
Cat/Category | The player's current role's category.
Class | The player's current role's class (most commonly Townsfolk/Werewolf/Solo/Unaligned/Extra).
Align/Alignment | The player's current alignment. A player can either align with a team, in which case their alignment is their team or they can be nonaligned in which case their alignment is set to `unaligned`.
FullCat | Matches both Class and Category (e.g. `@(FullCat:Townsfolk-Power)`), must be `-` separated.
OrigRole | The player's original role.
OrigCat | The player's original role's category.
OrigClass | The player's original role's class.
OrigAlign | The player's original role's default alignment (it should be noted that this may differ even without a role change necessary as `Align` checks for the players current alignment, not the player's current role's default alignment).
OrigFullCat | Matches both class and category of the player's original role.
Group | Checks for membership of a certain group.
Attr/Attribute | Checks whether the player has a certain custom attribute.
AttrSelf | Checks whether the player has a certain attribute which was applied by the current player.
AttrRole | Checks whether the player has a certain role type attribute.
AttrDisguise | Checks whether the player has a disguise applied by a certain player. As value specify a player selector which will have `@` prepended, so e.g. just `Self`.
AliveOnly | Special field. Set it to `False` to also search for dead players.
SelectAll | Special field. Set it to `False` to return a single random player from the selector instead of all players.

Players support a variety of property accesses:

Property | Meaning
--- | ---
Role | The player's current role.
Category | The player's current role's category.
OriginalRole | The player's original role.
Alignment | The player's current alignment.
Target | The player's target.
Counter | The player's counter.
PublicVotingPower | Evaluates the player'S current public voting power.
PrivateVotingPower | Evaluates the player'S current private voting power.
RandomPlayer | Selects a random player from a list of players (e.g. `@All->RandomPlayer` would return an entirely random player).
MostFreqRole | Returns the most common role amongst a group of players (e.g. `@(Group:Wolfpack)->MostFreqRole` would return the most common role in the wolfpack).
Attr(<AttributeName>) | Returns a certain custom attribute that is applied to the player. 

### Role Type

Role type is one of the most common types and refers to a role (passive).

Selector | Meaning
--- | ---
@Target | The current player's target (must be alive).
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a role if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@VisitParameter | Refers to a parameter in a visit in `On Visited`.
@Option | Refers to the chosen option of a choice, though this will be an option type it will be cast to a role if possible.
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
^All | Refers to all roles
`<RoleName>` | Constant role 

The advanced role selector has the format ^(Property:Value) and searches for roles where a certain property matches a certain value. For example, `^(Cat:Killing)` will return all killing roles. All properties may be inverted using an `!` at the start of the value, e.g. `@(Team:!Townsfolk)` will return all roles who's are __not__ part of townsfolk.

Selector | Meaning
--- | ---
Cat/Category | The role's category.
Type | The role's type (most commonly Default and Limited).
Class | The role's class.
Team | The role's team.

Roles support a few property accesses:

Selector | Meaning
--- | ---
Category | The role's category.
Class | The role's class.
Team | The role's team.
Type | The role's type.
Players | All players that have this role.

## Game Element Formats

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
