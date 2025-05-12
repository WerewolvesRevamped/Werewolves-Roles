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
- [Game Element Formats](#game-element-formats)
  - [Roles Format](#roles-format)
  - [Teams Format](#teams-format)
  - [Groups Format](#groups-format)
  - [Abilities Format](#abilities-format)


## Introduction
Werewolves Revamped is automated using "formalization" - i.e., all roles and similar are written in a formal way in a custom language (WWRF) which can be interpreted by the bot.

WWRF is designed with a "Trigger > Condition > Action" model: there are certains events that emit a "trigger" (a phase starting, a player being killed, etc) and there are game "actions" which change the game state and my emit triggers themselves (e.g. killing a player, creating a group, manipulating voting values). All elements in WWR are designed by combining triggers and actions (and optionally including conditions that limit the execution of the action to certain cases). Actions are differentiated between prompting actions, which prompt the player for an input and are only executed once such an input is provided, and automatic actions ,which are executed without player interaction (and potentially even without the players knowledge)

For example, the Fortune Teller has a prompting investigation action which is triggered by the night starting. The Wolf Cub has an automatic poll creating action which is triggered by their death.

## Game Elements

Game Elements are all the components that make up the game. This includes players, roles, groups, polls, teams and more.

We differentiate between active and passive game elements. Active elements can be modified, while passive elements remain static. Passive elements are what is defined in this repository, and active elements are instances of a passive element created during the game. Outside a game, only passive elements exist. Some game elements exist in both passive and active form.

For example, the concept of a lynch poll, as defined in this repository,y is a passive game element, whereas one specific lynch poll within a game is an active game element.
Passive game elements store general information about the element (e.g. a role's description), whereas active elements store game data (e.g., a player's date).

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

∗ Roles can be instantiated in two ways: when they are assigned to a player, the player sort of becomes the instantiated version of the role, though of course there is more data on the player. Alternatively if a role is assigned as an extra role mid-game it is instantiated as a role type attribute.  
⁑ While Teams are both active and passive, they are not instantiated as there can only ever be one of each team. Instead the team's active and passive data is stored in the same element.  
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
⁑ Locations are a special type which unifies several other types into a single type. The following types can be interpreted as a location: group, base location, active extra role, player, attribute.  
⁂ Results are a special type which is returned after executing an ability, depending on context different types can be extracted from it. It always stores at least a success type (for if the ability succeeded) and an info type (for the ability feedback) and depending on the ability potentially other abilities.  
⁑⁑ Info is a special type which is a text containing several selectors. All selectors contained within the info text must either be annotated or support run-time annotation. The info type is used when a text output is generated. As part of the text output process all selectors contained within the info type are evaluated and converted to text.  
⁑⁑∗ An Ability Subtype contains the info of the ability type _and_ the subtype, not just the latter.  

Some types additionally support property accesses using the `<Selector>-><Property>`, e.g. `@Target->Role` retrieves a targets role.

Many types default to not just being a single value, but actually a list, though this will often be a list of length one, this is shown in the table above. Types that are list types will return several results if a selector matches several results (though some abilities may choose to only use the first element of the list), while types that are not list types can only return a single value. When using a property access on a list type, the property access is executed on each element in the list and a new list is returned. Lists can be created through a selector which returns several values or by adding several constant values together (e.g. `Citizen`+`Wolf` would be a simple role type list).

For this document, when the syntax of any component requires a user to put a type, this syntax is used to signify the expected type: `{Type}`, where `Type` is replaced with the name of the expected type. When expecting a user to put a text that is not a specific type this format is used: `<UserInput>`, where `UserInput` describes the expected input. When a user must pick one of several possible values the syntax `[Option1|Option2]` is used, though square brackets are used in many other contexts as well.

### Player Type

Player type is one of the most common types and refers to a player of the game (active). This type is also known under `player_attr` (referring to a player when acting through an extra role) or `player_group` (referring to a player when acting through a group).

Selector | Meaning
--- | ---
@Self | The current player (Alternatively, the player who an attribute is applied on when using from an attribute).
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
Count | Returns the amount of roles.

Roles support a few property accesses:

Selector | Meaning
--- | ---
Category | The role's category.
Class | The role's class.
Team | The role's team.
Type | The role's type.
Players | All players that have this role.
Count | Returns the amount of roles.

### Active Extra Role Type

Active Extra role type refers to a special subtype of attributes: role type attributes. These are rarely treated as actual attributes however. Active Extra Roles are created through grantings and show up as an additional secret channel for said role. For example, when a player is elected as a Mayor they receive Mayor as an active extra role.

Selector | Meaning
--- | ---
@ThisAttr | Refers to the current active extra role.
``​`<RoleName>`​`` | Refers to an active extra role name with the specified name which was __created by the current game element__. 

### Group Type

Group types refer to the active instance of a group. Currently there is only the active group type, though when a group name is passed and no active group exists for this group yet, some contexts may automatically create an active instance of the group so the group type is able to return an active instance.

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
Count | Returns the amount of groups.

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
Count | Returns the amount of teams.

### Location Type

Location type is a special type that combines groups, players, base locations, active extra roles and attributes into one. While the other types exists also outside the location type, base locations are only available within this type.

Selector | Type | Meaning
--- | --- | ---
`#<LocationName>` | Base Location | Name of a base location, such as town square.
`#<GroupName>` | Group | Name of a group.
``​`<RoleName>`​`` | Active Extra Role | Refers to an active extra role name with the specified name which was __created by the current game element__. 
@Self | Player/Group | Refers to the current player (for players, active extra roles), to the player the current attribute is attached to (for attributes) or to the current group (for groups).
@AttackLocation | Player/Group | Either `@Attacker` or `@AttackSource` depending on which can be resolved to a location.
Any Player Selector | Player | If none of the previous selectors matches the specified selector, the value is treated as a player type - all player selectors are valid.

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

Result type is a special type that encompasses several other values. In some contexts it will automatically be cast to one of its values, but generally in most contexts it is necessary to use property access to retrieve one of the values making up the result. Results are returned by an ability when storing its result through a Process ability.

Selector | Meaning
--- | ---
@Result[1-7] | Refers to the result of a processed ability.
@ActionResult | Refers to the result of an action in `On Action` and variants.

Results support a couple of property accesses, however not all properties are present in each result:

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
Count | Returns the amount of results.

### Info Type

Info type is a special type - it is plain text including selectors. The selectors are parsed into text if possible, for example: ``​`Your current target is @Target`​`` would inform a player of their current target. For selectors to be parsed into text correctly they must be surrounded by spaces in both directions so that they can correctly be identified.

Selector | Meaning
--- | ---
@ActionFeedback | The feedback of a processed ability.
%PartialRoleList% | Refers to a role list info text stored as host information.
``​`<Info>`​`` | Normal info, potentially containing other selectors.

Each type uses a different method to get parsed to text - for discord elements a respective discord reference is generated (ping, channel link) while other types are usually just their name in title case.

### Ability Type Type

Ability Type type is a type that represents an ability type. Ability types have formal names which may vary from their common names (e.g. `Investigating`).

Selector | Meaning
--- | ---
@VisitSubtype | The ability type of a visit in `On Visited`.
``​`<AbilityType>`​`` | The name of an ability type.

The following ability types exist: killing, investigating, targeting, disguising, protecting, applying, redirecting, manipulating, whispering, joining, granting, loyalty, obstructing, poll, announcement, changing, choices, ascend, descend, disband, counting, reset, cancel, feedback, success, failure, log, process_evaluate, abilities, emit, storing, displaying.

### Ability Subtype Type

Ability Subtype type is a type that represents the exact ability subtype. This is made up as a combination of the ability subtype and the ability type (e.g. `Attack Killing`), with the subtype coming first followed by the type. Subtype that are comprised of multiple words must have their spaces replaced by `-`'s (e.g. `True Kill Killing`).

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

### Ability Category Type

Ability Category type is a type for categories of abilities, but currently only supports two values: ``​`all`​`` (which matches all abilities) and ``​`non-killing abilities`​`` (which matches all abilities besides killing).

Selector | Meaning
--- | ---
@VisitType | The ability type of a visit in `On Visited`.
@ActionAbilityType | The ability type of an ability in `On Action`.
``​`<AbilityType>`​`` | The name of an ability type.

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

Active Attribute type is the counterpart to the attribute type which refers to active attribute instances. To be able to correctly resolve some types of active attribute selectors as "on element" is required (the element on which the attribute is applied). What is passed here is based on the ability. There are various variants of advanced active attribute selectors, so they are all listed in the same taböle.

Selector | Meaning
--- | ---
@ThisAttr | Refers to the current attribute.
``​`<AttributeName>`​`` | Searches for a custom attribute 
``​`<AttributeName>:Self`​`` | Searches for a custom attribute, applied by the current player.
``​`<AttributeName>:<SourceReference>`​`` | Searches for a custom attribute, applied by a certain source specified by its reference. This is not very usable most of the time as most source references are different each game, however team names can be used. See [sources](#sources).
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
``​`<String>`​`` | The text.
%String[any]% | Refers to a string stored as host information. Replace [any] with any text or nothing.

### Null Type

Null type is a special type that should not be used manually. When inferring the type of a target at runtime, but no target is set, a null type list with zero elements is instead returned. This is to ensure that abilities relying on a target gracefully fail instead of erroring. Null type always takes the form of an empty list.

Technically null types also support property accesses, however each property access will simply return an empty list as well.

### Phase Type

Phase type represents a phase and is made up of the type of phase (Day or Night) and the phase's number. 

Value | Meaning
--- | ---
Day \<Number\> | A day phase.
Night \<Number\> | A night phase.

### Actor Pseudo-Type

Actor pseudo-type is not an actual type, but is sometimes the expected input. In that case the input can take the form of any acting game element. The following types may be specified when an actor type is expected: player, group, team, active attribute, active extra role.

### Any Pseudo-Type

Any pseudo-type is a pseudo-type that is sometimes the expected input. In that case _any_ type may be specified.

### Variables

Variables aren't directly a type, but are used in some contexts. When a variable is evaluated it returns a value dependent on the current game state.

Variable | Meaning
--- | ---
$total | Amount of players in the game.
$living | Amount of living players.
$phase | The current phase as a number.

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
On Defense<br>On Active Defense<br>On Passive Defense<br>On Partial Defense<br>On Recruitment Defense<br>On Absence Defense | Triggers when one of the current player's defenses of the specified (or any) type is used. Performs a Source Name match - this requires the defense to have been created by the same role the trigger is in. | @Attacker<br>@DeathType<br>@AttackSource
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

## Trigger Parameters

Triggers can be further limited/extended with additional paramaters. There are two primary types of trigger parameters: restrictions and scaling. The former restricts the execution of all abilities attached to the trigger to certain situations, whereas the latter changes the amount of times the abilities attached to the trigger are executed.

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

Other trigger paramaters are marked by curly brackets: {}. Curly brackets in this section do __not__ signfiy type markers like in the rest of the document. When using several other parameters their curly bracket blocks are combined, e.g. `{Forced, Direct}`.

To make a prompting trigger forced, a Trigger Compulsion cane set by specifying `{Forced}`. By default a forced trigger randomly selects a target where necessary. If a forced trigger should select a specific target use `{Forced: <Default Target>}` instead.

To make a trigger unaffected by redirections, specify `{Direct}`.

To make a trigger no perform a vist (which also makes it unaffected by redirections!), specify `{Visitless}`.

### Prompt Overwrites

Prompt overwrites are marked by vertical brackets: ||.

By default a prompting trigger will generate a prompt message code name and look it up in the prompt message file. To overwrite the default behaivor, you can specify the name of a custom prompt. For example, `|custom.prompt.1|` would look up the prompt text for `custom.prompt.1`.

To additionally mark a prompt as silent (which skips the ping), you may specify `silent:` before the prompt name, e.g. `|silent:custom.prompt.1|`.

## Conditions

Conditions are a part of the formalization that can be used in several places (Restrictions and Process/Evaluate) to limit execution of abilities to certain situations.

The following conditions exist:

Name | Syntax | Explanation
--- | --- | ---
Equality | `{Any} is {Any}` | Compares two values with each other, passes if equal. When comparing lists, equality is only checked for the first value of each list (e.g. [a,b] = [a,c] will pass).
Less/Greater Than | `{Number} [>\|<] {Number}` | Compares two numbers with each other, passes if the first value is greater/less than the second. When comparing lists, the first value of each list is used.
No Equality | `{Any} is not {Any}` | Compares two values with each other, passes if different.
Existence | `{Any} exists` | Checks if a specified selector evaluates to at least one element.
Attribute | `{Actor} has {Attribute}` | Checks if a specifed actor has a specified attribute.
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
• Role Type Attribute: Role type attributes are a type of attribute which represent an additional role applied to a player. For most purposes this additional role acts as a normal role would, making it an acting attribute.  
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

**Success:** Besides obstructions targeting is always successful, unless there is a syntax error or the target is invalid. Furthermore, the ability will fail when attempting to target more than one value.

**Feedback:**

Name | Type | Value
--- | --- | ---
Message | String | -
Success | Success | -
Target | Player | The selected target (`target` subtype)

**Subtypes:** 

Subtype | Syntax | Feedback
--- | --- | ---
Target | `Target {*} ({**})` | Sets a palyers target, specify a selector (`{*}`) and additionally annotate the type (`{**}`), the latter of which must be one of the following values: Player, Dead, Role, Attribute, Category, Full Category, Boolean, Option. The former must be a selector of matching type.
Untarget | `Untarget` | Removes the player's target.

**Triggers:** There are no triggers associated with targeting.

### Ability Template

**Summary:** 

**Attributes:** 

**Visits:** 

**Redirections:** 

**Success:** 

**Feedback:**

**Subtypes:** 

**Triggers:** 
 
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
