# Formalization Guide

- [Introduction](#introduction)
- [Game Elements](#game-elements)
- [Types](#types)
  - [Player Type](#player-type)
  - [Role Type](#role-type)
  - [Active Extra Role Type](#active-extra-role-type)
  - [Group Type](#group-type)
  - [Alignment Type](#alignment-type)
  - [Location Type](#location-type)
  - [Poll Type](#poll-type)
  - [Success Type](#success-type)
  - [Result Type](#result-type)
  - [Info Type](#info-type)
  - [Ability Type Type](#ability-type-type)
  - [Ability Subtype Type](#ability-subtype-type)
  - [Ability Category Type](#ability-category-type)
  - [Number Type](#number-type)
  - [Boolean Type](#boolean-type)
  - [Attribute Type](#attribute-type)
  - [Active Attribute Type](#active-attribute-type)
  - [Display Type](#display-type)
  - [Display Value Type](#display-value-type)
  - [Category Type](#category-type)
  - [Killing Type Type](#killing-type-type)
  - [Class Type](#class-type)
  - [Source Type](#source-type)
  - [Option Type](#option-type)
  - [String Type](#string-type)
  - [Null Type](#null-type)
  - [Phase Type](#phase-type)
  - [Duration Type](#duration-type)
  - [Variable String Type](#variable-string-pseudo-type)
  - [Ghost Pseudo-Type](#ghost-pseudo-type)
  - [Dead Pseudo-Type](#ghost-pseudo-type)
  - [Actor Pseudo-Type](#actor-pseudo-type)
  - [Any Pseudo-Type](#any-pseudo-type)
  - [Variables](#variables)
- [Sources](#sources)
- [Triggers](#triggers)
- [Trigger Parameters](#trigger-parameters)
  - [Restrictions](#restrictions)
  - [Scaling](#scaling)
  - [Other Parameters](#other-parameters)
  - [Prompt Overwrites](#prompt-overwrites)
- [Conditions](#conditions)
- [Attributes](#attributes)
- [Abilities](#abilities)
  - [Killing](#killing)
  - [Investigating](#investigating)
  - [Targeting](#targeting)
  - [Disguising](#disguising)
  - [Protecting](#protecting)
  - [Applying](#applying)
  - [Redirecting](#redirecting)
  - [Manipulating](#manipulating)
- [Game Element Formats](#game-element-formats)
  - [Roles Format](#roles-format)
  - [Teams Format](#teams-format)
  - [Groups Format](#groups-format)
  - [Abilities Format](#abilities-format)


## Introduction
Werewolves Revamped is automated using "formalization" - i.e., all roles and similar are written in a formal way in a custom language (WWRF) which can be interpreted by the bot.

WWRF is designed with a "Trigger > Condition > Action" model: there are certains events that emit a "trigger" (a phase starting, a player being killed, etc) and there are game "actions" which change the game state and my emit triggers themselves (e.g. killing a player, creating a group, manipulating voting values). All elements in WWR are designed by combining triggers and actions (and optionally including conditions that limit the execution of the action to certain cases). Actions are differentiated between prompting actions, which prompt the player for an input and are only executed once such an input is provided, and automatic actions, which are executed without player interaction (and potentially even without the players knowledge)

For example, the Fortune Teller has a prompting investigation action which is triggered by the night starting. The Wolf Cub has an automatic poll creating action which is triggered by their death.

## Game Elements

Game Elements are all the components that make up the game. This includes players, roles, groups, polls, teams and more.

We differentiate between active and passive game elements. Active elements can be modified, while passive elements remain static. Passive elements are what is defined in this repository, and active elements are instances of a passive element created during the game. Outside a game, only passive elements exist. Some game elements exist in both passive and active form.

For example, the concept of a lynch poll, as defined in this repository, is a passive game element, whereas one specific lynch poll within a game is an active game element.
Passive game elements store general information about the element (e.g., a role's description), whereas active elements store game data (e.g., a player's date).

Furthermore, we differentiate between acting active elements and non-acting active elements. Acting elements are those that are able to execute (i.e., create) abilities.

For example, a player can create an ability by using their role's abilities. A display, on the other hand, cannot create or execute abilities.

The following types of game elements exist:

Name | Passive  | Active | Acting | Notes
--- | --- | --- | --- | --- 
Roles | ✅ | ⛔ | ⛔ | ∗ 
Players | ⛔ | ✅ | ✅ |  
Teams | ✅ | ✅ | ✅ | ⁑ 
Groups | ✅ | ✅ | ✅ |  
Polls | ✅ | ✅ | ✅ | ⁂
Attributes (Default) | ⛔ | ✅ | ⛔ |  
Attributes (Custom) | ✅ | ✅ | ✅ |  
Attributes (Role) | ⛔ | ✅ | ✅ | 
Abilities | ⛔ | ✅ | ⛔ |  
Displays | ✅ | ✅ | ⛔ |  
Locations | ✅ | ⛔ | ⛔ |  
Choices | ⛔ | ✅ | ⛔ |  

∗ Roles can be instantiated in two ways: when they are assigned to a player, the player sort of becomes the instantiated version of the role, though of course there is more data on the player. Alternatively, if a role is assigned as an extra role mid-game it is instantiated as a role type attribute.  
⁑ While Teams are both active and passive, they are not instantiated as there can only ever be one of each team. Instead, the team's active and passive data is stored in the same element.  
⁂ While Polls are both active and passive, and while they are instantiated, their data is still stored on the passive poll, meaning that if a poll updates its counter or target it will affect all polls of the same type.

The format of various game elements is described in more detail below.

Active game elements generally always support two values that can be modified and accessed through various methods:
- Target: A value that can store a variety of types and can be modified through targeting.
- Counter: A numeric value that can be modified through counting.

## Types

In many cases roles or other game elements need to deal with values, for example player inputs or constants used for comparisons. Each value has a certain type which must be defined or inferred.

For example, a role investigation takes one input value: a player that is to be investigated. A disguise takes two inputs: a player that should be disguised and a role they should be disguised as. These inputs may be player submissions, but may also be built into the role (for example, for a Tanner both the role and the player are user submitted, but for a Disguised Fox the player is always set to themselves, whereas the role is user submitted).

Each value needs to be annotated with its respective type; however, most of the time, the WWRF parser can do this - in the investigation example the target must always be a player, so the value is always annotated as a player type. For some other abilities, this is less clear, however. In these cases, the type can sometimes be inferred from the contents of the value itself (sometimes at parse time, sometimes at runtime) and other times the type has to be manually annotated. Check for each ability what types it expects and when annotation may be necessary. Annotation is done be appending the type surrounded by square brackets, for example: ``​`Citizen`[role]``.

Values are split into two categories: constant values (e.g. `Citizen`, referring to the citizen role) or selectors (e.g. `@Self`, referring to the current game element). Furthermore, there is a differentiation between basic selectors (e.g. `@Self` or `@Target`) which refer to a specifc value based on context and advanced selectors (e.g. `@(Role:Citizen)`, returning all players with the citizen role) which select values according to a specified query.

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
Info ⁑⁑ | `Attacked @Target` | @ActionFeedback | ⛔ | ⛔
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
Phase | `Day 1` | ⛔ | ⛔ | ⛔

∗ Players can not be referenced using a constant value, as that would require this player to be present in every game.  
⁑ Locations are a special type that unifies several other types into a single type. The following types can be interpreted as a location: group, base location, active extra role, player, attribute.  
⁂ Results are a special type which is returned after executing an ability, depending on context different types can be extracted from it. It always stores at least a success type (for if the ability succeeded) and an info type (for the ability feedback) and depending on the ability potentially other abilities.  
⁑⁑ Info is a special type that is a text containing several selectors. All selectors contained within the info text must either be annotated or support run-time annotation. The info type is used when a text output is generated. As part of the text output process, all selectors contained within the info type are evaluated and converted to text.  
⁑⁑∗ An Ability Subtype contains the info of the ability type _and_ the subtype, not just the latter.  

Some types additionally support property accesses using the `<Selector>-><Property>`, e.g. `@Target->Role` retrieves a targets role.

Many types default to not just being a single value, but actually a list, though this will often be a list of length one, this is shown in the table above. Types that are list types will return several results if a selector matches several results (though some abilities may choose to only use the first element of the list), while types that are not list types can only return a single value. When using a property access on a list type, the property access is executed on each element in the list and a new list is returned. Lists can be created through a selector which returns several values or by adding several constant values together (e.g. `Citizen`+`Wolf` would be a simple role type list).

For this document, when the syntax of any component requires a user to put a type, this syntax is used to signify the expected type: `{Type}`, where `Type` is replaced with the name of the expected type. When expecting a user to put a text that is not a specific type, this format is used: `<UserInput>`, where `UserInput` describes the expected input. When a user must pick one of several possible values the syntax `[Option1|Option2]` is used, though square brackets are used in many other contexts as well. Optional values may be listed by appending a `?` (e.g,. `{Duration?}` denotes an optional duration type value). Adding additional characters within the brackets of an optional type means these characters are either added with the type or not added at all. For example, `{(Duration?)}` may resolve to `(~Permanent)` or no value at all.

### Player Type

Player type is one of the most common types and refers to a player of the game (active). This type is also known under `player_attr` (referring to a player when acting through an extra role) or `player_group` (referring to a player when acting through a group).

When prompted, players may only submit living players as a player type, even if the player type can otherwise deal with dead or ghostly players. To allow a player to submit dead or ghostly players, the [ghost pseudo-type](#ghost-pseudo-type) or the [dead pseudo-type](#dead-pseudo-type) needs to be used.

Selector | Meaning
--- | ---
@Self | The current player (Alternatively, the player who an attribute is applied on when using from an attribute).
@All | All living players.
@Others | @All without @Self.
@Dead | All dead players.
@Ghostly | All ghostly players.
@DeadAlive | All players (dead, alive, ghostly).
@Nobody | Nobody / empty selector.
@Target | The current player's target (must be alive).
@TargetDead | The current player's target (even if the target is dead).
@Members | The current group's or team's members.
@Attacker | A player responsible for a killing in `On Death`, `On Killed`, `On Banishment`, `On Banished` and variants.
@Attacked | A player getting attacked in `On Defense` and variants.
@This | Refers to the player for whom a complex trigger featuring a target (e.g. `On <Target> Death`) triggered for.
@Winner | Refers to the winner of a poll in `On Poll Closed`.
@ActionTarget | Refers to the target of an action in `On Action` and variants.
@Executor | Refers to the executor of an action in `On Poll Closed` and `On Poll Skipped` for polls created by a group.
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
@RoleChanger | Refers to the responsible player in `On Role Change`.
@Chooser | Refers to the player that made the choice in `Choice Chosen`.
@Visitor | Refers to the visiting player in `On Visited`, `On Redirect` and variants.
@Joiner | Refers to the joining player in `On Join`.
@VisitParameter | Refers to a parameter in a visit in `On Visited`, though this will usually __NOT__ be a player.
@SecondVisitParameter | Refers to a second parameter in a visit in `On Visited`, though this will usually __NOT__ be a player.
@Ind | Refers to the current element in a `For Each` ability.
@Voters | Refers to all players that voted for the winning option in `On Poll Closed`, `On Poll Skipped` and `On Poll Win`.
@OtherVoters | @Voters without the poll winner.
@Voter | Refers to the player that voted in `On Vote Add`, `On Vote Remove` and `On Poll Change`.
@Vote | Refers to the player that was voted on in `On Vote Add` and `On Vote Remove`.
@OldVote | Refers to the player that was *previously* voted on in `On Vote Change`.
@NewVote | Refers to the player that was voted on in `On Vote Change`.
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a player if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@ID:\<ID\> | Refers to a specific player based on discord id. Useful for testing.
%Player[any]% | Refers to a player stored as host information. Replace [any] with any text or nothing.
\<ID\> | When submitting a player as Host Information you may use the discord id directly as a format.

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
OrigAlign | The player's original role's default alignment (it should be noted that this may differ even without a role change necessary, as `Align` checks for the player's current alignment, not the player's current role's default alignment).
OrigFullCat | Matches both class and category of the player's original role.
Group | Checks for membership in a certain group.
Attr/Attribute | Checks whether the player has a certain custom attribute.
AttrSelf | Checks whether the player has a certain attribute which was applied by the current player.
AttrRole | Checks whether the player has a certain role type attribute.
AttrDisguise | Checks whether the player has a disguise applied by a certain player. As value specify a player selector which will have `@` prepended, so e.g. just `Self`.
AliveOnly | Special field. Set it to `False` to also search for dead players.
SelectAll | Special field. Set it to `False` to return a single random player from the selector instead of all players.
Ghostly | Special field. Set it to `True` to search for ghostly players (only).

Players support a variety of property accesses:

Property | Meaning
--- | ---
Role | The player's current role.
Category | The player's current role's category.
OriginalRole | The player's original role.
Alignment | The player's current alignment.
Target | The player's target.
Counter | The player's counter.
PublicVotingPower | Evaluates the player's current public voting power.
PrivateVotingPower | Evaluates the player's current private voting power.
RandomPlayer | Selects a random player from a list of players (e.g. `@All->RandomPlayer` would return an entirely random player).
MostFreqRole | Returns the most common role amongst a group of players (e.g. `@(Group:Wolfpack)->MostFreqRole` would return the most common role in the wolfpack).
Attr(\<AttributeName\>) | Returns a certain custom attribute that is applied to the player. 
Count | Returns the amount of players (e.g. `@All->Count` returns the amount of living players).

### Role Type

Role type is one of the most common types and refers to a role (passive).

Selector | Meaning
--- | ---
@Target | The current player's target (must be alive).
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a role if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@VisitParameter | Refers to a parameter in a visit in `On Visited`.
@SecondVisitParameter | Refers to a second parameter in a visit in `On Visited`.
@Option | Refers to the chosen option of a choice, though this will be an option type it will be cast to a role if possible.
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
^All | Refers to all roles
``​`<RoleName>`​`` | Constant role 
%Role[any]% | Refers to a role stored as host information. Replace [any] with any text or nothing.

The advanced role selector has the format ^(Property:Value) and searches for roles where a certain property matches a certain value. For example, `^(Cat:Killing)` will return all killing roles. All properties may be inverted using an `!` at the start of the value, e.g. `^(Team:!Townsfolk)` will return all roles who's are __not__ part of townsfolk.

Selector | Meaning
--- | ---
Cat/Category | The role's category.
Type | The role's type (most commonly Default and Limited).
Class | The role's class.
Team | The role's team.
Count | Returns the number of roles.

Roles support a few property accesses:

Selector | Meaning
--- | ---
Category | The role's category.
Class | The role's class.
Team | The role's team.
Type | The role's type.
Players | All players who have this role.
Count | Returns the number of roles.

### Active Extra Role Type

Active Extra role type refers to a special subtype of attributes: role type attributes. These are rarely treated as actual attributes however. Active Extra Roles are created through grantings and show up as an additional secret channel for said role. For example, when a player is elected as a Mayor they receive Mayor as an active extra role.

Selector | Meaning
--- | ---
@ThisAttr | Refers to the current active extra role.
``​`<RoleName>`​`` | Refers to an active extra role name with the specified name which was __created by the current game element__. 

### Group Type

Group types refer to the active instance of a group. Currently, there is only the active group type, though when a group name is passed and no active group exists for this group yet, some contexts may automatically create an active instance of the group so the group type is able to return an active instance.

Selector | Meaning
--- | ---
@Self | The current group.
`#<GroupName>` | A group with the specified name.

Groups support a few property accesses:

Selector | Meaning
--- | ---
Target | The group's target.
Counter | The group's counter.
Members | All members of the group.
Attr(\<AttributeName\>) | Returns a certain custom attribute that is applied to the group. 
Count | Returns the number of groups.

### Alignment Type

Alignment type refers to a team. Teams are both passive/active at the same time.

Selector | Meaning
--- | ---
@Result[1-7] | Refers to the result of a processed ability, which will be cast to a team if possible.
@ActionResult | Refers to the result of an action in `On Action` and variants.
@VisitParameter | Refers to a parameter in a visit in `On Visited`.
@SecondVisitParameter | Refers to a second parameter in a visit in `On Visited`.
@Option | Refers to the chosen option of a choice, though this will be an option type it will be cast to a team if possible.
&All | All teams.
&Self | The current team.
&Ind | Refers to the current element in a `For Each` ability.
``​`<TeamName>`​`` | Constant alignment. 

The advanced team selector has the format &(Property:Value) and searches for teams where a certain property matches a certain value. For example, `&(Attr:Marker)` will return all teams with the marker attribute. All properties may be inverted using an `!` at the start of the value, e.g. `&(Align:!Townsfolk)` will return all teams except townsfolk.

Selector | Meaning
--- | ---
Align/Alignment | The team - only useful when inverted.
Attr | Checks whether the team has a certain custom attribute.

Alignments support a few property accesses:

Selector | Meaning
--- | ---
Target | The team's target.
Counter | The team's counter.
Members | All members of the team.
Attr(\<AttributeName\>) | Returns a certain custom attribute that is applied to the team. 
Count | Returns the number of teams.

### Location Type

Location type is a special type that combines groups, players, base locations, active extra roles and attributes into one. While the other types exists also outside the location type, base locations are only available within this type.

Selector | Type | Meaning
--- | --- | ---
`#<LocationName>` | Base Location | Name of a base location, such as town square.
`#<GroupName>` | Group | Name of a group.
``​`<RoleName>`​`` | Active Extra Role | Refers to an active extra role name with the specified name, which was __created by the current game element__. 
@Self | Player/Group | Refers to the current player (for players, active extra roles), to the player the current attribute is attached to (for attributes) or to the current group (for groups).
@AttackLocation | Player/Group | Either `@Attacker` or `@AttackSource`, depending on which can be resolved to a location.
Any Player Selector | Player | If none of the previous selectors match the specified selector, the value is treated as a player type - all player selectors are valid.

Locations support a property result __only__ when they are a group type location, in which case they support normal group type property accesses.

### Poll Type

Poll type refers to the passive poll. There is no selector for active polls.

Selector | Meaning
--- | ---
``​`<PollName>`​`` | Constant poll.
@Self | Retrieves the name of the current poll.

### Success Type

Success type represents whether an ability succeeded or not. It can take exactly two values: ``​`Failure`​`` and ``​`Success`​``.

### Result Type

Result type is a special type that encompasses several other values. In some contexts, it will automatically be cast to one of its values, but generally in most contexts it is necessary to use property access to retrieve one of the values making up the result. Results are returned by an ability when storing its result through a Process ability.

Selector | Meaning
--- | ---
@Result[1-7] | Refers to the result of a processed ability.
@ActionResult | Refers to the result of an action in `On Action` and variants.

Results support a couple of property accesses; however, not all properties are present in each result:

Selector | Meaning
--- | ---
Class | A role's class returned as a result.
Category | A role's category returned as a result.
Alignment | A role's team returned as a result.
Role | A role returned as a result.
Number | A number returned as a result.
Result | The result's result as a single value which may take several types.
Success | Whether the ability succeeded (always present).
Target | The primary target of an ability.
Message | The ability feedback a player would receive (always present).
Count | Returns the number of results.

### Info Type

Info type is a special type - it is plain text including selectors. The selectors are parsed into text if possible, for example: ``​`Your current target is @Target`​`` would inform a player of their current target. For selectors to be parsed into text correctly, they must be surrounded by spaces in both directions so that they can correctly be identified.

Selector | Meaning
--- | ---
@ActionFeedback | The feedback of a processed ability.
%PartialRoleList% | Refers to a role list info text stored as host information.
``​`<Info>`​`` | Normal info, potentially containing other selectors.

Each type uses a different method to get parsed to text - for discord elements, a respective discord reference is generated (ping, channel link) while other types are usually just their name in title case.

### Ability Type Type

Ability Type type is a type that represents an ability type. Ability types have formal names which may vary from their common names (e.g. `Investigating`).

Selector | Meaning
--- | ---
@VisitSubtype | The ability type of a visit in `On Visited`.
``​`<AbilityType>`​`` | The name of an ability type.

The following ability types exist: killing, investigating, targeting, disguising, protecting, applying, redirecting, manipulating, whispering, joining, granting, loyalty, obstructing, poll, announcement, changing, choices, ascend, descend, disband, counting, reset, cancel, feedback, success, failure, log, process_evaluate, abilities, emit, storing, displaying, win, locking, executing.

### Ability Subtype Type

Ability Subtype type is a type that represents the exact ability subtype. This is made up as a combination of the ability subtype and the ability type (e.g. `Attack Killing`), with the subtype coming first, followed by the type. Subtypes that are comprised of multiple words must have their spaces replaced by `-`'s (e.g. `True-Kill Killing`).

Selector | Meaning
--- | ---
@VisitType | The ability type of a visit in `On Visited`.
@ActionAbilityType | The ability type of an ability in `On Action`.
``​`<AbilityType>`​`` | The name of an ability type.

The following ability subtypes exist:

Ability Type | Ability Subtypes
--- | ---
Killing | Attack<br>Kill<br>Lynch<br>True-Kill<br>Banish<br>True-Banish
Investigating | Role<br>Alignment<br>Class<br>Category<br>Player_Count<br>Count<br>Attribute
Targeting | Target<br>Untarget
Disguising | Weakly<br>Strongly
Protecting | Active<br>Passive<br>Partial<br>Recruitment<br>Absence
Applying | Add<br>Remove<br>Change<br>Change_Parsed
Redirecting | ⛔
Manipulating | Absolute<br>Relative
Whispering | ⛔
Joining | Add<br>Remove
Granting | Add<br>Remove<br>Transfer
Loyalty | ⛔
Obstructing | ⛔
Poll | Creation<br>Addition<br>Deletion<br>Cancellation<br>Manipulation<br>Votes
Announcement | Immediate<br>Buffer
Changing | Role<br>Alignment<br>Group
Choices | Creation<br>Choosing
Ascend | ⛔
Descend | ⛔
Disband | ⛔
Counting | Increment<br>Decrement<br>Set<br>Increment_Math<br>Decrement_Math<br>Set_Math
Reset | ⛔
Cancel | ⛔
Feedback | ⛔
Success | ⛔
Failure | ⛔
Log | ⛔
Process_Evaluate | ⛔
Abilities | ⛔
Emit | ⛔
Storing | ⛔
Displaying | Create<br>Change
Win | ⛔
Locking | Lock<br>Unlock
Executing | ⛔

### Ability Category Type

Ability Category type is a type for categories of abilities, but currently only supports two values: ``​`all`​`` (which matches all abilities) and ``​`non-killing abilities`​`` (which matches all abilities besides killing).

Selector | Meaning
--- | ---
``​`<AbilityType>`​`` | The name of an ability category.

### Number Type

Number type is used to represent numbers.

Selector | Meaning
--- | ---
@Selection | Refers to a selection submitted by a player through a prompt.
@SecondarySelection | Refers to a selection submitted by a player through a prompt.
\<Number\> | A number.
%Number[any]% | Refers to a number stored as host information. Replace [any] with any text or nothing.
\<Variable\> | A [variable](#variables) that is evaluated to a number.
\<Number\>/\<Number\> | A division of two numbers (both of which can be one of the above options), rounded to the nearest full number.

### Boolean Type

Boolean type represents whether something is true or not. It can take exactly two values: `​True` and `False`.

### Attribute Type

Attribute type refers to a _passive_ attribute.

Selector | Meaning
--- | ---
@VisitParameter | Refers to a parameter in a visit in `On Visited`.
@SecondVisitParameter | Refers to a second parameter in a visit in `On Visited`.
``​`<AttributeName>`​`` | The name of an attribute.

### Active Attribute Type

Active Attribute type is the counterpart to the attribute type, which refers to active attribute instances. To be able to correctly resolve some types of active attribute selectors as "on element" is required (the element on which the attribute is applied). What is passed here is based on the ability. There are various variants of advanced active attribute selectors, so they are all listed in the same table.

Selector | Meaning
--- | ---
@ThisAttr | Refers to the current attribute.
``​`<AttributeName>`​`` | Searches for a custom attribute 
``​`<AttributeName>:Self`​`` | Searches for a custom attribute, applied by the current player.
``​`<AttributeName>:<SourceReference>`​`` | Searches for a custom attribute, applied by a certain source specified by its reference. This is not very usable most of the time as most source references are different each game, however, team names can be used. See [sources](#sources).
``​`<AttributeName>:<SourceName>`​`` | Searches for a custom attribute, applied by a certain source specified by its name. See [sources](#sources).
``​`<AttributeName>::<Val1>`​`` | Searches for a custom  attribute, with a specific value in Value 1.
``​`<AttributeName>:Self:<Val1>`​`` | Combination of the above.
``​`<AttributeName>:<SourceReference>:<Value1>`​`` | Combination of the above.
``​`<AttributeName>:<SourceName>:<Value1>`​`` | Combination of the above.
``​`<GenericAttributeType>`​`` | Searches for generic attributes of the specified type. Valid generic attribute types are the following: disguise, defense, absence, manipulation, groupmembership, obstruction, pollcount, pollresult, polldisqualification, pollvotes, role, redirection, loyalty, whisper.
``​`<GenericAttributeType>:Self`​`` | Combination of the above.
``​`<GenericAttributeType>:<SourceReference>`​`` | Combination of the above.
``​`<GenericAttributeType>:<SourceName>`​`` | Combination of the above.
``​`<GenericAttributeType>:<Value1>`​`` | Combination of the above.

Active attributes support a few property accesses:

Selector | Meaning
--- | ---
Target | The attribute's target.
Counter | The attribute's counter.
Source | The attribute's creator player. Fails if not created by a player.
Value1 | The attribute's value1 which may hold arbitrary data.
Value2 | The attribute's value2 which may hold arbitrary data.
Value3 | The attribute's value3 which may hold arbitrary data.
Count | Returns the amount of attributes.

### Display Type

Display types can only be a constant value referring to the display name.

### Display Value Type

Display Value types are used for the values displayed in a display. All text values are supported (and will be displayed as text), but some special values have special meaning.

Value | Meaning
--- | ---
Yes | The yes emoji.
No | The no emoji.
Counter | The counter of the display's creator.
Target | The target of the display's creator.
\<AnyText\> | Just the text.

### Category Type

Category types represent the category of a role. Supported values are any text, but automatic type annotation is only done for known categories (e.g. `Miscellaneous`).

### Killing Type Type

Killing type represents the type of a killing. 

Selector | Meaning
--- | ---
@DeathType | Set to the type of killing in `On Death`.
@KillingType | Set to the type of killing in `On Killed`.
``​`<KillingType>`​`` | A constant killing type. Supported values are: attack, kill, lynch, true kill, banish, true banish.

### Class Type

Class type refers to a role's class. Supported values are any text, but automatic type annotation is only done for known classes (e.g. `Limited`).

### Source Type

Source type is one of the rare instances where the formalization system can directly interact with sources (See [sources](#sources). The source type represents the source name (e.g. `Role:Assassin` or `Group:Wolfpack`).

Selector | Meaning
--- | ---
@AttackSource | Returns the source of an attack in `On Death` and `On Killed`. For groups this will differ from the attacker.
@TriggerSource | Returns the source of the trigger in `On Any Action`.
``​`<SourceNameType>:<SourceNameValue>`​`` | A colon separated two segement value, with the first specifying the source type (e.g. `Role`) and the second the name/value (e.g. `Assassin`).

### Option Type

Option type is the value for/from choices.

Selector | Meaning
--- | ---
@Chosen | Special selector which should only be used in the choice choosing ability as it generates a choice prompt with which @Chosen is filled.
@Option | The chosen option in `Choice Chosen`.
``​`<OptionName>`​`` | The name of an option.

### String Type

String type represents an arbitrary text. Can take any value, for example ``​`This is a text.`​``.

Selector | Meaning
--- | ---
@VoteText | A text representation of the option that was voted on in `On Vote Add` and `On Vote Remove`.
@OldVoteText | A text representation of the option that was *previously* voted on in `On Vote Change`.
@NewVoteText | A text representation of the option that was voted on in `On Vote Change`.
``​`<String>`​`` | The text.
%String[any]% | Refers to a string stored as host information. Replace [any] with any text or nothing.

### Null Type

Null type is a special type that should not be used manually. When inferring the type of a target at runtime, but no target is set, a null type list with zero elements is instead returned. This is to ensure that abilities relying on a target gracefully fail instead of erroring. Null type always takes the form of an empty list.

Technically, null types also support property accesses, however each property access will simply return an empty list as well.

### Phase Type

Phase type represents a phase and is made up of the type of phase (Day or Night) and the phase's number. 

Value | Meaning
--- | ---
Day \<Number\> | A day phase.
Night \<Number\> | A night phase.

### Duration Type

Duration type is a type only used by attribute applying abilities. Durations take the form of `~<Name>`. A full list of available attribute durations can be found in the [attribute section](#Attributes).

### Variable String Pseudo-Type

Variable String pseudo-type represents an arbitrary text that is sometimes the expected input. Can take any value, for example ``​`This is a text.`​``. [Variables](#variables) within this text are resolved.

### Ghost Pseudo-Type

Ghost pseudo-type is not an actual type, but can be specified as a type annotation. The type is equivalent to the player type, but specifying the ghost pseudo-type allows players to submit ghostly players when prompted (prompts for player type restrict the input to living players).

### Dead Pseudo-Type

Dead pseudo-type is not an actual type, but can be specified as a type annotation. The type is equivalent to the player type, but specifying the dead pseudo-type allows players to submit dead players when prompted (prompts for player type restrict the input to living players).

### Actor Pseudo-Type

Actor pseudo-type is not an actual type, but is sometimes the expected input. In that case, the input can take the form of any acting game element. The following types may be specified when an actor type is expected: player, group, team, active attribute, active extra role.

### Any Pseudo-Type

Any pseudo-type is a pseudo-type that is sometimes the expected input. In that case _any_ type may be specified.

### Variables

Variables aren't directly a type, but are used in some contexts. When a variable is evaluated it returns a value dependent on the current game state.

Variable | Meaning
--- | ---
$total | Amount of players in the game.
$living | Amount of living players.
$dead | Amount of dead players.
$ghostly | Amount of ghostly players.
$haunting | Boolean value that reflects whether haunting is enabled.
$phase | The current phase as a number.
$phname | The current phase as a text.

## Sources

Sources are used internally to represent an acting active game elements. Only very few elements of formalization directly interact with sources, but it still useful to understand them. Furthermore, they are often shown or used in debug commands.

A source represents a unique named reference to an acting game element and is stored in other game elements it creates (for example polls, displays and attributes store their source).

Source consist out of two parts. A source reference (a unique reference to the acting game element) and a source name (a non-unique name for the acting game element). Each of the two parts is made up of a type and a value. For example a source reference may look like this: `player:350000308900100207` (made up of the `player` type and a unique player id), while a source name may look like this: `role:citizen` (made up of the `role` type and the player's role).

Here is a list of existing source pairs:

Reference Type | Reference Value | Name Type | Name Value | Example | Explanation
--- | --- | --- | --- | --- | ---
`player` | Member ID | `role` | Role Name | `player:350000308900100207` / `role:citizen` | Represents a player when acting through their primary role. References the player's discord id.
`player_attr` | Channel ID | `role` | Role Name | `player_attr:30050368990104207` / `role:mayor` | Represents a player when acting through a secondary/extra role. References the extra role's discord channel id.
`group` | Channel ID | `group` | Group Name | `group:45050668799134173` / `group:wolfpack` | Represents a group when acting directly. References the group's discord channel id.
`player_group` | Member ID | `group` | Group Name | `player_group:350000308900100207` / `group:wolfpack` | Represents a player when acting as the executor of a group action. References the executor's discord id.
`poll` | Poll Name | `poll` | Poll Name | `poll:election` / `poll:election` | Represents a poll. References the poll's name. 
`team` | Team Name | `team` | Team Name | `team:townsfolk` / `team:townsfolk` | Represents a team. References the team's name.
`attribute` | Attribute ID | `attribute` | Attribute Name | `attribute:13` / `attribute:wolfish` | Represents an attribute. References the attribute's unique id.

## Triggers

Triggers are one of the main parts of formalization. Triggers determine when an ability is executed. Triggers are differentiated between prompting triggers, which create a prompt for each ability (even if no input is required - in this case the player simply needs to confirm the ability) and automatic triggers. Some triggers have special selectors available within them that are not available in other contexts.

Some triggers are considered to be complex triggers. Such triggers take one or more parameters to limit their execution to certain conditions. 

A list of triggers can be found here:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Trigger&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Explanation | Additional Selectors
--- | --- | ---
Start Night<br>End Night<br>Start Day<br>End Day<br>Immediate Night<br>Immediate Day<br>End Phase<br>Start Phase<br>Immediate<br>Pre-End Night<br>Pre-End Day | Prompting triggers for different timings. | ⛔
Second Pre-End Night<br>Third Pre-End Night<br>Fourth Pre-End Night<br>Second Pre-End Day<br>Third Pre-End Day<br>Fourth Pre-End Day | Prompting triggers for different timings. Higher numbers are evaluated first (Fourth > Third > Second > Pre > End) | ⛔
Passive End Day<br>Passive End Night<br>Passive Start Day<br>Passive Start Night<br>Passive Start Phase<br>Passive End Phase | Non-prompting triggers for different timings. | ⛔
Starting | Triggers at the start of the game, and whenever a new group/role/team/attribute/etc is created. | ⛔
On Assigned | Triggers whenever an extra role is created or assigned to a new player. | ⛔
Passive | Triggers whenever a significant change occurs (game start, phase change, somebody dies/changes roles) and can be used with conditions that should theoretically be checked constantly. | ⛔
On Death | Triggers when the current player dies. | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Death | Triggers when a player from a specific selector dies. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Killed | Triggers when the current player is killed (true kill, kill or attack, but __not__ lynch). | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Killed | Triggers when a player from a specific selector is killed. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Banishment | Triggers when the current player is banished. | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Banishment | Triggers when a player from a specific selector is banished. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Banished | Triggers when the current player is banished?? Do not use. | @Attacker<br>@DeathType<br>@AttackSource
On {Player} Banished | Triggers when a player from a specific selector is banished?? Do not use. | @Attacker<br>@DeathType<br>@AttackSource<br>@This
On Lynch | Triggers when the current played is lynched. | @Attacker<br>@DeathType<br>@AttackSource
On Defense<br>On Active Defense<br>On Passive Defense<br>On Partial Defense<br>On Recruitment Defense<br>On Absence Defense | Triggers when one of the current player's defenses of the specified (or any) type is used. Performs a Source Name match - this requires the defense to have been created by the same role the trigger is in. This triggers even if the defense is applied to another player. | @Attacker<br>@Attacked<br>@DeathType<br>@AttackSource
On Visited<br>On Visited [{AbilityType}]<br>On Visited [{AbilitySubtype}]<br>On Visited [!{AbilityType}]<br>On Visited [!{AbilitySubtype}] | Triggers when the current player is visited. Can be filtered to limit it to or to exclude specific ability types or subtypes. | @Visitor<br>@VisitParameter<br>@SecondVisitParameter<br>@VisitType<br>@VisitSubtype 
On {Player} Visited<br>On {Player} Visited [{AbilityType}]<br>On {Player} Visited [{AbilitySubtype}]<br>On {Player} Visited [!{AbilityType}]<br>On {Player} Visited [!{AbilitySubtype}] | Triggers when a player from a specific selector is visited. Can be filtered to limit it to or to exclude specific ability types or subtypes. | @Visitor<br>@VisitParameter<br>@SecondVisitParameter<br>@VisitType<br>@VisitSubtype<br>@This
On Action<br>On Action [{AbilityType}]<br>On Action [{AbilitySubtype}]<br>On Action [!{AbilityType}]<br>On Action [!{AbilitySubtype}] | Triggers when the current player performs an action. Can be filtered to limit it to or to exclude specific ability types or subtypes. Performs a Source Name match - this requires the action to be performed from the same role the trigger is in. | @ActionTarget<br>@ActionResult<br>@ActionFeedback<br>@ActionAbilityType
On Any Action<br>On Any Action [{AbilityType}]<br>On Any Action [{AbilitySubtype}]<br>On Any Action [!{AbilityType}]<br>On Any Action [!{AbilitySubtype}] | Triggers when the current player performs an action. Can be filtered to limit it to or to exclude specific ability types or subtypes. Does __not__ perform a Source Name match - the action may be performed from a different role than the trigger. | @ActionTarget<br>@ActionResult<br>@ActionFeedback<br>@ActionAbilityType<br>@TriggerSource
On {Player} Action<br>On {Player} Action [{AbilityType}]<br>On {Player} Action [{AbilitySubtype}]<br>On {Player} Action [!{AbilityType}]<br>On {Player} Action [!{AbilitySubtype}] | Triggers when a player from a specific selector performs an action. Can be filtered to limit it to or to exclude specific ability types or subtypes. | @ActionTarget<br>@ActionResult<br>@ActionFeedback<br>@ActionAbilityType<br>@This
On Disbandment | Triggers when the current group is disbanded. | ⛔
On Redirect | Triggers when the current player has redirected an ability. | @Visitor
On Betrayal | Triggers when a player changes role while being part of the current group | ⛔
On Poll Closed | Triggers when a poll created by the current player/group/poll through poll creation is closed and a winner exists. | @Winner, @Voters, @OtherVoters, @Executor
On Poll Skipped | Triggers when a poll created by the current player/group/poll through poll creation is closed and no winner exists. | @Voters, @OtherVoters, @Executor
On Poll Win | Triggers when the current player wins a poll. | @Voters, @OtherVoters
On Poll {Poll} Win | Triggers when the current player wins a specific poll. | @Voters, @OtherVoters
On Role Change | Triggers when the current player changes role. | @RoleChanger
On Removal | Triggers when the current attribute is removed through remove applying. | ⛔
On End | Triggers when the game ends. | ⛔
On Emitted<br>On {String} Emitted | Triggers when an emitting action was used to emit a specific value. | ⛔
On End Emitted<br>On {String} End Emitted | Triggers when an end phase emitting action was used to emit a specific value. | ⛔
On Vote Add<br>On Vote Remove | Triggers when a vote is added or removed for a poll. | @Voter<br>@Vote<br>@VoteText
On Vote Change | Triggers when a vote is added and removed for a poll within 15 seconds by the same player - in this case On Vote Add/Remove are replaced by a single On Vote Change. | @Voter<br>@OldVote<br>@OldVoteText<br>@NewVote<br>@NewVoteText
On Hammer | Triggers when a poll causes a hammer. | ⛔

## Trigger Parameters

Triggers can be further limited/extended with additional parameters. There are two primary types of trigger parameters: restrictions and scaling. The former restricts the execution of all abilities attached to the trigger to certain situations, whereas the latter changes the amount of times the abilities attached to the trigger are executed.

These parameters are always attached to the trigger and thus always apply equally to all actions of the trigger.

When using several types of trigger paramaters, the order __must always__ be: Restrictions > Scaling > Other Paramaters > Prompt Overwrites.

### Restrictions

Trigger restrictions are marked by square brackets: []. When using several restrictions they are part of the same square bracket block. For example, `[Temporal: Day 1+, Succession: No Succession]`.

Trigger Restrictions can be one or more of the following:

Name | Syntax | Explanation | Example
--- | --- | --- | ---
Temporal Restriction | `Temporal: {Phase}` | May only be executed in `{Phase}`. | `Temporal: Day 0` 
Temporal Restriction | `Temporal: {Phase}+` | May only be executed in `{Phase}` or after. | `Temporal: Night 2+`
Temporal Restriction | `Temporal: [Day\|Night]` | May only be executed during day/night. | `Temporal: Night`
Attribute Restriction | `Attribute: has <AttributeName>` | May only be executed if `<AttributeName>` is present on the current element. | ``Attribute: has `Wolfish`​``
Attribute Restriction | `Attribute: lacks <AttributeName>` | May only be executed if `<AttributeName>` is __not__ present on the current element. | ``Attribute: lacks `Wolfish`​``
Attribute Restriction | `Attribute: {Actor} [has\|lacks] <AttributeName>` | May only be executed if `{Actor}` (a selector) has/lacks `<Attribute>`. | ``Attribute: @Target lacks `Wolfish`​``
Succession Restriction | `Succession: No Succession` | May not be executed in in succession. | ⛔
Succession Restriction | `Succession: No Target Succession` | May not be executed successively on the same target. | ⛔
Quantity Restrictions | `Quantity: <Number>` | May only be executed a maximum of `<Number>` times. | `Quantity: 3`
Condition Restriction | `Condition: <Condition>` | May only be executed if a [condition](#conditions) passes. | `Condition: @Target is in #Wolfpack`
(Living) Status Restriction | `Status: [Ghostly\|Any\|Alive]` | May only be executed while ghostly/any status/alive (the latter being the default). | `Status: Ghostly`

### Scaling

Trigger scalings are marked by angle brackets: ⟨⟩.

Trigger Scalings can be one of the following:

Name | Syntax | Explanation | Example
--- | --- | --- | ---
Static Scaling | `x<Number>` | Specify a number (`Value`) that determines how many times the trigger will be executed. | `x2`
Dynamic Scaling | `[$total\|$living]/<Number>` | The trigger will be executed exactly as many times as the player count divided by the specified `Number`, rounded down. | `$total/2`
Dynamic Scaling | `[$total\|$living]<Comparison><Number> ⇒ <Count>` | A comma separated list of conditions, where `Comparison` may be `<`, `>`, `≤`, `≥` or `=` and `Number` is a constant number specifying a player amount and `Count` is the amount of executions. The default value for scaling is 1, but each failed condition sets the value to 0, so a condition should be specified first, followed by a default value. | `$total<10 ⇒ 1, $total≥10 ⇒ 2, 3`
Phase Specific Scaling | `Odd: x<Number>, Even: x<Number>` | To specify different multiplicities of trigger executions in even and odd phases. | `Odd: x1, Even: x2`

### Other Parameters

Other trigger parameters are marked by curly brackets: {}. Curly brackets in this section do __not__ signify type markers like in the rest of the document. When using several other parameters, their curly bracket blocks are combined, e.g. `{Forced, Direct}`.

To make a prompting trigger forced, a Trigger Compulsion can be set by specifying `{Forced}`. By default, a forced trigger randomly selects a target where necessary. If a forced trigger should select a specific target, use `{Forced: <Default Target>}` instead.

To make a trigger unaffected by redirections, specify `{Direct}`.

To make a trigger not perform a visit (which also makes it unaffected by redirections!), specify `{Visitless}`.

To make it so that a trigger's prompt is deleted when it is ignored/not executed, specify `{Vanishing}`.

### Prompt Overwrites

Prompt overwrites are marked by vertical brackets: ||.

By default, a prompting trigger will generate a prompt message code name and look it up in the prompt message file. To overwrite the default behavior, you can specify the name of a custom prompt. For example, `|custom.prompt.1|` would look up the prompt text for `custom.prompt.1`.

To additionally mark a prompt as silent (which skips the ping), you may specify `silent:` before the prompt name, e.g. `|silent:custom.prompt.1|`.

## Conditions

Conditions are a part of the formalization that can be used in several places (Restrictions and Process/Evaluate) to limit the execution of abilities to certain situations.

The following conditions exist:

Name | Syntax | Explanation
--- | --- | ---
Equality | `{Any} is {Any}` | Compares two values with each other, passes if equal. When comparing lists, equality is only checked for the first value of each list (e.g. [a,b] = [a,c] will pass).
Less/Greater Than | `{Number} [>\|<] {Number}` | Compares two numbers with each other, passes if the first value is greater/less than the second. When comparing lists, the first value of each list is used.
No Equality | `{Any} is not {Any}` | Compares two values with each other, passes if different.
Existence | `{Any} exists` | Checks if a specified selector evaluates to at least one element.
Attribute | `{Actor} has {Attribute}` | Checks if a specified actor has a specified attribute.
Membership | `{Player} is in {Group}` | Checks if a specified player is in a specified group.
Selector | `{Any} is part of {Any}` | Checks if a specified element is part of another selector.
Inversion | `not (<Condition>)` | Passes if the specified condition is false.
And | `(<Condition>) and (<Condition>)` | Passes if both specified conditions are true.
And x2 | `(<Condition>) and (<Condition>) and (<Condition>)` | Passes if all specified conditions are true.
And x3 | `(<Condition>) and (<Condition>) and (<Condition>) and (<Condition>)` | Passes if all specified conditions are true.
Or | `(<Condition>) or (<Condition>)` | Passes if one of the two specified conditions is true.
Or x2 | `(<Condition>) or (<Condition>) or (<Condition>)` | Passes if one of the specified conditions is true.
Or x3 | `(<Condition>) or (<Condition>) or (<Condition>) or (<Condition>)` | Passes if one of the specified conditions is true.
And/Or | `(<Condition>) and (<Condition>) or (<Condition>)` | Passes if the first, and at least one of the other conditions are true.
Or/And | `(<Condition>) or (<Condition>) and (<Condition>)` | Passes if the third, and at least one of the other conditions are true.

Even though this makes little sense, you may not combine and/or operations in any other way than listed above and cannot contain and/or operations inside the conditions specified for an and/or operation.

## Attributes

Attributes are one of the most common game elements and attribute applying abilities are one of the most common ability types.  

Attributes are acting active game elements that are applied onto another game element and apply some additional power/ability/limitation onto that element. All attributes have an attribute type that determines what their effect is, a typed owner (who the attribute is applied on), a typed source (who created the attribute - source reference and source name), an attribute duration (determining how long the attribute lasts), a usage tracker (counting the amount of times the attribute has been used), an application phase (a numeric value representing the phase the attribute was applied in), an aliveness (signifying whether the attribute owner is still alive, if they are a player), a counter and target (like all acting game elements) and up to four additional paramaters with varying functionality depending on the attribute type.  

Attributes come in three categories:  
• Role Type Attribute: Role type attributes are a type of attribute which represent an additional role applied to a player. For most purposes, this additional role acts as a normal role would, making it an acting attribute.  
• Custom Type Attribute: Custom type attributes are attributes which are formalized in a passive attribute. In there, formalization defines certain acting behavior for the attribute.  
• Default Attributes: The remaining attribute types are fully "built-in" to the bot and cover things such as disguises and vote manipulations as well as many more. While these attributes support all the fields of the other attribute types, they "choose" not to use any features which would qualify them as an "acting" game element and can thus be considered to be non-acting. All their functionality is built into the various abilities (see [abilities](#abilities))  

The different types of attributes are explained in more detail in their respective ability sections.

Attributes can be defined to last forever or to only be temporary. This is achieved through an attribute duration, which can be passed in most abilities that create attributes. Some abilities have preset durations for their attributes, and some have default values that can be overwritten. This is explained in detail for each ability in their respective sections.

The following ability durations exist:

Name | Explanation
--- | ---
`~Persistent` | Lasts forever, unless manually removed
`~Permanent` | Lasts until role loss
`~Phase` | Lasts until the end of the current phase
`~PhaseAttribute` | Lasts until the end of the current phase (see `~Phase`) or until the originating attribute disappears (see `~Attribute`)
`~NextPhase` | Lasts until the end of the next phase
`~NextDay`, `~NextNight` | Lasts until the end of the next day/night
`~NextPhaseAttribute`, `~NextDayAttribute`, `~NextNightAttribute` | Combination of the above and `~Attribute`.
`~UntilUse` | Lasts until the attribute is used. (When an attribute is 'used' depends on the attribute type - more in the respective ability sections)
`~UntilSecondUse` | Lasts until the attribute is used twice
`~Attribute` | Lasts as long as the originating attribute is applied, can only be used inside an attribute.
`~UntilUseAttribute` | Lasts until used (see `~UntilUse`) or until the originating attribute disappears (see `~Attribute`)

## Abilities

Abilities are used by all active game elements to take action in a game. While some abilities are unique, there are three major categories of abilities:
- Attribute Appliers: Many abilities apply an attribute - the attribute then takes care of the main impact of the ability.
- Game Action: Abilities of this type update some part of the game (besides attributes) directly.
- Game Logic: Abilities of this type don't directly affect the game, instead, they are used in combination with other abilities to determine how those abilities are executed.

### Killing

**Summary:** Killing is a Game Logic type action - it can modify the aliveness status of a player. By default, killing abilities are queued up and executed at the very end of a trigger. For example, a player that is killed during an "End Night" trigger may still execute their own "End Night" ability, even if their kill is queued up in the same trigger before their ability was triggered. 

When a player is killed to death, they are eliminated from the game. If they are killed to banishment, they may still participate as a ghost. Should the dying player be the last owner of a group, the group is disbanded. 

**Attributes:** Killing abilities are affected by defense and absence attributes that match the subtype of the killing ability. The killing ability checks for defense and absence attributes in the following order: Absence -> Active Defense -> Passive Defense -> Partial Defense -> Recruitment Defense. The first matching attribute that is found is used for the evasion. 

**Visits:** When killing another player, a visit to that player occurs.

**Redirections:** The target of a killing may be redirected.

**Success:** A killing ability succeeds if at least a single killing ability can be queued up. Killing abilities that are evaded through defense attributes are not considered to be queued up.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | First target of the ability

**Subtypes:** The subtypes differ in what defenses they are affected by, in what their result is, and in what triggers they run.

<table>
<thead><tr><th>Subtype</th><th>Syntax</th><th>Result</th><th>Executed Triggers</th></tr></thead>
<tbody>
<tr><td>Kill</td><td><code>Kill {Player}</code></td><td rowspan=4>Death</td><td rowspan=3>On Death<br>On Killed<br>On Death Complex<br>On Killed Complex<br>Passive</td></tr>
<tr><td>Attack</td><td><code>Attack {Player}</code></td></tr>
<tr><td>True Kill</td><td><code>True Kill {Player}</code></td></tr>
<tr><td>Lynch</td><td><code>Lynch {Player}</code></td><td>On Death<br>On Lynch<br>On Death Complex<br>Passive</td></tr>
<tr><td>Banish</td><td><code>Banish {Player}</code></td><td rowspan=2>Banishment</td><td rowspan=2>On Banished<br>On Banishment<br>On Banished Complex<br>On Banishment Complex<br>Passive</td></tr>
<tr><td>True Banish</td><td><code>True Banish {Player}</code></td></tr>
</tbody></table>

**Triggers:** The killing ability will emit the "On Defense" trigger if a defense is used to evade a killing ability, as well as the matching specific variant (On Absence Defense, On Active Defense, On Passive Defense, On Partial Defense, On Recruitment Defense). Additionally, upon a successful killing (i.e. not just the queuing, but also the successful execution), the triggers listed in the table above are executed.

### Investigating

**Summary:** Investigating is a special ability that technically doesn't have any effect - it merely returns feedback - however, its feedback is much more useful than that of other abilities as it provides additional info about other players and/or their roles.

**Attributes:** Investigating is affected by disguise attributes. Most ability subtypes check for weak and/or strong disguises as specified in the ability. In case both disguises are checked for, strong disguises take priority over weak disguises.

**Visits:** All investigating subtypes cause a visit to the player the ability is targeting (except for ones not targeting a player).

**Redirections:** The target of all investigating subtypes can be affected by redirections, though the feedback (if it mentions the target) will not be affected by that.

**Success:** Most subtypes are always successful (unless obstructed), exceptions are listed under subtypes.

**Feedback:** 

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | First target of the ability
Result | Any | The result of the investigating. The same value is also passed on a subtype specific value. See subtypes

**Subtypes:** 

For `<Disguise Levels>`, one of the following needs to be specified:

Value | Meaning
--- | ---
*blank* | Affected by no disguises.
(SD) | Affected by strong disguises only.
(WD) | Affected by weak disguises only.
(WD, SD)<br>(SD, WD) | Affected by both weak and strong disguises.

Subtype | Syntax | Feedback | Feedback Name
--- | --- | --- | --- 
Role | `Role Investigate {Player} <Disguise Levels>` | The player's role. | Role
Class | `Class Investigate {Player} <Disguise Levels>` | The player's role's class. | Class
Category | `Category Investigate {Player} <Disguise Levels>` | The player's role's category. | Category
Alignment | `Alignment Investigate {Player} <Disguise Levels>` | The player's role's alignment. | Alignment
Attribute | `Attribute Investigate {Player} for {Attribute} <Disguise Levels>` | Whether the attribute is present or not. | Success
Count | `Investigate {Role} Count <Disguise Levels>` | The amount of times a certain role exists. | Number
Player Count | `Investigate {Player} Count` | The amount of players in a selector. | Number

**Triggers:** There are no triggers associated with Investigating.

### Targeting

**Summary:** Targeting is an extremely basic Game Action that updates the current acting element's target field, a field present for every acting element. Elements can store a variety of values into their target field (though they are limited to storing a single value, and cannot store a list). An acting element can retrieve its own current target value through the `@Target` selector, though it may also be retrieved through property access.

**Attributes:** There are no attribute interactions specific to targeting.

**Visits:** A visit occurs for the `target` subtype.

**Redirections:** The `target` subtype may be affected by redirections.

**Success:** Besides obstructions, targeting is always successful, unless there is a syntax error or the target is invalid. Furthermore, the ability will fail when attempting to target more than one value.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | The selected target (`target` subtype)

**Subtypes:** 

Subtype | Syntax | Function
--- | --- | ---
Target | `Target {*} ({**})` | Sets a players target, specify a selector (`{*}`) and additionally annotate the type (`{**}`), the latter of which must be one of the following values: Player, Dead, Role, Attribute, Category, Full Category, Boolean, Option, Player Optional. The former must be a selector of matching type. Player Optional is a special variant of Player in which 'nobody' is accepted as an input and leads to resetting the target/untargetting.
Untarget | `Untarget` | Removes the player's target.

**Triggers:** There are no triggers associated with targeting.

### Disguising

**Summary:** Disguising is an Attribute Applier type action that applies a disguise attribute to another player. Disguises affect the outcome of [Investigating](#investigating) abilities.

**Attributes:** Disguising creates disguise attributes. Each disguise attribute stores a role and the strength of the disguise. Disguises must store a valid role. For more info, see [Investigating](#investigating).

Property | Value
--- | ---
Attribute Type | `disguise`
(1) Disguise Role | `{Role}` 
(2) Disguise Strength | `[weak\|strong]`

Disguises result in an investigating ability seeing another role besides what they would usually see. This can also affect role adjacent results such as a team investigating - in this case the disguise's team is seen.

Whenever a disguise is used to affect an investigation, an attribute usage is tracked.

**Visits:** Disguising causes a visit to the player getting disguised.

**Redirections:** Disguisings may be redirected.

**Success:** Besides obstructions, disguising is always successful.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | First target of the ability

**Subtypes:** 

Subtype | Syntax
--- | ---
Weakly | `Weakly Disguise {Player} as {Role} {(Duration?)}`
Strongly | `Strongly Disguise {Player} as {Role} {(Duration?)}`

**Triggers:** There are no triggers associated with targeting.

### Protecting

**Summary:** Protecting is an Attribute Applier type action that applies a defense or absence attribute to another player. Defenses/Absences affect the outcome of [Killing](#killing) abilities.

**Attributes:** Protecting creates defense/absence attributes. Each defense/absence attribute stores several properties defining which killings they defend from. When a killing occurs, first defenses are filtered based on their properties to find which apply to the current killing. Then the killing ability checks for defense and absence attributes in the following order: Absence -> Active Defense -> Passive Defense -> Partial Defense -> Recruitment Defense. The first matching attribute that is found is used for the evasion.

Defense and Absence attributes are fairly similar, with the difference that the Absence stores an absence location instead of a defense subtype.

Property | Value
--- | ---
Attribute Type | `defense`
(1) Defense Type (Defense Attribute)<br>(1) Absence Location (Absence Attribute) | `[active\|passive\|partial\|recruitment]`<br>`<Source Reference>`
(2) Killing Subtype Filter | `[attacks\|kills\|lynches\|attacks_lynches\|all\|banishments]`
(3) Selector Filter | `{Player}`
(4) Phase Filter | `[day\|night\|all]`

The values in the attribute correspond to the values set in the protecting ability; for a list of all possible syntax variants, see below. Each killing goes through the following checks:  
- Does the subtype of the killing match an allowed killing subtype? For this, the following killing subtypes are allowed in the following killing subtype filters:
  - `attack`: can be defended against with `attacks`, `kills`, `attacks_lynches` and `all` filters.
  - `kill`: can be defended against with `kills` and `all` filters.
  - `lynch`: can be defended against with `lynches`, `attacks_lynches` and `all` filters.
  - `banish`: can be defended against with `banishments` filters.
  - `true kill` / `true banish`: can not be protected against.
- Does the player executing the killing match the selector filter? For this the defense stores a player type selector, which is parsed at runtime when the defense is evaluated and checks if the killing player occurs within this selector (For example, the selector `@(Attr:Marker)` may be stored. This is then resolved at execution time of the defense and it is checked if the killing player occurs within this selector).
- Is the killing occurring in an allowed phase? Some defenses only work at `day` or `night`, while others work in `all` phases.

Absences are evaluated just like defenses are and work extremely similarly to defenses with one exception: whenever a killing is used, all absences are checked - any absences where the absence location is set to the player that is currently getting hit by the killing, will be affected by the killing too. Both killings are executed separately. For example, Player A may be absent at Player B when Player B is getting attacked. That means Player A will be attacked as well, however, both attacks are still executed individually and may also be affected by other defenses.

Absences are not recursive. That means that a Player C who is absent at Player A, will _not_ get attacked by the attack on Player B, which is affecting Player A.

Players may be absent at non-player locations (e.g. at a public channel), however in that case there is no chance to get attacked/killed through the absence, as non-player locations can not be targeted by killings.

Whenever a defense or absence is used to evade an attack, an attribute usage is tracked.

**Visits:**  When applying a defense/absence, a visit occurs to the target. When applying an absence __NO__ visit occurs to the absence location \[note: this may be unintentional\].

**Redirections:** Protecting may be redirected. For the absence subtype, both the absence location and the target of the defense may be redirected.

**Success:** Besides obstructions, protecting is always successful. The absence subtype will additionally fail if the specified absence location yields more than a single value.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | First target of the ability

**Subtypes:** 

There are 4 basic subtypes and the special absence subtype. There are several syntax variants that are the same for all the subtypes, so they are not individually listed. Instead, `<ProtectingSubtype>` may be substituted with one of the following:
- `Active Defense`
- `Passive Defense`
- `Partial Defense`
- `Recruitment Defense`
- `Absence at {Location}`

Allowed values for `<KillingSubtypeFilter>` are the following (for functionality, see above):
- `Attacks`
- `Kills`
- `Lynches`
- `Attacks & Lynches`
- `All`
- `Banishments`

Syntax | Killing Subtype Filter Value | Selector Filter Value | Phase Filter Value
--- | --- | --- | ---
``Protect {Player} from `<KillingSubtypeFilter>` by {Player} through <ProtectingSubtype> during [Day\|Night] {(Duration?)}`` | `<KillingSubtypeFilter>` | `{Player}` | `[Day\|Night]`
``Protect {Player} from `<KillingSubtypeFilter>` by {Player} through <ProtectingSubtype> {(Duration?)}`` | `<KillingSubtypeFilter>` | `{Player}` | `All`
``Protect {Player} from `<KillingSubtypeFilter>` through <ProtectingSubtype> during [Day\|Night] {(Duration?)}`` | `<KillingSubtypeFilter>` | `@All` | `[Day\|Night]`
``Protect {Player} from `<KillingSubtypeFilter>` through <ProtectingSubtype> {(Duration?)}`` | `<KillingSubtypeFilter>` | `@All` | `All`

**Triggers:** When a defense is used, two triggers are run for the player who created the defense (This means a player may apply a defense to another player and use this trigger to get notified when that player's defense is used). Each defense usage triggers the `On Defense` trigger, and then additionally, depending on subtype, one of the following: `On Absence Defense`, `On Active Defense`, `On Passive Defense`, `On Partial Defense` or `On Recruitment Defense`.

### Applying

**Summary:** Applying is an Attribute Applier type action that applies (or removes/changes) custom type attributes to another game element. While applying itself is a fairly basic action, custom type attributes can have a major impact on the game as they are acting game elements themselves, i.e. they may grant the game element they are applied to additional powers or may apply other effects onto it.

**Attributes:** Applying creates custom type attributes. Custom type attributes are acting game elements that can store a target/counter/etc, however their basic attribute properties are the following:

Property | Value
--- | ---
Attribute Type | `custom`
(1) Custom Type | `<Attribute Name>`<br>The name of the passive attribute that this active attribute is instantiated from.
(2) Value #1<br>(3) Value #2<br>(4) Value #3 | `{Any}`<br>These values may be used by the custom attribute to whatever fits their purpose.

Whenever a custom attribute's trigger is triggered an attribute usage is tracked.

**Visits:** Applying causes a visit to the selected game element.

**Redirections:** Applyings may be redirected.

**Success:** Besides obstructions, Applying is almost always successful. An unapplying (remove applying) may fail when not a single attribute can be removed, a change applying may fail when the attribute that is to be changed cannot be found.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | First target of the ability

**Subtypes:** 

There are 3 subtypes for applying. Add applying adds an attribute through a reference to a passive attribute. Remove applying and change applying, remove/change an already applied attribute through a reference to an active attribute.  
When add applying up to three attribute parameters may be optionally specified. These will be stored into Value #1, Value #2, and Value #3, respectively. When setting these values initially, only Variable String input is supported, however when using change applying any type is supported and parsed.

Subtype | Syntax
--- | ---
add | `Add {Attribute} to {Actor} {(Duration?)}`<br>`Add {Attribute} to {Actor} {(Duration?)} ({VariableString})`<br>`Add {Attribute} to {Actor} {(Duration?)} ({VariableString}, {VariableString})`<br>`Add {Attribute} to {Actor} {(Duration?)} ({VariableString}, {VariableString}, {VariableString})`
remove | `Remove {Active Attribute} from {Actor}`
change/change_parsed | ``Change {Active Attribute} value `[1\|2\|3]` to {Any} for {Actor}``<br>``Change {Active Attribute} value `[1\|2\|3]` to {Any} for {Actor}``

**Triggers:** An add applying triggers a `Starting` trigger for the applied attribute. A remove applying triggers a `On Removal` trigger for the removed attribute.

### Redirecting

**Summary:** Redirecting is an Attribute Applier type action that applies a redirection attribute to another player. Redirection attributes can change the target of other abilities. When an ability that is affected by redirections (which, by default, is all) is used on a player that has a redirection attribute applied, the target is changed to whomever the redirection points to.

**Attributes:** Redirecting creates redirection attribute. Each redirection attribute stores the target to which abilities would be redirected to, a selector that filters which players are affected and an ability type, subtype or category that limits the redirection to certain abilities.

Property | Value
--- | ---
Attribute Type | `redirection`
(1) Target | `{Player}`
(2) Affected Sources | `{Player}`
(3) Affected Abilities | `{Ability Type\|Ability Subtype\|Ability Category}`

Both Target and Affected Sources are stored as selectors and only resolved when a redirection is being evaluated. This means that when using a selector, the selected players may differ from when the attribute is getting created to when it is actually getting evaluated.

Whenever a redirection attribute redirects an ability, an attribute usage is tracked.

The whispering ability type cannot be affected by redirections.

**Visits:** Applying a redirection does not cause a visit.

**Redirections:** Applying a redirection cannot be redirected.

**Success:** Redirecting is always successful, unless the abilities attempt to target several players.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | Target of the ability

**Subtypes:**

There are no subtypes of redirecting, but there are a few syntax variants.

Syntax | Explanation
--- | ---
`Redirect {Ability Type\|Ability Subtype\|Ability Category} to {Player} {(Duration?)}` | Simplified syntax, affected sources defaults to `@All`.
`Redirect {Ability Type\|Ability Subtype\|Ability Category} from {Player} to {Player} {(Duration?)}` | Full syntax

**Triggers:** When an ability is redirected, a `On Redirect` trigger is triggered for the player that has the redirection applied.

### Manipulating

**Summary:** Manipulating is an Attribute Applier type action that applies a manipulation attribute. The ability is also known as 'Vote Manipulation' as manipulation attributes affect how many votes an affected player has on certain polls. Vote manipulations can be absolute or relative. When evaluating the amount of votes a player has, all manipulations are handeled in order of application, with oldest first. Absolute manipulations set the vote value to their number, while relative manipulations increment/decrement the number. This means, that if an absolute manipulation is applied before a relative manipulation, the latter will still be applied, but relative to the absolute value from the absolute manipulation.

**Attributes:** Manipulating creates manipulation attributes. Manipulation attributes store whether they are an absolute/relative manipulation and a subtype, which can be either public, private or special. Public and private manipulations affect only public/private polls, respectively. Special manipulations also only affect public polls, but are applied to the vote value after all public manipulations have been evaluated.

Property | Value
--- | ---
Attribute Type | `manipulation`
(1) Manipulation Type | `[absolute\|relaitve]`
(2) Manipulation Subtype | `[public\|special\|private]`
(3) Value | `<Number>`

No attribute usages are tracked for manipulation attributes.

**Visits:** Manipulating causes a visit to the selected player.

**Redirections:** The target of a Manipulating may be redirected.

**Success:** Besides obstructions, Manipulating is always successful.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | First target of the ability

**Subtypes:** 

Subtype | Syntax
--- | ---
absolute | `Manipulate {Player}'s [public\|special public\|private] voting power to {Number} {(Duration?)}`
relative | `Manipulate {Player}'s [public\|special public\|private] voting power by {Number} {(Duration?)}`

**Triggers:** There are no triggers associated with manipulating.



### Ability Template

**Summary:** 

**Attributes:** There are no attribute interactions specific to targeting.

**Visits:** 

**Redirections:** The target of a ABILITY may be redirected.

**Success:** Besides obstructions, ABILITY is always successful.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | First target of the ability

**Subtypes:** 

Subtype | Syntax | Feedback
--- | --- | ---

**Triggers:** There are no triggers associated with targeting.





 
## Game Element Formats

### Roles Format

Roles are the main holders of WWRF information. Their formalization is formatted as follows:

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
